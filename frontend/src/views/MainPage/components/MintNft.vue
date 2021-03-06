<template>
  <div class="create-nft-section">
    <div class="create-nft-title title mb-6">
      Apply Patient Application
    </div>
    <ValidationObserver ref="observer" v-slot="{ passes }">
      <div class="create-nft-body">
        <div class="column">
          <TextField
            v-model="form.patientName"
            :label="'Patient Name'"
            :placeholder="'Patient Name (eg: Andrew Phua)'"
            :rules="rules.patientName"
          />
        </div>

        <div class="column">
          <TextField
            v-model="form.regNo"
            :label="'Patient Registration No.'"
            :placeholder="'Patient Registration No. (eg: P001)'"
            :rules="rules.regNo"
          />
        </div>

        <div class="column">
          <TextField
            v-model="form.itemId"
            :label="'Item ID'"
            :placeholder="'Item ID (For BlockChain Reference)'"
            :disabled="true"
          />
        </div>

        <div class="column">
          <TextField v-model="form.nftSymbol" :label="'Symbol'" :placeholder="'Symbol (eg: BLC60)'" :rules="rules.nftSymbol" />
        </div>
      </div>

      <div class="create-nft-action mt-2 mt-sm-4 text-center">
        <v-btn class="btn-cancel mx-1" :disabled="loading" @click="reset">Reset</v-btn>
        <v-btn class="btn-common mx-1" :loading="loading" @click="passes(submit)">Submit Application</v-btn>
      </div>
    </ValidationObserver>
  </div>
</template>

<script>
import TextField from '@/components/Form/TextField';
import DateField from '@/components/Form/Date';
import { credential } from '@/api/credential';

export default {
  components: {
    TextField,
    DateField,
  },
  data() {
    return {
      loading: false,
      form: {
        patientName: '',
        regNo: '',
        itemId: '',
        nftSymbol: '',
      },
      rules: {
        patientName: {
          required: true,
        },
        regNo: {
          required: true,
        },
        nftSymbol: {
          required: true,
        },
      },
    };
  },
  watch: {
    'form.regNo'() {
      this.form.itemId = this.form.regNo;
    },
  },
  methods: {
    submit() {
      this.loading = true;
      let data = {
        nftSymbol: this.form.nftSymbol,
        itemId: this.form.itemId,
        properties: { centerName: this.form.patientName },
        metadata: { bedCapacity: this.form.regNo },
      };
      return credential
        .mintNft(data)
        .then(res => {
          if (res.ret == 0) {
            this.showSuccess('Successfully mint NFT');
            this.reset();
            this.loading = false;
            this.$emit('results', data);
          }
        })
        .catch(err => {
          console.log(`error: ${err.msg}`);
          this.reset();
          this.loading = false;
        });
    },
    resetValidation() {
      this.$refs.observer.reset();
    },
    reset() {
      let newForm = {
        patientName: '',
        regNo: '',
        itemId: '',
        nftSymbol: '',
      };
      this.form = { ...newForm };
      this.resetValidation();
    },
  },
};
</script>

<style lang="scss" scoped></style>
