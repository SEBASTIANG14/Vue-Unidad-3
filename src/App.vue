<script setup>
import { ref, computed } from 'vue'

const nombre = ref('')
const apellido = ref('')
const count = ref(0)
const num1 = ref(0)
const num2 = ref(0)
const resultado = computed(() => num1.value + num2.value)
const users = ref([]) 

const agregarUsuarios = () => {
  users.value.push({
    nombre: nombre.value,
    apellido: apellido.value
  })
  nombre.value = ''
  apellido.value = ''
}
</script>

<template>
  <div id="app">
    <button @click="count++">
      Count is: {{ count }}
    </button>

    <br>
    <input type="number" v-model="num1" placeholder="Escribe el primer número">
    <br><br>
    <input type="number" v-model="num2" placeholder="Escribe el segundo número">
    <br><br>
    <h2>El resultado es: {{ resultado }}</h2>
    <br>
    <form @submit.prevent="agregarUsuarios">
      <input type="text" v-model="nombre" placeholder="Escribe tu nombre">
      <br>
      <input type="text" v-model="apellido" placeholder="Escribe tu apellido">
      <br>
      <button type="button" @click="agregarUsuarios">Agregar usuario</button>
    </form>
    <ul>
      <li v-for="(user, index) in users" :key="index">
        {{ user.nombre }} {{ user.apellido }}
      </li>
    </ul>
  </div>
</template>


<style>
  button {
    width: 200px;
    height: 50px;
    padding: 10px 20px; 
    font-size: 18px;
  }
</style>





