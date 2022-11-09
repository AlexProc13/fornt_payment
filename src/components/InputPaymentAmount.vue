<template>
    <input
        type="number"
        inputmode="decimal"
        class="withoutArrows"
        :placeholder="$t('shortText.amount')"
        :min="min"
        :max="max"
        :step="step"
        :readonly="readonly"
        :value="amount"
        @input="$emit('input', Number($event.target.value))"
        @keypress="validateInputForPayment($event)"
        @keyup="keyUpEvent"
    />
</template>
<script>

export default {
    name: 'InputPaymentAmount',
    components: {},
    props: ['min', 'max', 'provider', 'amount', 'readonly'],
    data() {
        return {
            step: this.getStepByCurrency(),
        };
    },
    created() {},
    methods: {
        keyUpEvent() {
            //todo how pass native object
            this.$emit('keyup');
        },
        getStepByCurrency() {
            let numberOfDecimalPlaces = this.$store.state.numberOfDecimals;
            let step = 1;
            for (var i = 1; i <= numberOfDecimalPlaces; i++) {
                step = step / 10;
            }

            if (i > 1) {
                step = step * 100;
            }

            return step;
        }
    },
};
</script>
