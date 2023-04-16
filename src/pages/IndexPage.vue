<template>
  <q-page class="flex column">
    <section class="q-pa-xl row items-start">
      <q-btn
        outline
        align="around"
        color="green"
        icon="save"
        class="q-mx-auto q-mb-xs"
        label="Novo endereço"
        @click="add = true"
      />
      <q-form
        @submit="findAddress"
        class="q-gutter-md row items-start q-mx-auto">
        <q-input
          outlined dense
          debounce="300"
          v-model="apiCep"
          placeholder="Procurar"
          name="apiCep"
          mask="#####-###"
          hint="Cep: 12345/678"
        >
          <template
            v-slot:before>
            <q-icon
              v-if="apiCep !== ''"
              name="close"
              @click="clearApiCep"
            />
          </template>
          <template
            v-slot:append>
            <q-icon name="search"/>
          </template>
        </q-input>
        <div>
          <q-btn label="Submit" type="submit" color="primary"/>
        </div>
      </q-form>
    </section>
    <section class=" flex row justify-center">
      <CardCEP
        class="q-ma-lg"
        v-for="(address,index) in addresses"
        :key="address"
        :index="index"
        :cep="address.cep"
        :logradouro="address.logradouro"
        :bairro="address.bairro"
        :localidade="address.localidade"
        :uf="address.uf"
        @delete-element="deleteEvent"
        @update-element="updateEvent"
      />
    </section>
    <q-dialog v-model="add">
      <q-card>
        <q-card-section class="row items-center q-pb-none">
          <div class="text-h6">Novo Endereço</div>
          <q-space/>
        </q-card-section>
        <q-card-section>
          <q-form
            @submit="onSubmit"
            @reset="onReset"
            class="q-gutter-md"
          >
            <q-input
              filled
              v-model="cep"
              label="Digite seu CEP"
              mask="#####-###"
              hint="Cep: 12345/678"
            />
            <q-input
              filled
              v-model="logradouro"
              label="Seu logradouro"
              hint="Av. Fernanda Correa da Costa"
            />
            <q-input
              filled
              v-model="bairro"
              label="Seu bairro"
              hint="Jardins"
            />
            <div class="row">
              <q-input
                filled
                v-model="localidade"
                label="Seu municipio"
                hint="São Paulo"
              />
              <q-input
                filled
                v-model="uf"
                label="Sua UF"
                mask="AA"
                unmasked-value
                hint="SP"
              />
            </div>
            <div>
              <q-btn
                label="Salvar"
                type="submit"
                color="primary"
                v-close-popup
              />
              <q-btn
                label="Cancelar"
                type="reset"
                color="primary"
                class="q-ml-sm"
                outline
                v-close-popup
              />
            </div>
          </q-form>
        </q-card-section>
      </q-card>
    </q-dialog>
  </q-page>
</template>
<script>
import { defineComponent, ref } from 'vue';
import CardCEP from 'components/CardCEP.vue';
import { useQuasar } from 'quasar';
import axios from 'axios';

export default defineComponent({
  name: 'IndexPage',
  components: { CardCEP },
  data() {
    return {
      cep: '',
      complemento: '',
      bairro: '',
      uf: '',
      localidade: '',
      logradouro: '',
      addresses: [],
      apiCep: '',
      add: ref(false),
      $q: useQuasar(),
    };
  },
  methods: {
    deleteEvent(address) {
      this.addresses.splice(address.index, 1);
      localStorage.setItem('address', JSON.stringify(this.addresses));
      this.$q.notify({
        message: 'Endereço removido com êxito.',
        position: 'top-right',
        color: 'negative',
      });
    },
    updateEvent(address) {
      const updatedAddress = {
        cep: address.cep,
        bairro: address.bairro,
        localidade: address.localidade,
        uf: address.uf.toUpperCase(),
        logradouro: address.logradouro,
      };
      this.addresses.splice(address.index, 0, updatedAddress);
      this.addresses.splice(address.index + 1, 1);
      // this.addresses = [...this.addresses, updatedAddress];
      localStorage.setItem('address', JSON.stringify(this.addresses));
      this.$q.notify({
        message: 'Endereço alterado com êxito.',
        position: 'top-right',
        color: 'edite',
      });
    },
    onSubmit() {
      const setAddress = {
        cep: this.cep,
        bairro: this.bairro,
        localidade: this.localidade,
        uf: this.uf.toUpperCase(),
        logradouro: this.logradouro,
      };
      this.addresses = [...this.addresses, setAddress];
      localStorage.setItem('address', JSON.stringify(this.addresses));
      this.onReset();
      this.$q.notify({
        message: 'Endereço salvo com êxito.',
        position: 'top-right',
        color: 'positive',
      });
    },
    onSubmitAPi() {
      axios
        .get(`https://viacep.com.br/ws/${this.apiCep}/json/`)
        .then((response) => {
          const apiAdress = {
            cep: response.data.cep,
            bairro: response.data.bairro,
            localidade: response.data.localidade,
            uf: response.data.uf.toUpperCase(),
            logradouro: response.data.logradouro,
          };
          this.addresses = [...this.addresses, apiAdress];
          localStorage.setItem('address', JSON.stringify(this.addresses));
        })
        .catch((err) => {
          this.$q.notify({
            message: `Erro ao gerar, ${err}!`,
            position: 'top-right',
            color: 'negative',
          });
        });
    },
    findAddress() {
      if (this.apiCep === '') {
        this.addresses = JSON.parse(localStorage.getItem('address'));
        return;
      }
      const index = this.addresses.findIndex((address) => address.cep === this.apiCep);
      if (index === -1) {
        this.onSubmitAPi();
        this.$q.notify({
          message: 'Endereço não encontrado, gerando um novo!',
          position: 'top-right',
          color: 'positive',
        });
      } else {
        this.addresses = [this.addresses[index]];
        this.$q.notify({
          message: 'Endereço encontrado com sucesso.',
          position: 'top-right',
          color: 'positive',
        });
      }
    },
    clearApiCep() {
      this.apiCep = '';
      this.addresses = JSON.parse(localStorage.getItem('address'));
    },
    onReset() {
      this.cep = '';
      this.logradouro = '';
      this.localidade = '';
      this.uf = '';
      this.bairro = '';
    },
  },
  mounted() {
    if (localStorage.address === undefined) {
      localStorage.address = JSON.stringify([]);
    }
    this.addresses = JSON.parse(localStorage.address);
  },
});
</script>
