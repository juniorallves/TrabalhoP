<template xmlns:v-slot="http://www.w3.org/1999/XSL/Transform">
  <v-container fluid grid-list-xl>
    <app-breadcrumbs :items="breadcrumbItems" icon="person" title="Informações do Usuário"></app-breadcrumbs>
    <v-layout row wrap>
      <v-flex xs12>
        <v-widget enableActions title="Informações Básicas">
          <div slot="widget-content">
            <v-layout row>
              <v-flex xs12>
                <span class="title" style="font-weight:bold">{{ usuario.nome }}</span>
              </v-flex>
            </v-layout>
            <v-divider></v-divider>
            <v-layout row>
              <v-flex xs3>
                <span class="font-weight-bold text-uppercase">Cpf</span>
              </v-flex>
              <v-flex xs3>
                <span class="font-weight-bold text-uppercase">Data de Nascimento</span>
              </v-flex>
              <v-flex xs3>
                <span class="font-weight-bold text-uppercase">Sexo</span>
              </v-flex>
              <v-flex xs3>
                <span class="font-weight-bold text-uppercase">Papel</span>
              </v-flex>
            </v-layout>

            <v-layout row>
              <v-flex xs3>
                <span class="font-weight-regular">{{ usuario.cpf | cpf }}</span>
              </v-flex>
              <v-flex xs3>
                <span class="font-weight-regular">{{ usuario.dataNascimento | data }}</span>
              </v-flex>
              <v-flex xs3>
                <span class="font-weight-regular text-uppercase">{{ sexo }}</span>
              </v-flex>
              <v-flex xs3>
                <span class="font-weight-regular text-uppercase">{{ papel }}</span>
              </v-flex>
            </v-layout>

            <v-divider></v-divider>

            <v-layout row>
              <v-flex xs6>
                <span class="font-weight-bold text-uppercase">Endereço:</span>
                <span class="font-weight-regular">{{ usuario.endereco }}</span>
              </v-flex>
              <v-flex xs6>
                <span class="font-weight-bold text-uppercase">Email:</span>
                <span class="font-weight-regular">{{ usuario.email }}</span>
              </v-flex>
            </v-layout>
            <v-layout row>
              <v-flex xs6>
                <span class="font-weight-bold text-uppercase">Cidade:</span>
                <span class="font-weight-regular">{{ usuario.cidade }}</span>
              </v-flex>
              <v-flex xs6>
                <span class="font-weight-bold text-uppercase">Telefone:</span>
                <span class="font-weight-regular">{{ usuario.telefone | telefone }}</span>
              </v-flex>
            </v-layout>
            <v-layout row>
              <v-flex xs6>
                <span class="font-weight-bold text-uppercase">UF:</span>
                <span class="font-weight-regular">{{ usuario.uf }}</span>
              </v-flex>
              <v-flex xs6>
                <span class="font-weight-bold text-uppercase">País:</span>
                <span class="font-weight-regular">{{ usuario.pais }}</span>
              </v-flex>
            </v-layout>
            <v-layout row>
              <v-flex xs6>
                <span class="font-weight-bold text-uppercase">Cargo:</span>
                <span class="font-weight-regular">{{ usuario.cargo }}</span>
              </v-flex>
            </v-layout>
          </div>
          <div slot="widget-actions">
            <v-layout row>
              <v-flex xs12>
                <v-btn color="primary" flat :to=" usuario.id + '/editar'">
                  <v-icon small>edit</v-icon>Editar
                </v-btn>
                <v-btn color="error" flat @click="dialog = true">
                  <v-icon small>delete</v-icon>Deletar
                </v-btn>
              </v-flex>
            </v-layout>
          </div>
        </v-widget>
      </v-flex>
      
      <v-flex xs12 v-if="usuario.papel === 'MEDICO' || usuario.papel === 'COLABORADOR'">
        <v-widget title="Exames/Procedimentos Realizados" v-if="usuario.papel === 'MEDICO'">
          <div slot="widget-content">
            <v-toolbar card color="white" >
              <v-text-field flat solo prepend-icon="search" placeholder="Buscar" v-model="search" hide-details class="hidden-sm-and-down" ></v-text-field>
            </v-toolbar>
            <v-data-table
                    :search="search"
                    :headers="headers_exames1"
                    :items="exames"
                    no-data-text="Nenhum resultado encontrado"
                    no-results-text="Nenhum resultado encontrado"
            >

              <template v-slot:items="props">
                <tr @click="props.expanded = !props.expanded">
                  <td class="text-xs-left">{{ props.item.nomeExame }}</td>
                  <td class="text-xs-left">{{ props.item.colaborador_id }}</td>
                  <td class="text-xs-left">{{ props.item.dataExame | data}}</td>
                  <td class="text-xs-left">{{ props.item.diaProximoExame | data}}</td>
                  
                   <td class="text-xs-left" v-if="props.item.diaProximoExame
                    == props.item.dataExame "><i class="material-icons">done_outline</i></td>

                  <td class="text-xs-left" v-if="dataAtual > props.item.diaProximoExame &&
                    props.item.diaProximoExame != props.item.dataExame "><i class="material-icons">
                    report_problem
                  </i></td>

                  <td class="text-xs-left" v-if="dataAtual <= props.item.diaProximoExame"><i class="material-icons">
                    schedule
                  </i></td>0


                </tr>
              </template>
            </v-data-table>
          </div>
         
        </v-widget>
        <v-widget title="Exames/Procedimentos Vinculados ao Colaborador" v-if="usuario.papel === 'COLABORADOR'">
          <div slot="widget-content">
            <v-toolbar card color="white" >
              <v-text-field flat solo prepend-icon="search" placeholder="Buscar" v-model="search" hide-details class="hidden-sm-and-down" ></v-text-field>
            </v-toolbar>
            <v-data-table
                    :search="search"
                    :headers="headers_exames2"
                    :items="exames"
                    no-data-text="Nenhum resultado encontrado"
                    no-results-text="Nenhum resultado encontrado"
            >

              <template v-slot:items="props">
                <tr @click="props.expanded = !props.expanded">
                  <td class="text-xs-left">{{ props.item.nomeExame }}</td>
                  <td class="text-xs-left">{{ props.item.medico_id }}</td>
                  <td class="text-xs-left">{{ props.item.dataExame | data}}</td>
                  <td class="text-xs-left">{{ props.item.diaProximoExame | data}}</td>
                  
                   <td class="text-xs-left" v-if="props.item.diaProximoExame
                    == props.item.dataExame "><i class="material-icons">done_outline</i></td>

                  <td class="text-xs-left" v-if="dataAtual > props.item.diaProximoExame &&
                    props.item.diaProximoExame != props.item.dataExame "><i class="material-icons">
                    report_problem
                  </i></td>

                  <td class="text-xs-left" v-if="dataAtual <= props.item.diaProximoExame"><i class="material-icons">
                    schedule
                  </i></td>0



                </tr>
              </template>
            </v-data-table>
          </div>
        </v-widget>
      </v-flex>
    </v-layout>

    <v-dialog max-width="290" v-model="dialog">
      <v-card>
        <v-card-title class="headline">Excluir Usuário?</v-card-title>

        <v-card-text>Tem certeza que deseja excluir usuário?</v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>

          <v-btn @click="dialog = false" color="silver darken-1" flat="flat">Não</v-btn>

          <v-btn @click="excluirUsuario()" color="red darken-1" flat="flat">Sim</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-snackbar :color="cor" right top v-model="snackbar">{{ mensagem }}</v-snackbar>
  </v-container>
</template>

<script>
import axios from "axios";
import AppBreadcrumbs from "../../../components/core/AppBreadcrumbs";
import VWidget from "../../../components/core/VWidget";
import store from "../../../store";
import moment from "moment";

import teste from "moment"

export default {
  props: {
    id: String
  },

  components: {
    AppBreadcrumbs,
    VWidget
  },

  computed: {
    sexo() {
      return this.usuario.sexo
        ? store.getters["enums/getLabelSexo"](this.usuario.sexo)
        : "";
    },
    papel() {
      return this.usuario.papel
        ? store.getters["enums/getLabelPapel"](this.usuario.papel)
        : "";
    }
  },

  data() {
    return {
      dataAtual: new Date().toISOString().substr(0, 10),
      search: '',
      headers_exames1: [
        { text: "Tipo de Exame", value: "nomeExame", sortable: false },
        { text: "Colaborador", value: "colaborador_id", sortable: false },
        { text: "Data Exame", value: "dataExame", sortable: false },
        { text: "Data Retorno", value: "diaProximoExame", sortable: false },
        { text: "Status", value: "", sortable: false },
      ],
      headers_exames2: [
        { text: "Tipo de Exame", value: "nomeExame", sortable: false },
        { text: "Medico", value: "medico_id", sortable: false },
        { text: "Data Exame", value: "dataExame", sortable: false },
        { text: "Data Retorno", value: "diaProximoExame", sortable: false },
        { text: "Status", value: "", sortable: false },      ],
      usuario: {
        uf: null,
        dataNascimento: null,
        endereco: null,
        email: null,
        nome: null,
        papel: null,
        estadoCivil: null,
        cidade: null,
        id: null,
        telefone: null,
        sexo: null,
        cpf: null,
        pais: null,
        cargo: null
      },
      inscricoes: [],
      exames:[],
      userId:'',
      breadcrumbItems: [
        { position: 1, text: "Usuários", disabled: false, href: "/gestor/usuarios/listarUsuarios" }
      ],

      dialog: false,

      cor: "",
      snackbar: false,
      mensagem: ""
    };
  },
  mounted: function() {
    this.initialize();
  },
  filters: {
    cpf: function(cpf) {
      if (!cpf) return "";
      return cpf.replace(/(\d{3})(\d{3})(\d{3})(\d{2})/g, "$1.$2.$3-$4");
    },
    data: function(data) {

      return data ? moment(data).format("LL") : "";

    },
    telefone: function(telefone) {
      if (!telefone) {
        return "";
      } else if (telefone.length === 11) {
        return telefone.replace(/(\d{2})(\d)(\d{4})(\d{4})/g, "($1) $2.$3-$4");
      } else if (telefone.length === 10) {
        return telefone.replace(/(\d{2})(\d{4})(\d{4})/g, "($1) $2-$3");
      }
    }
  },
  methods: {
    initialize() {
      this.userId = this.$route.params.id;

      axios.get(`/usuario/${this.id}`).then(res => {
        this.usuario = res.data;
        this.breadcrumbItems.push({ text: this.usuario.nome, disabled: true });

        if(this.usuario.papel === "MEDICO"){
          axios.get(`/exame/listar/medico/${this.id}`).then(res => {
            this.exames = res.data;
          });
        }

        else if(this.usuario.papel === "COLABORADOR"){

          axios.get(`/exame/listar/colaborador/${this.id}`).then(res => {
            this.exames = res.data;
          });
        }
        else{
          console.log("OK");
        }
      });

    },

    getLabelStatusAvaliacao(status) {
      return store.getters["enums/getLabelStatusAvaliacao"](status);
    },
    excluirUsuario() {
      axios
        .delete("/usuario/excluir-usuario/" + this.id)
        .then(() => {
          this.$router.push( "/gestor/usuarios/listarUsuarios", {
            params: {
              cor: "success",
              snackbar: true,
              mensagem: "Usuário excluído com sucesso"
            }
          });
        })
        .catch(error => {
          if (error.response) {
            this.dialog = false;
            this.cor = "error";
            this.snackbar = true;
            this.mensagem = error.response.data;
          }
        });
    }
  },
  created() {
    listarExames();
  }

};
</script>

<style>
.APROVADO {
  color: #45e276;
  font-weight: 500;
}

.REPROVADO {
  color: #ea4c72;
  font-weight: 500;
}

.EM_AVALIACAO {
  color: #565656;
  font-weight: 500;
}
</style>