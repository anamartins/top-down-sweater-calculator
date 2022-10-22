<template>
    <div class="number-input">
        <div class="label">{{label}}</div>
        <input class="input" type="number" :value="value" @input="onInput"/>
        <div class="unit">{{unit}}</div>
    </div>
</template>

<script>
    export default {
        props:{
            label: {
                type: String,
                required: true
            },
            unit:{
                type: String,
                default: 'cm'
            },
            value:{
                type: Number,
                default: 0
            }
        },
        methods:{
            onInput(event){
                const newValue = parseInt(event.target.value);
                this.$emit('update:value', newValue);
            }
        }
    }
</script>

<style lang="scss" scoped>
    .number-input{
        position: relative;
        display: flex;
        flex-flow: row wrap;
        align-items: baseline;
        width: 20%;
        min-width: 245px; 
        margin: 5px 0;    
    }
    .input{
        position: relative;
        box-sizing: border-box;
        width: 20%;
        min-width: 55px;
        max-width: 100px;
        margin: 0 5px 0 0;
        padding: 5px 5px 1px 5px;
        border: none;
        border-bottom: 2px solid var(--graphite);
        font-size: 1.5rem;
        text-align: center;
    }
    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
        -webkit-appearance: none;
        margin: 0;
    }
    .label{
        position: relative;
        width: 100%;
    }
    .unit{
        position: relative;
        width: 40%;
        font-size: 1.5rem;
    }
    

</style>
