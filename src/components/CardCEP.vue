<template>
  <q-card class="my-card rounded-borders"
          flat bordered
  >
    <q-card-section>
      <article class="q-pa-lg">
        <div
          v-if="cep"
          class="text-h6"
        >
          <p>
            CEP: <label>{{ cep }}</label>
          </p>
        </div>
        <div
          v-else
          class="q-mb-lg"
        >
          CEP não foi encontrado
        </div>
        <p v-if="localidade">
          {{ localidade }} / {{ uf }}
        </p>
        <div v-if="logradouro">
          {{ logradouro }}, {{ bairro }}
        </div>
        <div v-else>
          Logradouro não foi encontrado
        </div>
      </article>
    </q-card-section>
    <q-card-actions class="justify-around items-center cols q-ml-auto bg-secondary">
      <q-btn
        flat
        round
        color="gray"
        icon="edit"
        class="q-ml-auto"
        @click="edit = true"
      />
      <q-btn
        flat
        round
        color="red"
        icon="delete"
        class="q-mx-auto"
        @click="deleteDialog = true"
      />
    </q-card-actions>
    <q-dialog v-model="edit">
      <q-card>
        <q-card-section class="row items-center q-pb-none">
          <div class="text-h6">
            Edite o endereço
          </div>
          <q-space/>
        </q-card-section>
        <q-card-section>
          <q-form
            @submit="onSubmit"
            class="q-gutter-md justify-center"
          >
            <q-input
              filled
              v-model="updateCep"
              label="Digite seu CEP"
              mask="#####-###"
              hint="Cep: 12345/678"
            />
            <q-input
              filled
              v-model="updateLogradouro"
              label="Seu logradouro"
              hint="Av. Fernanda Correa da Costa"
            />
            <q-input
              filled
              v-model="updateBairro"
              label="Seu bairro"
              hint="Jardins"
            />
            <div class="row q-px-auto">
              <q-input
                filled
                v-model="updateLocalidade"
                class="inputWidth"
                label="Seu municipio"
                hint="São Paulo"
              />
              <q-input
                filled
                v-model="updateUf"
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
                v-close-popup
              />
              <q-btn
                label="Cancelar"
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
    <q-dialog
      v-model="deleteDialog"
      persistent
    >
      <q-card>
        <q-card-section class="row items-center">
          <q-avatar
            icon="close"
            color="negative"
            text-color="white"
          />
          <span class="q-ml-sm">
            Tem certeza que deseja deletar este endereço?
          </span>
        </q-card-section>
        <q-card-actions align="right">
          <q-btn
            flat
            label="Cancel"
            color="primary"
            v-close-popup
          />
          <q-btn
            flat
            label="Confirmar"
            color="primary"
            @click="deleteEvent"
            v-close-popup
          />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-card>
</template>
<script>
import { ref } from 'vue';

export default {
  emits: [
    'delete-element',
    'update-element',
  ],
  methods: {
    onSubmit() {
      this.updateEvent();
      // eslint-disable-next-line no-console
      console.log(`valor do index = ${this.index}`);
    },
    deleteEvent() {
      this.$emit('delete-element', {
        index: this.index,
      });
    },
    updateEvent() {
      this.$emit('update-element', {
        cep: this.updateCep,
        bairro: this.updateBairro,
        localidade: this.updateLocalidade,
        uf: this.updateUf,
        logradouro: this.updateLogradouro,
        index: this.index,
      });
    },
  },
  data() {
    return {
      edit: ref(false),
      deleteDialog: ref(false),
      updateIndex: this.index,
      updateCep: this.cep,
      updateLogradouro: this.logradouro,
      updateUf: this.uf,
      updateLocalidade: this.localidade,
      updateBairro: this.bairro,
    };
  },
  props: {
    index: Number,
    cep: String,
    uf: String,
    logradouro: String,
    localidade: String,
    bairro: String,
    setAddress: [],
  },
  name: 'CardCEP',
};
</script>
<style scoped>
.my-card {
  width: 100%;
  max-width: 400px;
}
.inputWidth {
  width: calc(75dvw + 126px);
}
.inputWidth:nth-child(2) {
  margin-top: 1rem;
}
@media screen and (min-width: 1366px) {
  .my-card {
    display: flex;
    flex-direction: row;
    height: 200px;
  }

  .cols {
    flex-direction: column;
  }
  .inputWidth {
    min-width: calc(40dvw - 10rem);
  }
  .inputWidth:nth-child(2) {
    margin-top: 1rem;
  }
}
@media screen and (min-width: 1730px) {
  .my-card {
    display: flex;
    flex-direction: row;
    height: 200px;
  }
  .inputWidth {
    min-width: calc(12dvw + 32px);
  }
  .inputWidth:nth-child(2) {
    margin-top: 1rem;
  }
}
</style>
