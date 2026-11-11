
<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

// DIRECCION DE LA API
const API = import.meta.env.VITE_API_URL + '/api/estudiantes'

// FORMULARIO
const estudiantes = ref({
  nombre: '',
  apellido: '',
  curso: '',
  materia: '',
  nota: ''
})

// LISTADO
const nuevoestudiantes = ref([])

// ESTADOS
const cargando = ref(false)
const guardando = ref(false)

// LIMPIAR FORMULARIO
function limpiarFormulario() {
  estudiantes.value = {
    nombre: '',
    apellido: '',
    curso: '',
    materia: '',
    nota: ''
  }
}

// LISTAR ESTUDIANTES
async function cargar_estudiantes() {
  try {
    cargando.value = true

    let respuesta = await axios.get(API)

    nuevoestudiantes.value = respuesta.data

  } catch (error) {
    console.error('Error al listar:', error)

    alert(
      'Error al cargar estudiantes: ' +
      (error.response?.data?.mensaje || error.message)
    )

  } finally {
    cargando.value = false
  }
}

// GUARDAR ESTUDIANTE
async function guardar() {
  try {
    guardando.value = true

    let respuesta = await axios.post(
      API,
      estudiantes.value
    )

    alert(respuesta.data.mensaje || '¡Guardado con éxito!')

    // Actualizar el listado desde MySQL
    await cargar_estudiantes()

    // Limpiar formulario
    limpiarFormulario()

  } catch (error) {
    console.error('Error al guardar:', error)

    alert(
      'Error al guardar: ' +
      (error.response?.data?.mensaje || error.message)
    )

  } finally {
    guardando.value = false
  }
}

// CARGAR AL ABRIR LA PAGINA
onMounted(() => {
  cargar_estudiantes()
})
</script>

<template>
  <h1>CARGAR DATOS DE ESTUDIANTE</h1>

  <form @submit.prevent="guardar">

    <label>Nombre</label>
    <input
      type="text"
      v-model.trim="estudiantes.nombre"
      required
    >
    <br>

    <label>Apellido</label>
    <input
      type="text"
      v-model.trim="estudiantes.apellido"
      required
    >
    <br>

    <label>Curso</label>
    <input
      type="text"
      v-model.trim="estudiantes.curso"
      required
    >
    <br>

    <label>Materia</label>
    <input
      type="text"
      v-model.trim="estudiantes.materia"
      required
    >
    <br>

    <label>Nota</label>
    <input
      type="number"
      v-model.number="estudiantes.nota"
      min="1"
      max="10"
      step="0.01"
      required
    >
    <br>

    <button type="submit" :disabled="guardando">
      {{ guardando ? 'Guardando...' : 'Guardar alumno' }}
    </button>

  </form>

  <h2>LISTADO DE ALUMNOS</h2>

  <p v-if="cargando">
    Cargando estudiantes...
  </p>

  <p v-else-if="nuevoestudiantes.length === 0">
    No hay estudiantes cargados.
  </p>

  <table v-else border="1">

    <thead>
      <tr>
        <th>Nombre</th>
        <th>Apellido</th>
        <th>Curso</th>
        <th>Materia</th>
        <th>Nota</th>
        <th>Condición</th>
      </tr>
    </thead>

    <tbody>
      <tr v-for="i in nuevoestudiantes" :key="i.id">

        <td>{{ i.nombre }}</td>
        <td>{{ i.apellido }}</td>
        <td>{{ i.curso }}</td>
        <td>{{ i.materia }}</td>
        <td>{{ i.nota }}</td>

        <td v-if="Number(i.nota) >= 7">
          APROBADO
        </td>

        <td v-else>
          DESAPROBADO
        </td>

      </tr>
    </tbody>

  </table>

</template>

<style scoped>
h1, h2 {
  text-align: center;
}

form {
  width: 300px;
  margin: auto;
  padding: 20px;
}

label {
  display: inline-block;
  width: 80px;
  margin: 5px;
}

input {
  margin: 5px;
  padding: 5px;
}

button {
  margin: 10px 5px;
  padding: 8px 15px;
  cursor: pointer;
}

button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}

table {
  margin: 20px auto;
  border-collapse: collapse;
  width: 80%;
}

th, td {
  padding: 10px;
  text-align: center;
}
</style>
