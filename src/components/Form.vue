<template>
<div>
  <v-layout row>
    <section>
      <v-container fluid>
        <v-layout row wrap>
          <v-flex xs12>
            <v-card flat>
              <v-card-text>
                <v-radio-group v-model="tabla" :mandatory="true" row>
                  <v-radio label="Usuario" value="usuario"></v-radio>
                  <v-radio label="Sala" value="sala"></v-radio>
                </v-radio-group>
              </v-card-text>
            </v-card>
          </v-flex>
        </v-layout>
        <v-flex xs12>
          <h2 v-if="tabla === 'usuario'">Listado de usuarios</h2>
          <h2 v-if="tabla === 'sala'">Listado de salas</h2>
        </v-flex>
        <v-flex xs12>
          <v-btn v-if="tabla === 'usuario'" dark color="primary" @click.native="dialogUsuario = true">Nuevo Usuario</v-btn>
          <v-btn v-if="tabla === 'sala'" dark color="primary" @click.native="abreCreateSala(props.item.id_usuario)">Nueva Sala</v-btn>
        </v-flex>
      </v-container>
    </section>
  </v-layout>
  <v-data-table v-if="tabla === 'usuario'" v-bind:headers="headersAsignacionUsuario" v-bind:items="asignacionesUsuario" v-bind:pagination.sync="pagination" :total-items="totalItems" class="elevation-1" rows-per-page-text="registros por página">
    <template slot="items" slot-scope="props">
      <td class="text-xs-right">
        <v-btn icon dark color="primary" @click.native="abreEditUsuario(props.item.id_usuario)">
          <v-icon>edit</v-icon>
        </v-btn>
        <v-btn v-if="props.item.estado !== 'ACTIVO'" icon dark color="primary" @click.native="eliminarUsuario(props.item.id_usuario)">
          <v-icon>remove</v-icon>
        </v-btn>
      </td>
      <td>{{ props.item.nombres }}</td>
      <td>{{ props.item.apellidos }}</td>
      <td>{{ props.item.usuario }}</td>
      <td>{{ props.item.fecha_nacimiento}}</td>
    </template>
  </v-data-table>
  <v-data-table v-if="tabla === 'sala'" v-bind:headers="headersAsignacionSala" v-bind:items="asignacionesSala" v-bind:pagination.sync="pagination" :total-items="totalItems" class="elevation-1" rows-per-page-text="registros por página">
    <template slot="items" slot-scope="props">
      <td class="text-xs-right">
        <v-btn icon dark color="primary" @click.native="abreEditSala(props.item.id_sala)">
          <v-icon>edit</v-icon>
        </v-btn>
        <v-btn v-if="props.item.estado !== 'ACTIVO'" icon dark color="primary" @click.native="eliminarSala(props.item.id_sala)">
          <v-icon>remove</v-icon>
        </v-btn>
      </td>
      <td>{{ props.item.numero }}</td>
      <td>{{ props.item.nombre }}</td>
    </template>
  </v-data-table>

  <v-layout row>
    <v-dialog v-model="dialogUsuario" persistent width="1200px">
      <v-card>
        <v-card-title class="headline">
          Cargar datos de usuario
        </v-card-title>
        <v-layout row>
          <v-flex xs10 offset-xs1>
            <form @submit.prevent="editaUsuario">
              <v-layout row wrap>
                <v-flex xs6>
                  <v-text-field label="Nombres" v-model="formUsuarios.nombres"></v-text-field>
                </v-flex>
                <v-flex xs6>
                  <v-text-field label="Apellidos" v-model="formUsuarios.apellidos"></v-text-field>
                </v-flex>
                <v-flex xs6>
                  <v-text-field label="Usuario" v-model="formUsuarios.usuario"></v-text-field>
                </v-flex>
                <v-flex xs6>
                  <v-text-field label="Fecha de nacimiento" v-model="formUsuarios.fecha_nacimiento"></v-text-field>
                </v-flex>
              </v-layout>
            </form>
          </v-flex>
        </v-layout>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn class="seccion" dark @click.native="dialogUsuario = false">Cancelar
          </v-btn>
          <v-btn class="primary" dark v-on:click="crearUsuario()">Guardar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-layout>
  </div>
</template>
<script>
import axios from 'axios';

export default {
  created () {
  },
  mounted () {
    // let rute = 'https://jsonplaceholder.typicode.com'; 
    // axios.get(`${rute}/posts/1`)
    // axios.get(`http://localhost:4000/usuarios`)
    // // axios.get(`http://127.0.0.1:4000/artistas`)
    // .then(respuesta => {
    //   respuesta.data.forEach(function(item) {
    //     // this.lista.push(item.nombres + ' ' + item.apellidos)
    //     this.form.usuario = item.usuario;
    //     this.form.nombres = item.nombres;
    //     this.form.apellidos = item.apellidos;
    //     this.form.fecha_nacimiento = item.fecha_nacimiento;
    //   }, this);
    //   // this.lista = respuesta.data;
    //   console.log(this.lista);
    // })
    // .catch(error => {
    //   this.$message.error('Debe llenar el formulario para guardar.');
    // })
    // this.cargarAsignaciones ()
  },
  data () {
    return {
      mensaje: "",
      lista: [],
      tabla: 'usuario',
      formUsuarios: {
        'usuario': '',
        'nombres': '',
        'apellidos': '',
        'fecha_nacimiento': ''
      },
      headersAsignacionUsuario: [
        {text: 'Acciones', value: ''},
        {text: 'Usuario', value: 'usuario'},
        {text: 'Nombres', value: 'nombres'},
        {text: 'Apellidos', value: 'apellidos'},
        {text: 'Fecha de nacimiento', value: 'fecha_nacimiento'}
      ],
      headersAsignacionSala: [
        {text: 'Acciones', value: ''},
        {text: 'Numero', value: 'numero'},
        {text: 'Nombre', value: 'nombre'},
      ],
      asignacionesUsuario: [],
      asignacionesSala: [],
      dialogUsuario: false,
      totalItems: 0,
      pagination: {
        sortBy: null
      }
    }
  },
  watch: {
    pagination: {
      handler () {
        let sorting = '';
        if (this.pagination.sortBy != null && this.pagination.descending != null) {
          if (this.pagination.descending) {
            sorting = `&order=-${this.pagination.sortBy}`
          } else {
            sorting = `&order=${this.pagination.sortBy}`;
          }
        }
        axios.get(`http://localhost:4000/usuarios`)
        .then(response => {
          this.asignacionesUsuario = response.data;
          // this.count = response.datos.count;
          console.log('ENTRAAAA');
          return axios.get(`http://localhost:4000/salas`)
        })
        // } else {
        // this.$service.get(`usuarios?limit=5&page=1`) // TODO Apagar el incendio
        .then(response => {
          console.log('----------------');
          this.asignacionesSala = response.data;
          console.log(this.asignacionesSala);
          // this.count = response.datos.count;
        })
      },
      deep: true
    }
  },
  methods: {
    cargarAsignaciones() { // Carga lista de usuarios
      axios.get(`http://localhost:4000/usuarios`)
        .then(response => {
          this.asignacionesUsuario = response.data;
          return axios.get(`http://localhost:4000/salas`)
        })
        .then(response => {
          this.asignacionesSala = response.data;
        })
        .catch(error => console.log(error));
    },
    crearUsuario() {
      axios.post(`http://localhost:4000/usuario`, this.form)
        .then(response => {
          console.log(response);
        })
        .catch(error => console.log(error));
    },
    // crearSala() {

    // },
    eliminarSala(id) {
      axios.delete(`http://localhost:4000/sala`, {
        data: {id_sala: id}
      })
      .then(respuesta => {
        console.log(respuesta);
      })
    },
    eliminarUsuario(id) {
      axios.delete(`http://localhost:4000/usuario`, {
        data: {id_usuario: id}
      })
      .then(respuesta => {
        console.log(respuesta);
      })
    }
  },
  computed: {
    // listaParesComputed () {
    //   return this.lista.filter(valor => valor % 2 == 0)
    // }
  }
}
</script>
