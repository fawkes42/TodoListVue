<template>
  <div class="hello">
    <nav class="menu">
      <img src="../assets/logo.png">
    </nav>
    <h1>{{ msg }}</h1>
    <h2>Bacaníssimo</h2>
      <div class="button">
        <input type="button" value="+" @click="novo">
        <input type="button" :value="mostrar ? 'Mostrar somente pendentes' : 'Mostrar todos'" @click="show">
        <ul>
          <li v-for="(item, index) in listaFiltrada" :key="index">
            <input type="text" v-model="item.texto" @input="atualiza(item, 'texto')">
            <input type="checkbox" v-model="item.pronto" @change="atualiza(item, 'pronto')">
            <input type="button" value="-" @click="apaga(index)">
          </li>
        </ul>
      </div>
  </div>
</template>

<script>
import * as firebase from 'firebase/app'
import 'firebase/database'

export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Teste Vue.js',
      mostrar: true,
      lista: []
    }
  },
  methods: {
    novo () {
      let key = firebase.database().ref().child('tasks').push().key
      firebase.database().ref('tasks/' + key).set({texto: '', pronto: false, key})
    },
    show () {
      this.mostrar = !this.mostrar
    },
    apaga (i) {
      firebase.database().ref('tasks/' + this.lista[i].key).remove()
    },
    atualiza (item, campo) {
      firebase.database().ref('tasks/' + item.key + '/' + campo).set(item[campo])
    },
    carrega (snapshot) {
      this.lista = []
      let valores = snapshot.val()
      for (let prop in valores) {
        this.lista.push({
          key: prop,
          texto: valores[prop].texto,
          pronto: valores[prop].pronto
        })
      }
    }
  },
  computed: {
    listaFiltrada () {
      return this.mostrar ? this.lista : this.lista.filter(el => {
        return !el.pronto
      })
    }
  },
  mounted () {
    // Your web app's Firebase configuration
    var firebaseConfig = {
      apiKey: 'AIzaSyB3TEPR45_pGvzofu8Oxj6iJ4dYWl0KHl4',
      authDomain: 'to-do-list-d3aa5.firebaseapp.com',
      databaseURL: 'https://to-do-list-d3aa5.firebaseio.com',
      projectId: 'to-do-list-d3aa5',
      storageBucket: 'to-do-list-d3aa5.appspot.com',
      messagingSenderId: '832078297256',
      appId: '1:832078297256:web:e8b3378c74d8261b'
    }
    // Initialize Firebase
    firebase.initializeApp(firebaseConfig)
    firebase.database().ref('tasks').once('value').then(snapshot => {
      this.carrega(snapshot)
    })
    firebase.database().ref('tasks').on('value', snapshot => {
      this.carrega(snapshot)
    })
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  width: 100%;
  color: white;
  text-shadow: 2px 2px 5px green;
  font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  text-align: center;
}
.menu{
  width: 100%;
  height: 60px;
  background-color: rgb(20, 20, 20);
  display: flex;
}
img{
  margin: 5px 0 0 20px;
  height: 50px;
  background-color: transparent;
}
li{
  width: auto;
  margin: 10px;
  list-style-type: none
}
input[type = button], input[type = text], input[type = checkbox]{
  margin: 5px;
  padding: 5px 10px;
  border: 2px black;
  border-radius: 5px;
  box-shadow: 5px 5px 5px gray;
  background-color: white
}
</style>
