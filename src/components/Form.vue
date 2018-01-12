<template>
  <section>
    <!-- <h2 class="title">Formulario</h2>
    {{ mensaje }} <p>Hola Mundo</p> -->
    <form @submit.prevent="enviar">
      <input type="text"
      class="form-control"
      v-model="mensaje">
      <button type="submit" class="btn">Mostrar</button>
    </form>
    <h3>Listado</h3>
    <ul v-if="lista.length > 0">
      <li v-for="item in lista">
        <!-- <span v-if="parseInt(item) % 2 == 0"> -->
          {{item}}
        <!-- </span> -->
      </li>
    </ul>
    <p v-else>
      No tiene registros
    </p>
  </section>
</template>
<script>
import axios from 'axios';

export default {
  created () {
    // this.lista = [1, 2, 3, 4, 5, 6, 7, 8, 9];
  },
  mounted () {
    // let rute = 'https://jsonplaceholder.typicode.com'; 
    // axios.get(`${rute}/posts/1`)
    axios.get(`http://localhost:4000/artistas`)
    // axios.get(`http://127.0.0.1:4000/artistas`)
    .then(respuesta => {
      respuesta.data.forEach(function(item) {
        this.lista.push(item.nombres + ' ' + item.apellidos)
      }, this);
      // this.lista = respuesta.data;
      console.log(this.lista[0]);
    })
    .catch(error => {
      this.$message.error('Debe llenar el formulario para guardar.');
    })
  },
  data () {
    return {
      mensaje: "Hey Baby",
      lista: [],
      listadoPares: []
    }
  },
  methods: {
    enviar () {
      this.lista.push(this.mensaje);
      console.log(this.lista);
    },
    listaPares () {
      return this.lista.filter(valor => valor % 2 == 0)
    }
  },
  computed: {
    listaParesComputed () {
      return this.lista.filter(valor => valor % 2 == 0)
    }
  }
}
</script>
