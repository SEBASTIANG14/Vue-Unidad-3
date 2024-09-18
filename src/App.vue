<template>
  <h1>Login Perrón</h1>
  <form @submit.prevent="onSubmit" v-if="!access">
    <fieldset>
      <label>Correo</label>
      <input v-model="email" type="email" placeholder="Escribe tu correo aquí" required />
    </fieldset>
    <fieldset>
      <label>Contraseña</label>
      <input v-model="password" type="password" placeholder="Escribe tu contraseña aquí" required />
    </fieldset>
    <button type="submit">Iniciar sesión</button>
  </form>

  <div class="bienvenida" v-if="access">
    <h1>ola k ace</h1>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const email = ref('')
    const password = ref('')
    const access = ref(false)

    const onSubmit = () => {
      fetch('/users.json')
      .then((res) => res.json())
      .then((data) => {
        const user = data.find(
          (user) => user.email === email.value && user.password === password.value
        );

        if (user) {
          access.value = true;
          alert('Login exitoso, bienvenido!');
        } else {
          alert('Correo o contraseña incorrectos');
        }
      })
      .catch((e) => {
        alert('Ocurrió un error: ' + e.message);
      });
    }
    return {
      email,
      password,
      access,
      onSubmit,
    }
  },
}
</script>

<style>
 
</style>
