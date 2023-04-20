<template>
  <q-page class="flex column">
    <section class="q-py-lg row nav-address items-start">
      <q-btn
        push
        align="around"
        color="green"
        icon="save"
        class="q-mx-xl q-mb-md addButton"
        label="Novo endereço"
        size="md"
        padding="sm"
        @click="add = true"
      />
      <q-form
        @submit="findAddress"
        class="q-gutter-y--md row items-start q-mx-xl">
        <q-input
          outlined dense
          debounce="300"
          v-model="apiCep"
          label="Procurar endereco"
          class="q-mr-sm"
          name="apiCep"
          style="min-width: 20rem"
          mask="#####-###"
          hint="Cep:12345-678"
          lazy-rules
          :rules="[
                value => value.length <= 9 & value.length > 8
                || 'Por favor escreva um cep de 8 digitos'
                ]"
        >
          <template
            v-slot:append>
            <q-icon
              v-if="apiCep !== ''"
              name="close"
              @click="clearApiCep"
            />
          </template>
          <template
            v-slot:prepend>
            <q-icon name="search"/>
          </template>
        </q-input>
        <div>
          <q-btn
            push
            size="md"
            class="btnSubmit"
            padding="sm"
            label="Procurar"
            type="submit"
            color="primary"
          />
        </div>
      </q-form>
    </section>
    <section
      class="row q-gutter-lg q-mx-auto cardSection"
    >
      <CardCEP
        class="col-12 col-sm-3"
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
              label="Digite seu CEP*"
              mask="#####-###"
              hint="Cep: 12345-678"
              :rules="[
                value => value.length <= 9 & value.length > 8
                || 'Por favor escreva um cep de 8 digitos'
                ]"
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
            <div class="row q-px-auto">
              <q-input
                filled
                v-model="localidade"
                class="inputWidth"
                label="Seu municipio"
                hint="São Paulo"
              />
              <q-input
                filled
                v-model="uf"
                class="inputWidth"
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
      index: '',
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
        color: 'positive',
      });
      this.onReset();
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
        color: 'positive',
      });
      this.onReset();
    },
    onSubmit() {
      this.index = this.addresses.findIndex((address) => address.cep === this.cep);
      if (this.index === -1) {
        const setAddress = {
          cep: this.cep,
          bairro: this.bairro,
          localidade: this.localidade,
          uf: this.uf.toUpperCase(),
          logradouro: this.logradouro,
        };
        this.addresses = [...this.addresses, setAddress];
        localStorage.setItem('address', JSON.stringify(this.addresses));
        this.$q.notify({
          message: 'Endereço salvo com êxito.',
          position: 'top-right',
          color: 'positive',
        });
        this.onReset();
        this.add = false;
      } else {
        this.$q.notify({
          message: 'Endereço já está cadastrado.',
          position: 'top-right',
          color: 'positive',
        });
        this.add = false;
        this.onReset();
      }
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
        .catch(() => {
          this.$q.notify({
            message: 'Erro durante cadastro, verifice o dado informado!',
            position: 'top-right',
            color: 'negative',
          });
        });
      this.onReset();
    },
    findAddress() {
      if (this.apiCep === '') {
        this.addresses = JSON.parse(localStorage.getItem('address'));
        return;
      }
      this.index = this.addresses.findIndex((address) => address.cep === this.apiCep);
      if (this.index === -1) {
        this.onSubmitAPi();
        this.$q.notify({
          message: 'Endereço não encontrado, cadastrando um novo!',
          position: 'top-right',
          color: 'positive',
        });
      } else {
        this.addresses = [this.addresses[this.index]];
        this.$q.notify({
          message: 'Endereço encontrado com sucesso.',
          position: 'top-right',
          color: 'positive',
        });
        this.onReset();
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
      this.index = '';
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

<style scoped>
.cardSection {
  margin: 10px;
  width: 85dvw;
}
.addButton {
  min-width: 16.75rem;
}
.btnSubmit{
  margin-top: 0.25rem;
}
.inputWidth {
  width: calc(75dvw + 126px);
}

.inputWidth:nth-child(2) {
  margin-top: 1rem;
}

@media screen and (min-width: 1280px) {
  .cardSection {
    width: 100dvw;
  }
  .btnSubmit {
    margin-top: 0;
  }
  .addButton {
    min-width: 15rem;
  }
  .nav-address {
    justify-content: space-around;
  }

  .inputWidth {
    min-width: calc(40dvw - 10rem);
  }

  .inputWidth:nth-child(2) {
    margin-top: 1rem;
  }
}

@media screen and (min-width: 1730px) {
  .addButton {
    min-width: 250px;
  }
  .cardSection {
    margin-left: auto;
    margin-right: auto;
    width: 80dvw;
  }

  .inputWidth {
    min-width: calc(12dvw + 32px);
  }

  .inputWidth:nth-child(2) {
    margin-top: 1rem;
  }
}
</style>
