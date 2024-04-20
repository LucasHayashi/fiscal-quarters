<template>
    <div>
        <h1>Média de preços nos últimos 5 minutos</h1>
        <h3>Pesquise por um símbolo de criptoativo ( Atualizado a cada 1 segundo )</h3>
        <input type="text" class="search-symbol" v-model="searchSymbol" @input="toUpperCase"
            placeholder="Ex: BTCUSDT" />
        <div v-if="searchSymbol">
            <div class="resultContainer" v-if="searchSymbolResult.length > 0">
                <div class="resultSymbol" v-for="result of searchSymbolResult" v-bind:key="result"
                    v-on:click="addStream(result)">{{ result }}</div>
            </div>
            <div v-else>
                <p>Nenhum símbolo encontrado</p>
            </div>
        </div>
        <div v-if="streamSymbols.length">
            <h2>Streams ativos</h2>
            <div class="container">
                <template v-for="streamSymbol of streamSymbols" v-bind:key="streamSymbol.symbol">
                    <div class="stream" v-on:click="removeStream(streamSymbol.symbol)">
                        <div class="stream-symbol">{{ streamSymbol.symbol }}</div>
                        <div class="stream-symbol-overral">{{ streamSymbol.avgPrice ?? '...aguarde'}}</div>
                    </div>
                </template>
            </div>
        </div>
        <div v-else>
            <p>Adicione um stream!</p>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'MarketStream',
    data() {
        return {
            msg: 'Market Stream',
            websocket: null,
            searchSymbol: '',
            symbols: [],
            streamSymbols: [
                { symbol: 'BTCUSDT', stream: 'btcusdt@avgPrice' },
                { symbol: 'ETHUSDT', stream: 'ethusdt@avgPrice' },
            ],
        }
    },
    methods: {
        toUpperCase() {
            this.searchSymbol = this.searchSymbol.toUpperCase()
        },
        connect() {
            this.websocket = new WebSocket(`wss://stream.binance.com:9443/stream?streams=${this.streamSymbolsParams}`)
            this.websocket.onopen = (event) => {
                console.log('Websocket conectado!', event)
            }
            this.websocket.onmessage = (event) => {
                let data = JSON.parse(event.data);
                let stream = data.stream;
                let avgPrice = +data.data.w;
                avgPrice = avgPrice.toFixed(3);
                let symbolIndex = this.streamSymbols.findIndex(streamSymbol => streamSymbol.stream === stream);
                if (symbolIndex !== -1) {
                    this.streamSymbols[symbolIndex].avgPrice = avgPrice;
                }
            }
        },
        close() {
            this.websocket.close();
        },
        addStream(symbol) {
            this.close();
            this.streamSymbols.push({
                symbol,
                stream: `${symbol.toLowerCase()}@avgPrice`
            })
            this.connect();
            this.searchSymbol = '';
        },
        removeStream(symbol) {
            this.close();
            this.streamSymbols = this.streamSymbols.filter(streamSymbol => streamSymbol.symbol !== symbol);
            this.connect();
        }
    },
    mounted() {
        this.connect();

        axios.get('https://api.binance.com/api/v3/exchangeInfo')
            .then(response => {
                this.symbols = response.data.symbols
                    .filter(symbol => symbol.status === 'TRADING')
                    .map(symbol => symbol.symbol)
            })
    },
    computed: {
        searchSymbolResult() {
            return this.symbols.filter(
                symbol => symbol.includes(this.searchSymbol)
            )
        },
        streamSymbolsParams() {
            return this.streamSymbols.map(symbol => symbol.stream).join('/')
        }
    }
}
</script>

<style scoped>
.search-symbol {
    border: 1px solid #8f99a4;
    padding: 10px;
    margin: 10px;
    font-size: 20px;
    color: #2c3e50;
    outline: none;
}

.resultContainer {
    display: flex;
    justify-content: center;
    margin: 0 auto;
    max-width: 480px;
    flex-wrap: wrap;
}

.resultSymbol {
    margin: 10px;
    padding: 10px;
    font-size: 16px;
    background-color: #2c3e50;
    color: #e8eaec;
    cursor: pointer;
}

.stream {
    flex: 1;
    min-width: 80px;
    padding: 10px;
    margin: 10px;
    text-align: center;
    background-color: #2c3e50;
    color: #e8eaec;
    cursor: pointer;
}

.stream:hover {
    background-color: #1a2a39;
}

.stream-symbol-overral {
    color: #adc178;
    font-weight: bold;
}
</style>