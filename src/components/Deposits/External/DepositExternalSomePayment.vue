<template>
    <div>
        <h3 class="bold">{{ provider.name }}</h3>
        <DepositNotification />
        <br />
        <div class="depSelected">
            <div v-if="provider.extra_data.image" class="imgSelectWrap">
                <img :src="getHost() + provider.extra_data.image" alt="" />
            </div>
            <p>{{ $t('shortText.payment_page_deposit') }}</p>

            <div class="amountWrap">
                <div>
                    {{ $t('shortText.deposit_amount') }} ({{ $store.state.currencySymbol }})
                    <span class="danger-color">*</span>
                </div>
                <input
                    v-model.number="amount"
                    type="number"
                    class="withoutArrows"
                    placeholder="Amount"
                    @keypress="validateInputForPayment($event)"
                    @keyup="clearAmount()"
                />
            </div>

            <div v-if="innerError || error" class="error_msg amountErr">
                <svg width="16" height="16" viewBox="0 0 32 33" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <g clip-path="url(#clip0)">
                        <path
                            fill-rule="evenodd"
                            clip-rule="evenodd"
                            d="M0.256836 16.5002C0.256836 7.81961 7.31912 0.757324 15.9999 0.757324C24.6806 0.757324 31.7429 7.81945 31.7429 16.5002C31.7429 25.181 24.6806 32.2433 15.9999 32.2433C7.31912 32.2433 0.256836 25.1808 0.256836 16.5002ZM16.0966 5.63592C14.8041 5.63592 13.9645 6.49558 13.9928 7.39687C14.0136 8.05181 14.0886 10.2608 14.2157 11.7161L14.8419 18.7059C14.9418 19.5561 15.0857 20.1818 15.2738 20.5892C15.4411 20.9518 15.7153 21.1504 16.0966 21.1882C16.4779 21.1505 16.7522 20.9518 16.9194 20.5892C17.1074 20.1818 17.2513 19.5561 17.3513 18.7059L17.9776 11.7161C18.1047 10.2608 18.1797 8.05181 18.2004 7.39687C18.2266 6.56315 17.3891 5.63592 16.0966 5.63592ZM18.1713 24.2062C18.1713 25.3539 17.2424 26.2844 16.0965 26.2844C14.9506 26.2844 14.0217 25.3539 14.0217 24.2062C14.0217 23.0585 14.9506 22.128 16.0965 22.128C17.2424 22.128 18.1713 23.0585 18.1713 24.2062Z"
                            fill="#eb4846"
                        ></path>
                    </g>
                </svg>
                <span class="">{{ innerError || error }}</span>
            </div>

            <div class="btnWrap">
                <button class="mainBtn" @click="make()">
                    {{ $t('shortText.continue') }}
                </button>
                <button class="mainBtn btnCancel" @click="$store.commit('setDepositModal')">
                    {{ $t('shortText.cancel') }}
                </button>
            </div>
        </div>
    </div>
</template>
<script>
import DepositNotification from 'basePath/src/components/spans/DepositNotification.vue';

export default {
    name: 'DepositAdumo',
    components: { DepositNotification },
    props: ['error', 'provider'],

    data() {
        return {
            amount: null,
            innerError: null,
            userData: {},
        };
    },
    created() {},
    methods: {
        clearAmount() {
            this.innerError = null;
        },
        validateForm() {
            if (!this.amount) {
                this.innerError = this.$t('shortText.deposit_wrong_amount');
                return false;
            }

            return true;
        },
        make() {
            if (!this.validateForm()) {
                return false;
            }

            this.userData.amount = this.amount;
            this.userData.currency = this.$store.state.currency;
            this.$emit('make-deposit', this.userData);
        },
    },
};
</script>
