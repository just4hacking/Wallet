<template lang="html">
  <div>
    <b-modal
      ref="modal"
      :title="$t('nftManager.add-custom')"
      hide-footer
      class="bootstrap-modal nopadding max-height-1"
      centered
      @hidden="resetCompState"
    >
      <form class="tokens-modal-body" @keydown.enter.prevent>
        <div>
          <input
            v-model="contractAddress"
            v-validate="'required'"
            :class="[
              'custom-input-text-1',
              contractAddress !== '' && !validAddress ? 'invalid-address' : ''
            ]"
            :placeholder="$t('nftManager.ph-token-addr')"
            name="Address"
            type="text"
          />
          <span
            v-show="contractAddress !== '' && !validAddress"
            class="error-message"
          >
            {{ $t('nftManager.invalid-addr') }}
          </span>
          <span v-show="nonStandardMessage">
            {{ $t('nftManager.no-method-no-token', { token: tokenSymbol }) }}
          </span>
          <input
            v-model="tokenSymbol"
            :placeholder="$t('nftManager.name')"
            name="Name"
            type="text"
            class="custom-input-text-1"
          />
        </div>
        <div class="button-block">
          <button
            :class="[
              allFieldsValid ? '' : 'disabled',
              'save-button large-round-button-green-filled clickable'
            ]"
            @click.prevent="addCustom(contractAddress, tokenSymbol)"
          >
            {{ $t('common.save') }}
          </button>
          <interface-bottom-text
            :link-text="$t('common.help-center')"
            :question="$t('common.dont-know')"
            link="https://howto.xinfin.org/XinFinWallet/features"
          />
        </div>
      </form>
    </b-modal>
  </div>
</template>

<script>
import InterfaceBottomText from '@/components/InterfaceBottomText';
import { mapState } from 'vuex';
import { isAddress } from '@/helpers/addressUtils';

export default {
  components: {
    'interface-bottom-text': InterfaceBottomText
  },
  props: {
    addToken: {
      type: Function,
      default: function () {}
    },
    activeAddress: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      contractAddress: '',
      tokenSymbol: '',
      tokenDecimal: '',
      validAddress: false,
      nonStandardMessage: false
    };
  },
  computed: {
    ...mapState('main', ['web3']),
    allFieldsValid() {
      if (!this.validAddress) return false;
      if (this.tokenSymbol === '') return false;
      if (this.errors.has('address') || this.errors.has('symbol')) return false;
      return true;
    }
  },
  watch: {
    contractAddress(newVal) {
      const strippedWhitespace = newVal.toLowerCase().trim();
      const regTest = new RegExp(/[a-zA-Z0-9]/g);
      this.validAddress =
        regTest.test(strippedWhitespace) && isAddress(strippedWhitespace);
      this.toAddress = strippedWhitespace;
      this.contractAddress = strippedWhitespace;
    },
    tokenSymbol(newVal) {
      this.tokenSymbol = newVal;
    }
  },
  methods: {
    resetCompState() {
      this.contractAddress = '';
      this.tokenSymbol = '';
      this.tokenDecimal = '';
      this.validAddress = false;
      this.nonStandardMessage = false;
    },
    async addCustom(address, symbol) {
      this.addToken(address, symbol);
    },
    openCustomModal() {
      this.$refs.customModal.$refs.modal.show();
    }
  }
};
</script>

<style lang="scss" scoped>
@import 'NftCustomAddModal.scss';
</style>
