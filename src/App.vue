<template>
  <div>
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
      <h1>Hola, bienvenido!</h1>
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Username</th>
            <th>Phone</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in users" :key="user.id">
            <td>{{ user.name }}</td>
            <td>{{ user.username }}</td>
            <td>{{ user.phone }}</td>
            <td>
              <button @click="editUser(user)">Editar</button>
              <button @click="deleteUser(user.id)">Borrar</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const email = ref('')
    const password = ref('')
    const access = ref(false)
    const users = ref([])

    const onSubmit = () => {
      fetch('/users.json')
      .then((res) => res.json())
      .then((data) => {
        const user = data.find(
          (user) => user.email === email.value && user.password === password.value
        );

        if (user) {
          access.value = true;
          users.value = data; 
          alert('Login exitoso, bienvenido!');
        } else {
          alert('Correo o contraseña incorrectos');
        }
      })
      .catch((e) => {
        alert('Ocurrió un error: ' + e.message);
      });
    }

    const deleteUser = (id) => {
      users.value = users.value.filter(user => user.id !== id);
      alert('Usuario eliminado.');
    }

    const editUser = (user) => {
      
    }

    return {
      email,
      password,
      access,
      onSubmit,
      users,
      deleteUser,
      editUser,
    }
  },
}
</script>

<style>
table {
  width: 100%;
  border-collapse: collapse;
}
table, th, td {
  border: 1px solid black;
}
th, td {
  padding: 8px;
  text-align: left;
}
th {
  background-color: #f2f2f2;
}
button {
  margin-right: 5px;
}
</style>
