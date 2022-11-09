<template>
    <div class="wrapWithdrawBlock">
        <div class="availableWrap">
            <h3 class="bold">
                {{
                    $te(`shortText.${helperObject.camelize(provider.name)}`)
                        ? $t(`shortText.${helperObject.camelize(provider.name)}`)
                        : provider.name
                }}
            </h3>
            <template v-if="provider.hasOwnProperty('extra') && provider.extra.hasOwnProperty('text') && provider.extra.text">
                <div v-html="provider.extra.text"></div>
                <br />
            </template>
            <div class="customFileInput flexCenter">
                <span class="icon flexCenter">
                    <svg width="25" height="25" viewBox="0 0 37 37" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <g clip-path="url(#clip0)">
                            <path
                                fill-rule="evenodd"
                                clip-rule="evenodd"
                                d="M36.1729 13.0432V3.56348H0.172852V13.0816C0.172852 16.3822 2.82104 19.0304 6.12168 19.0304V9.47392H30.224V18.992C33.5247 18.992 36.1729 16.3439 36.1729 13.0432ZM18.1729 28.0496C17.2517 28.0496 16.5225 27.3204 16.5225 26.3993C16.5225 25.4782 17.2901 24.749 18.1729 24.749C19.094 24.749 19.8232 25.4782 19.8232 26.3993C19.8232 27.3204 19.094 28.0496 18.1729 28.0496ZM25.1196 27.8961C25.2731 27.8961 25.465 27.8961 25.6185 27.9345V11.6616H27.9596V30.7362C27.9596 32.233 26.7315 33.4611 25.2347 33.4611H11.111C9.61422 33.4611 8.38607 32.233 8.38607 30.7362V11.6616H10.7272V27.9345C10.8807 27.8961 11.0342 27.8961 11.2261 27.8961H11.2262C12.7613 27.8961 13.9895 29.1242 13.9895 30.6594C13.9895 30.8513 13.9895 31.0048 13.9511 31.1584H22.3946C22.3562 31.0048 22.3562 30.813 22.3562 30.6594V30.6594C22.3562 29.1242 23.5844 27.8961 25.1195 27.8961H25.1196ZM12.224 17.6871C12.224 20.9878 14.9106 23.6743 18.2112 23.6743C21.5119 23.6743 24.1984 20.9878 24.1984 17.6871C24.1984 14.3865 21.5119 11.6999 18.2112 11.6999C14.9106 11.6999 12.224 14.3865 12.224 17.6871ZM18.1733 14.4877L20.4028 17.1694C20.5577 17.3557 20.4368 17.6546 20.2065 17.6546H20.0652H19.1875V19.8988C19.1875 20.0857 19.049 20.2373 18.878 20.2373H17.5033C17.3324 20.2373 17.1939 20.0857 17.1939 19.8988V17.6546H16.2798H16.1385C15.9082 17.6546 15.7873 17.3557 15.9422 17.1694L18.1733 14.4877Z"
                            ></path>
                        </g>
                    </svg>
                </span>
                <span class="available">
                    <span class="secondColor">{{ $t('shortText.available_withdraw') }}: </span>
                    <span class="bold">{{ getBalance.withdrawable }}</span>
                </span>
                <span class="currSymbol">{{ $store.state.currencySymbol }}</span>
            </div>
        </div>
        <br />

        <div class="amountWrap">
            <div>
                {{ $t('shortText.user') }}
                <span class="danger-color">*</span>
            </div>
            <input v-model="user" type="text" :placeholder="$t('shortText.user')" readonly/>
        </div>
        <br />

        <div class="amountWrap">
            <div>
                {{ $t('shortText.email') }}
                <span class="danger-color">*</span>
            </div>
            <input v-model="email" type="text" :placeholder="$t('shortText.email')"/>
        </div>
        <br />

        <div class="amountWrap">
            <div>
                {{ $t('shortText.withdrawal_amount') }}
                <span class="danger-color">*</span>
            </div>
            <InputPaymentAmount
                v-model="withdrawalAmount"
                :min="provider.min_limit"
                :max="provider.max_limit"
                :class="{inputError: amountErr || amountEmpty || amountMinError || amountMaxError}"
                @keyup="validation()"
            >
            </InputPaymentAmount>
            <span class="currSymbol">{{ $store.state.currencySymbol }}</span>
        </div>
        <br />

        <div class="amountWrap">
            <div>
                {{ $t('shortText.manual_cvu') }}
                <span class="danger-color">*</span>
            </div>
            <input v-model="cvu" type="text" :placeholder="$t('shortText.manual_cvu')"/>
        </div>
        <br />

        <div class="amountWrap">
            <div>
                {{ $t('shortText.manual_name_cvu') }}
                <span class="danger-color">*</span>
            </div>
            <input v-model="cvuName" type="text" :placeholder="$t('shortText.manual_name_cvu')"/>
        </div>
        <br />

        <div class="amountWrap">
            <div>
                {{ $t('shortText.manual_cbu') }}
            </div>
            <input v-model="cbu" type="text" :placeholder="$t('shortText.manual_cbu')"/>
        </div>
        <br />

        <div class="amountWrap">
            <div>
                {{ $t('shortText.manual_cbu_name') }}
            </div>
            <input v-model="cbuName" type="text" :placeholder="$t('shortText.manual_cbu_name')"/>
        </div>
        <br />

        <div v-if="amountErr || amountMinError || amountMaxError" class="error_msg amountErr">
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
            <span v-if="amountMaxError">{{ $t('shortText.amountMaxError') }}</span>
            <span v-else-if="amountMinError">{{
                $t('shortText.amountMinErrorFull', { min: provider.min_limit })
            }}</span>
            <span v-else-if="amountErr">{{ $t('shortText.available_withdrawal_exceeded') }}</span>
        </div>
        <div v-else-if="amountEmpty" class="error_msg amountErr">
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
            <span class="">{{ $t('shortText.indicate_amount') }}</span>
        </div>
        <div v-else-if="errorMsg" class="error_msg amountErr">
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
            <span class="">{{ errorMsg }}</span>
        </div>
        <div v-else-if="innerError" class="error_msg amountErr">
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
            <span class="">{{ innerError }}</span>
        </div>

        <!--error block-->
        <div class="btnWrap">
            <button
                :disabled="amountErr || amountMinError || amountMaxError || (amountEmpty && formSubmited)"
                class="mainBtn"
                @click="make"
            >
                {{ $t('shortText.withdraw') }}
            </button>
            <button class="mainBtn btnCancel" @click="$store.commit('setWithdrawModal')">
                {{ $t('shortText.cancel') }}
            </button>
        </div>
    </div>
</template>
<style>
.wrapWithdrawBlock .amountWrap input {
    max-width: 100%;
}

.wrapWithdrawBlock .customFileInput {
    max-width: 100%;
}

.withoutArrows::-webkit-inner-spin-button,
.withoutArrows::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
}
</style>

<script>
import { mapGetters } from 'vuex';
import Helper from 'basePath/modules/helpers';
import InputPaymentAmount from 'basePath/src/components/profile/Payments/InputPaymentAmount.vue';

export default {
    name: 'WithdrawalManualAttachment',
    components: {InputPaymentAmount},
    props: ['provider', 'error'],
    computed: {
        ...mapGetters(['getBalance']),
    },
    data() {
        return {
            helperObject: Helper,
            innerError: null,
            errorMsg: '',
            amountErr: false,
            amountEmpty: false,
            amountMinError: false,
            amountMaxError: false,
            addressError: false,
            formSubmited: false,
            withdrawalAmount: '',
            userData: {},
            cbuName: null,
            cvuName: null,
            user: null,
            email: null,
            cbu: null,
            cvu: null,
        };
    },
    watch: {
        error(err) {
            this.errorMsg = err;
        },
    },
    created() {
        this.email = this.$store.state.email ? this.$store.state.email : null;
        this.user = this.$store.username ? this.$store.state.username : this.$store.state.user;
        this.formVars = ['withdrawalAmount', 'cvu', 'cvuName', 'email'];
    },
    mounted() {
        this.$watch(
            (vm) => {
                let array = [];
                this.formVars.forEach((item) => {
                    array.push(vm[item]);
                });
                return array;
            },
            () => {
                if (this.innerError) {
                    this.innerError = null;
                }
            },
            {
                immediate: true,
                deep: true,
            }
        );
    },
    methods: {
        validateForm() {
            let status = 0;
            this.formVars.forEach((item) => {
                if (!this[item]) {
                    this.innerError = this.$t('shortText.deposit_fill_all');
                    status = 1;
                }
            });

            if (status > 0) {
                return false;
            }

            return true;
        },
        translateAddress() {
            return this.$t('shortText.papara_account_number');
        },
        validation() {
            //ERROR BLOCK
            //clear previous errors
            // if (this.formSubmited) {
            this.addressError = false;
            this.amountEmpty = false;
            this.errorMsg = '';
            //clear previous errors
            //to do validations,it depends on currency
            this.amountMinError = this.withdrawalAmount && this.withdrawalAmount < this.provider.min_limit;
            this.amountMaxError = this.withdrawalAmount > this.getBalance.withdrawable;
            this.amountErr = this.withdrawalAmount > this.provider.max_limit;

            if (this.amountErr || this.amountMinError || this.amountMaxError || this.addressError) {
                return false;
            } else {
                return true;
            }
            // }
            //ERROR BLOCK
        },
        make() {
            if (!this.validateForm()) {
                return false;
            }

            //to do validate
            this.amountEmpty = false;
            this.formSubmited = true;
            //emmit event with userData variable
            if (!this.amountErr) {
                //error block
                if (!this.withdrawalAmount) {
                    this.amountEmpty = true;
                    return false;
                }

                if (!this.validation()) {
                    return false;
                }

                this.formSubmited = false;
                //error block

                this.userData.amount = this.withdrawalAmount;
                this.userData.email = this.email;
                this.userData.user = this.user;
                this.userData.cbu_name = this.cbuName;
                this.userData.cvu_name = this.cvuName;
                this.userData.cbu = this.cbu;
                this.userData.cvu = this.cvu;
                this.userData.return_url = window.location.origin + this.$router.resolve({ name: 'user-bank' }).href;
                this.$emit('make-withdrawal', this.userData);
            }
        },
    },
};
</script>
