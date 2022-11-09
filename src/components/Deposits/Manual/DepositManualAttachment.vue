<template>
    <div>
        <h3 class="bold">
            {{
                $te(`shortText.${helperObject.camelize(provider.name)}`)
                    ? $t(`shortText.${helperObject.camelize(provider.name)}`)
                    : provider.name
            }}
        </h3>

        <div class="depSelected">
            <DepositNotification />
            <div v-html="provider.extra.text"></div>
            <br />
            <br />
            <div class="amountWrap generateInput">
                <div>
                    {{ $t('shortText.manual_cbu') }}
                    <span class="danger-color">*</span>
                </div>
                <input
                    v-model="cbu"
                    type="text"
                    :placeholder="$t('shortText.manual_cbu')"
                    disabled
                />
                <button
                    class="btn btn-transparent"
                    :data-tooltip="$t('shortText.copy')"
                    @click="messageCopy();copyToClipboard(cbu);"
                >
                    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <path
                            d="M22.2227 0H8.95628C8.01078 0 7.24163 0.7692 7.24163 1.71464V5.73903C7.60607 5.73903 14.7583 5.73903 15.0439 5.73903C16.8525 5.73903 18.3238 7.21038 18.3238 9.01894C18.3238 9.3575 18.3238 16.6098 18.3238 16.6958H22.2227C23.1681 16.6958 23.9373 15.9266 23.9373 14.9811V1.71464C23.9373 0.7692 23.1681 0 22.2227 0Z"
                            fill="#3D4FDE"
                        />
                        <path
                            d="M15.0438 7.3042H1.77746C0.832019 7.3042 0.062767 8.0734 0.062767 9.0189V22.2853C0.0628192 23.2308 0.831967 24 1.77746 24H15.0438C15.9893 24 16.7585 23.2308 16.7585 22.2853V9.0189C16.7586 8.0734 15.9893 7.3042 15.0438 7.3042Z"
                            fill="#3D4FDE"
                        />
                    </svg>
                </button>
                <div class="copied" :class="{ showCopied: copied }">
                    <input id="copyModal" type="text" value="Copied" readonly />
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
                    {{ $t('shortText.deposit_amount') }} ({{ $store.state.currencySymbol }})
                    <span class="danger-color">*</span>
                </div>
                <InputPaymentAmount
                    v-model="amount"
                    :min="provider.min_limit"
                    :max="provider.max_limit"
                    @keyup="clearAmount()"
                >
                </InputPaymentAmount>
            </div>

            <br />
            <div class="amountWrap">
                <div>
                    {{ $t('shortText.holder') }}
                    <span class="danger-color">*</span>
                </div>
                <input v-model="holder" type="text" :placeholder="$t('shortText.holder')" />
            </div>
            <br />

            <div class="amountWrap">
                <div>
                    {{ $t('shortText.manual_attachment_file') }}
                    <span class="danger-color">*</span>
                </div>
                <input
                    id="added_file"
                    accept=".jpg, .jpeg"
                    type="file"
                    @input="file = $event.target.files[0]"
                />
                <label for="added_file" class="mainBtn">{{ file ? file.name : $t('shortText.add_file') }}</label>
            </div>
            <br />

            <br />
            <div v-if="errorHost || innerError" class="error_msg amountErr">
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
                <span class="">{{ errorHost || innerError }}</span>
            </div>
            <div class="btnWrap">
                <button :disabled="isButtonDisabled" class="mainBtn" @click="make()">
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
import { mapGetters } from 'vuex';
import Helper from 'basePath/modules/helpers';
import DepositNotification from 'basePath/src/components/spans/DepositNotification.vue';
import InputPaymentAmount from 'basePath/src/components/profile/Payments/InputPaymentAmount.vue';

export default {
    name: 'DepositManualAttachment',
    components: { DepositNotification, InputPaymentAmount },
    props: ['error', 'provider', 'isButtonDisabled'],
    data() {
        return {
            userData: {},
            helperObject: Helper,
            amount: null,
            errorHost: null,
            innerError: null,
            holder: null,
            email: null,
            file: null,
            user: null,
            cbu: null,
            copied: false,
            copyClass: {
                copied: true,
                showCopied: this.copied,
            },
        };
    },
    created() {
        this.cbu = this.provider.extra['CBU'];
        this.email = this.$store.state.email ? this.$store.state.email : null;
        this.user = this.$store.username ? this.$store.state.username : this.$store.state.user;
        this.formVars = ['file', 'email', 'holder', 'amount'];
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
                    this.errorHost = null;
                }
            },
            {
                immediate: true,
                deep: true,
            }
        );
    },
    computed: {
        ...mapGetters(['getUser', 'getLanguage']),
        countryPhoneCodes() {
            return [...new Set(this.countries.map((el) => el.phone_code))];
        },
    },
    watch: {
        error(err) {
            this.errorHost = err;
        },
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
        make() {
            //to do validate
            if (!this.validateForm()) {
                return false;
            }

            let formData = new FormData();
            formData.append('file', this.file);
            formData.append('holder', this.holder);
            formData.append('email', this.email);
            formData.append('amount', this.amount);
            formData.append('user', this.user);
            formData.append('cbu', this.cbu);
            this.userData = formData;

            //emmit event with userData variable
            this.$emit('make-deposit', formData);
        },
        handleOpenChange() {
            this.showTimePanel = false;
        },
        toggleTimePanel() {
            this.showTimePanel = !this.showTimePanel;
        },
        clearAmount() {
            this.innerError = null;
            this.errorHost = null;
        },
        messageCopy() {
            this.copied = !this.copied;
            setTimeout(() => {
                this.copied = !this.copied;
            }, 1000);
        },
    },
};
</script>

<style>
.copied {
    margin: 0px;
    display: inline-block;
    position: absolute;
    top: -20px;
    right: 5px;
    opacity: 0;
    transform: translateY(-10px);
    transition: opacity 0.3s, transform 0.5s;
}
.copied.showCopied {
    opacity: 1;
    transform: translateY(0px);
}

#copyModal {
    width: 50%;
    margin: 0px;
    padding: 0px;
    text-align: center;
}
</style>