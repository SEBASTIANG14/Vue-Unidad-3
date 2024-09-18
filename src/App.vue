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
      <button @click="showModal = true">Add</button>
      <button @click="logout">Cerrar sesión</button>
      
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
              <button @click="editUser(user)">Edit</button>
              <button @click="deleteUser(user.id)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>

      <div v-if="showModal" class="modal">
        <div class="modal-content">
          <span class="close" @click="showModal = false">&times;</span>
          <h2>New user</h2>
          <label>Name</label>
          <input v-model="newUser.name" type="text" placeholder="Name" />
          <label>Username</label>
          <input v-model="newUser.username" type="text" placeholder="Username" />
          <label>Phone</label>
          <input v-model="newUser.phone" type="text" placeholder="Cellphone"/>
          <button @click="addUser">Add user</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  setup() {
    const email = ref('')
    const password = ref('')
    const access = ref(false)
    const users = ref([])
    const showModal = ref(false)
    const newUser = ref({ name: '', username: '', phone: '' })

    onMounted(() => {
      const session = localStorage.getItem("user_log")
      if (session) {
        access.value = true
        users.value = JSON.parse(session)
      }
    })

    const onSubmit = () => {
      fetch('/users.json')
        .then((res) => res.json())
        .then((data) => {
          const user = data.find(
            (user) => user.email === email.value && user.password === password.value
          )

          if (user) {
            access.value = true
            users.value = data
            localStorage.setItem("user_log", JSON.stringify(data))
          } else {
            alert('Correo o contraseña incorrectos')
          }
        })
        .catch((e) => {
          alert('Ocurrió un error: ' + e.message)
        })
    }

    const deleteUser = (id) => {
      users.value = users.value.filter(user => user.id !== id)
      localStorage.setItem("user_log", JSON.stringify(users.value))
      alert('Usuario eliminado.')
    }

    const editUser = (user) => {

    }

    const addUser = () => {
      const id = users.value.length + 1 
      const user = { ...newUser.value, id }
      users.value.push(user) 
      localStorage.setItem("user_log", JSON.stringify(users.value))
      newUser.value = { name: '', username: '', phone: '' }
      showModal.value = false
      alert('Usuario agregado.')
    }

    const logout = () => {
      access.value = false
      localStorage.removeItem("user_log")
    }

    return {
      email,
      password,
      access,
      onSubmit,
      users,
      deleteUser,
      editUser,
      addUser,
      newUser,
      showModal,
      logout,
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
  background-color: #96faff;
}
button {
  margin-right: 5px;
}

/* Estilos del modal */
/* Se lo pedi al chatgpt pa que se vea perron*/ 
.modal {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background-color: #fff;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  width: 400px;
  max-width: 90%;
  position: relative;
  text-align: center;
}

.close {
  position: absolute;
  top: 10px;
  right: 15px;
  color: #aaa;
  font-size: 24px;
  font-weight: bold;
  cursor: pointer;
}

.close:hover,
.close:focus {
  color: #000;
  text-decoration: none;
  cursor: pointer;
}

.modal-content h2 {
  font-size: 24px;
  margin-bottom: 20px;
  color: #333;
}

.modal-content label {
  display: block;
  font-size: 16px;
  margin-bottom: 8px;
  text-align: left;
}

.modal-content input {
  width: 100%;
  padding: 10px;
  margin-bottom: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
}

.modal-content button {
  background-color: #28a745;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.modal-content button:hover {
  background-color: #218838;
}

.modal-content input:focus {
  border-color: #007bff;
  outline: none;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}

</style>
