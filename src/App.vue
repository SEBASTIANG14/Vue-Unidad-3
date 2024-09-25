<template>
  <div>
    <h1>Login Perrón</h1>
    <form @submit.prevent="onSubmit" v-if="!access">
      <fieldset>
        <label>Usuario</label>
        <input v-model="username" type="text" placeholder="Escribe tu usuario TMDB aquí" required />
      </fieldset>
      <fieldset>
        <label>Contraseña</label>
        <input v-model="password" type="password" placeholder="Escribe tu contraseña aquí" required />
      </fieldset>
      <button type="submit">Iniciar sesión</button>
    </form>

    <div class="bienvenida" v-if="access">
      <h1>Hola, bienvenido!</h1>
      <button @click="logout" class="logout-btn">Cerrar sesión</button>

      <div class="movies">
        <h2>Películas Populares</h2>
        <div class="movie-list">
          <div v-for="movie in movies" :key="movie.id" class="movie-item">
            <h3>{{ movie.title }}</h3>
            <p>{{ movie.overview }}</p>
            <img :src="'https://image.tmdb.org/t/p/w500' + movie.poster_path" alt="Poster de {{ movie.title }}">

            <div class="rating-section">
              <label for="rating">Calificación (10-100):</label>
              <input 
                type="number" 
                v-model.number="movie.userRating" 
                min="10" 
                max="100" 
                step="10" 
              />
              <button @click="rateMovie(movie.id, movie.userRating)" class="rate-btn">Agregar calificación</button>
              <button @click="removeRate(movie.id)" class="remove-rate-btn">Eliminar calificación</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const username = ref('')
    const password = ref('')
    const access = ref(false)
    const movies = ref([])
    const apiKey = "7dbf2be7efbc38c8a5ba78d99ae9f933"
    let requestToken = ref('')

    const session_id = ref(localStorage.getItem('session_id'))

    const getRequestToken = () => {
      return fetch(`https://api.themoviedb.org/3/authentication/token/new?api_key=${apiKey}`)
        .then(response => response.json())
        .then(data => {
          requestToken.value = data.request_token
        })
        .catch(error => {
          console.error('Error al obtener el request token:', error)
        })
    }

    const onSubmit = () => {
      getRequestToken().then(() => {
        const loginData = JSON.stringify({
          username: username.value,
          password: password.value,
          request_token: requestToken.value
        })

        fetch(`https://api.themoviedb.org/3/authentication/token/validate_with_login?api_key=${apiKey}`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: loginData
        })
          .then(response => response.json())
          .then(result => {
            if (result.success) {
              createSession()
            } else {
              alert('Usuario o contraseña incorrectos')
            }
          })
          .catch(error => {
            console.error('Error en la autenticación:', error)
          })
      })
    }

    const createSession = () => {
      const sessionData = JSON.stringify({
        request_token: requestToken.value
      })

      fetch(`https://api.themoviedb.org/3/authentication/session/new?api_key=${apiKey}`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: sessionData
      })
        .then(response => response.json())
        .then(result => {
          if (result.success) {
            access.value = true
            session_id.value = result.session_id
            localStorage.setItem('session_id', session_id.value)
            fetchMovies()
          } else {
            alert('No se pudo crear la sesión')
          }
        })
        .catch(error => {
          console.error('Error al crear la sesión:', error)
        })
    }

    const fetchMovies = () => {
      const url = `https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}&language=es-MX&page=1`
      fetch(url)
        .then((res) => res.json())
        .then((data) => {
          movies.value = data.results.map(movie => ({
            ...movie,
            userRating: 10 
          }))
        })
        .catch((e) => {
          console.error('Error al obtener las películas:', e)
        })
    }

    const rateMovie = (movieId, rating) => {
      if (rating < 10 || rating > 100) {
        alert('La calificación debe estar entre 10 y 100')
        return
      }

      const rateData = JSON.stringify({
        value: rating / 10 
      })

      fetch(`https://api.themoviedb.org/3/movie/${movieId}/rating?api_key=${apiKey}&session_id=${session_id.value}`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: rateData
      })
        .then(response => response.json())
        .then(result => {
          if (result.success) {
            alert('Calificación agregada con éxito')
          } else {
            alert('No se pudo agregar la calificación')
          }
        })
        .catch(error => {
          console.error('Error al agregar la calificación:', error)
        })
    }

    const removeRate = (movieId) => {
      fetch(`https://api.themoviedb.org/3/movie/${movieId}/rating?api_key=${apiKey}&session_id=${session_id.value}`, {
        method: 'DELETE'
      })
        .then(response => response.json())
        .then(result => {
          if (result.success) {
            alert('Calificación eliminada con éxito')
          } else {
            alert('No se pudo eliminar la calificación')
          }
        })
        .catch(error => {
          console.error('Error al eliminar la calificación:', error)
        })
    }

    const logout = () => {
      access.value = false
      localStorage.removeItem("session_id")
    }

    return {
      username,
      password,
      access,
      onSubmit,
      logout,
      movies,
      rateMovie,
      removeRate
    }
  }
}
</script>

<style>
.movie-list {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.movie-item {
  border: 1px solid #ccc;
  padding: 10px;
  width: 200px;
  text-align: center;
}

.movie-item img {
  max-width: 100%;
  height: auto;
}

.rating-section {
  margin-top: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.rating-section input {
  width: 60px;
  margin-bottom: 10px;
  padding: 5px;
  text-align: center;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.rating-section button {
  margin: 5px 0;
  padding: 5px 10px;
  background-color: #7dbcff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.rating-section button:hover {
  background-color: #0056b3;
}

.rate-btn {
  background-color: #28a745;
}

.remove-rate-btn {
  background-color: #dc3545;
}

.logout-btn {
  background-color: #343a40;
  color: white;
  padding: 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.logout-btn:hover {
  background-color: #23272b;
}
</style>
