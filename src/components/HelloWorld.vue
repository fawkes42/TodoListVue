<template>
  <div class="hello">
    <nav class="menu">
      <img src="../assets/logo.png">
    </nav>
    <h1>{{ title }}</h1>
    <h2>Bacaníssimo</h2>
    <section class="bacana">
      <div class="button">
        <input type="button" value="+" @click="novo">
        <ul>
          <li v-for="(item, index) in listaFiltrada" :key="index">
            <input type="text" v-model="item.texto" @input="atualiza(item, 'texto')" placeholder="Titulo da tarefa">
            <input type="checkbox" v-model="item.pronto" @change="atualiza(item, 'pronto')">
            <input type="button" value="-" @click="apaga(index)">
          </li>
        </ul>
      </div>
      <div class="chat">
        <div class="talk">
          <ul>
            <li v-for="message in messages" :key="message">
              {{message.msg}}
            </li>
          </ul>
        </div>
        <div class="panel">
          <input type="text" v-model="message" placeholder="Digite sua mensagem">
          <input type="button" value="Enviar" @click="send(message)">
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import * as firebase from 'firebase/app'
import 'firebase/database'

export default {
  name: 'HelloWorld',
  data () {
    return {
      title: 'Teste Vue.js',
      mostrar: true,
      lista: [],
      messages: [],
      message: ''
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
    },
    carregaChat (snapshot) {
      this.messages = []
      let valores = snapshot.val()
      for (let prop in valores) {
        this.messages.push({
          key: prop,
          msg: valores[prop].msg
        })
      }
    },
    send (msg) {
      let key = firebase.database().ref().child('chat').push().key
      firebase.database().ref('chat/' + key).set({msg})
      this.message = ''
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
    firebase.database().ref('chat').once('value').then(snapshot => {
      this.carregaChat(snapshot)
    })
    firebase.database().ref('chat').on('value', snapshot => {
      this.carregaChat(snapshot)
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
.bacana{
  display: flex;
  height: auto;
}
.button{
  width: 50%;
}
.chat{
  position: relative;
}
.talk{
  margin-bottom: 40px
}
.panel{
  position: absolute;
  bottom: 0 !important;
  left: 0;
  display: flex;
}
li{
  width: auto;
  margin: 10px;
  list-style-type: none;
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
