<template>
  <div>
    <v-toolbar>
      <v-toolbar-title>Motoristas</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn fab dark color="indigo" @click="dialog = true"> <v-icon dark>add</v-icon></v-btn>
    </v-toolbar>
     <v-dialog v-model="dialog" max-width="70%">
        <v-card justify-center>
          <v-form ref="form">
          <v-card-title>
            <span class="headline">{{ formTitle }}</span>
          </v-card-title>

          <v-card-text>

            <v-container grid-list-md>
              <v-layout wrap justify-center>
                <v-flex xs12 sm6 md10>
                  <v-text-field v-model="motorista.nome" label="Nome" :rules="obrigatorio"></v-text-field>
                </v-flex>
              </v-layout>
              <v-layout wrap justify-center>
                <v-flex xs12 sm6 md10>
                  <v-text-field v-model="motorista.cpf" label="CPF" :rules="obrigatorio" v-mask="'###.###.###-##'"></v-text-field>
                </v-flex>
              </v-layout>
              <v-layout wrap justify-center>
                <v-flex xs12 sm6 md10>
                  <v-text-field v-model="motorista.nascimento" label="Data de Nascimento" :rules="obrigatorio" v-mask="'##/##/####'" ></v-text-field>
                </v-flex>
              </v-layout>
              <v-layout wrap justify-center>
                <v-flex xs12 sm6 md10>
                  <v-text-field v-model="motorista.modeloCarro" label="Modelo Do Carro" :rules="obrigatorio"></v-text-field>
                </v-flex>
              </v-layout>
              <v-layout wrap justify-center>
                <v-flex xs12 sm6 md5>
                  <v-radio-group v-model="motorista.sexo" :mandatory="false" row :rules="obrigatorio">
                    <v-radio label="Masculino" value="M"></v-radio>
                    <v-radio label="Feminino"  value="F"></v-radio>
                  </v-radio-group>
                </v-flex>
                <v-flex xs12 sm6 md3>
                  <v-switch v-model="motorista.ativo" :label=" `Status:  ${formatarStatus(this.motorista.ativo)}`"></v-switch>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" flat @click="fechar">Cancelar</v-btn>
            <v-btn color="blue darken-1" flat @click="salvar">Salvar</v-btn>
          </v-card-actions>
          </v-form>
        </v-card>
      </v-dialog>

    <v-data-table :headers="headers" :items="listaMotoristas"  hide-actions class="elevation-1" no-data-text="Sem Dados">
      <template v-slot:items="props">
        <td class="text-xs-left">{{ props.item.nome }}</td>
        <td class="text-xs-left">{{ props.item.cpf }}</td>
        <td class="text-xs-left">{{ props.item.nascimento }}</td>
        <td class="text-xs-left">{{ props.item.modeloCarro }}</td>
        <td class="text-xs-left">{{ props.item.sexo }}</td>
        <td class="text-xs-left">{{ formatarStatus(props.item.ativo) }}</td>
        <td class="justify-center layout px-0">
          <v-icon  class="mr-2" @click="editarItem(props.item)">edit</v-icon>
          <v-icon  @click="excluirItem(props.item)">delete</v-icon>
        </td>
      </template>
    </v-data-table>
  </div>
</template>

<script>
export default {
   data: () => ({
      dialog: false,
      headers: [
        { text: 'Nome',                align: 'left', value: 'nome',       sortable: false},
        { text: 'CPF',                 align: 'left', value: 'cpf',        sortable: false },
        { text: 'Data de Nascimento',  align: 'left', value: 'nascimento', sortable: false },
        { text: 'Modelo do Carro',     align: 'left', value: 'modeloCarro',sortable: false },
        { text: 'Sexo',                align: 'left', value: 'sexo',       sortable: false },
        { text: 'Status',              align: 'left', value: 'status',     sortable: false },
        { text: 'Ações',                              value: 'acoes',      sortable: false }
      ],

      listaMotoristas: [],
      editarIndex: -1,
      motorista: {
        nome: '',
        cpf: null,
        nascimento: null,
        modeloCarro: null,
        sexo: null,
        ativo: false
      },
      defaultItem: {
        nome: '',
        cpf: null,
        nascimento: null,
        modeloCarro: null,
        sexo: null,
        ativo: false
      },

      //validação
      obrigatorio: [
        v => !!v || 'Esse campos é necessário'
      ],
    }),

    computed: {
      formTitle () {
        return this.editarIndex === -1 ? 'Adicionar Motorista' : 'Editar Motorista'
      }
    },

    watch: {
      dialog (val) {
        val || this.fechar()
      }
    },

    created () {
      this.initialize()
    },

    methods: {
      initialize () {
        // this.listaMotoristas = [
        //   {
        //     name: 'Frozen Yogurt',
        //     calories: 159,
        //     fat: 6.0,
        //     carbs: 24,
        //     protein: 4.0
        //   },
        //   {
        //     name: 'Ice cream sandwich',
        //     calories: 237,
        //     fat: 9.0,
        //     carbs: 37,
        //     protein: 4.3
        //   },
        //   {
        //     name: 'Eclair',
        //     calories: 262,
        //     fat: 16.0,
        //     carbs: 23,
        //     protein: 6.0
        //   }
        // ]
      },

      editarItem (item) {
        this.editarIndex = this.listaMotoristas.indexOf(item)
        this.motorista = Object.assign({}, item)
        this.dialog = true
      },

      excluirItem (item) {
        let index = this.listaMotoristas.indexOf(item)
        confirm('Tem certeza que deseja excluir esse motorista?') && this.listaMotoristas.splice(index, 1)
      },

      fechar () {
        this.dialog = false
        setTimeout(() => {
          this.$refs.form.resetValidation()
          this.motorista = Object.assign({}, this.defaultItem)
          this.editarIndex = -1
        }, 300)
      },

      salvar () {
        if(this.$refs.form.validate()){
          if (this.editarIndex > -1) {
            Object.assign(this.listaMotoristas[this.editarIndex], this.motorista)
          } else {
            this.listaMotoristas.push(this.motorista)
          }
          this.fechar()
        }
      },

      formatarStatus(status){
         return status === false ? 'Inativo' : 'Ativo'

      }
    }

}
</script>

<style>

</style>
