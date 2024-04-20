<template>
    <div>
        <h1>Quartos Fiscais</h1>
        <div class="container">
            <template v-for="quarter of quarters" v-bind:key="quarter.name">
                <div class="quarter" v-bind:class="{ 'currentMonth': quarter.name == currentQuarterName }">
                    <h2>{{ quarter.name }}</h2>
                    <div class="months">
                        <div v-for="month of quarter.months" v-bind:key="month.value">
                            <span v-bind:class="{ 'currentMonthText': month.value === currentMonth }">{{ month.name
                                }}</span>
                        </div>
                    </div>
                </div>
            </template>
        </div>
    </div>
</template>

<script>
export default {
    name: 'FiscalQuarters',
    data() {
        return {
            msg: 'Fiscal Quartes',
            quarters: [
                {
                    name: 'Q1', months: [
                        { name: 'Janeiro', value: 1 },
                        { name: 'Fevereiro', value: 2 },
                        { name: 'Março', value: 3 }
                    ]
                },
                {
                    name: 'Q2', months: [
                        { name: 'Abril', value: 4 },
                        { name: 'Maio', value: 5 },
                        { name: 'Junho', value: 6 }
                    ]
                },
                {
                    name: 'Q3', months: [
                        { name: 'Julho', value: 7 },
                        { name: 'Agosto', value: 8 },
                        { name: 'Setembro', value: 9 }
                    ]
                },
                {
                    name: 'Q4', months: [
                        { name: 'Outubro', value: 10 },
                        { name: 'Novembro', value: 11 },
                        { name: 'Dezembro', value: 12 }
                    ]
                }
            ],
            currentMonth: new Date().getMonth() + 1
        }
    },
    computed: {
        currentQuarterName() {
            return this.quarters.find(quarter => {
                return quarter.months.some(month => month.value === this.currentMonth)
            }).name
        }
    }
}
</script>

<style scoped>
h2 {
    margin-top: 0;
    margin-bottom: 10px;
}

.quarter {
    flex: 1;
    min-width: 80px;
    border: 1px solid #8f99a4;
    padding: 10px;
    margin: 10px;
    font-size: 20px;
    background-color: #a4adb5;
}

.currentMonth {
    background-color: #2c3e50;
    color: #e8eaec;
}

.currentMonthText {
    color: #adc178;
    font-weight: bold;
}
</style>