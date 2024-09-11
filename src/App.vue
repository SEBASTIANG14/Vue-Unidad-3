<script setup>
import { ref, computed } from 'vue'

// Definimos las variables reactivas
const nombre = ref('')
const count = ref(0)
const num1 = ref(0)
const num2 = ref(0)
const resultado = computed(() => num1.value + num2.value)
</script>

<template>
  <div id="app">
    <h1>Mi nombre es: {{ nombre }}</h1>
    <br>
    <input type="text" v-model="nombre" placeholder="Escribe tu nombre aquí">
    <br><br>
    <button @click="count++">
      Count is: {{ count }}
    </button>
    <br>
    <input type="number" v-model="num1" placeholder="Escribe el primer número">
    <br><br>
    <input type="number" v-model="num2" placeholder="Escribe el segundo número">
    <br><br>
    <h2>El resultado es: {{ resultado }}</h2>
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


