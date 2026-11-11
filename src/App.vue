
<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

let estudiantes = ref({
  nombre: "",
  apellido: "",
  curso: "",
  materia: "",
  nota: ""
})


let nuevoestudiantes = ref([])

async function cargar_estudiantes() {
  try {
    let respuesta = await axios.get("http://localhost:3000/api/estudiantes")
    nuevoestudiantes.value= respuesta.data;

  } catch (error) {
    alert("Error al guardar: " + error.message)
  }
}

onMounted(()=>
  cargar_estudiantes()
)

async function guardar() {
  try {
    let respuesta = await axios.post("http://localhost:3000/api/estudiantes",estudiantes.value)

    alert("¡Guardado con éxito!")

    
    nuevoestudiantes.value.push({
      ...estudiantes.value,
      id: respuesta.data.id
    })

    
    estudiantes.value = {
      nombre: "",
      apellido: "",
      curso: "",
      materia: "",
      nota: ""
    }

  } catch (error) {
    alert("Error al guardar: " + error.message)
  }
}
</script>

<template>
  <h1>CARGAR DATOS DE ESTUDIANTE</h1>

  <form @submit.prevent="guardar">

    <label>Nombre</label>
    <input type="text" v-model="estudiantes.nombre" required>
    <br>

    <label>Apellido</label>
    <input type="text" v-model="estudiantes.apellido" required>
    <br>

    <label>Curso</label>
    <input type="text" v-model="estudiantes.curso" required>
    <br>

    <label>Materia</label>
    <input type="text" v-model="estudiantes.materia" required>
    <br>

    <label>Nota</label>
    <input type="number" v-model.number="estudiantes.nota" min="1" max="10" required>
    <br>

    <input type="submit" value="Guardar alumno">

  </form>

  <h2>LISTADO DE ALUMNOS</h2>

  <table border="1">
    <tr>
      <th>Nombre</th>
      <th>Apellido</th>
      <th>Curso</th>
      <th>Materia</th>
      <th>Nota</th>
      <th>Condición</th>
    </tr>

    <tr v-for="i in nuevoestudiantes" :key="i.id">
      <td>{{ i.nombre }}</td>
      <td>{{ i.apellido }}</td>
      <td>{{ i.curso }}</td>
      <td>{{ i.materia }}</td>
      <td>{{ i.nota }}</td>

      <td v-if="i.nota >= 7">
        APROBADO
      </td>

      <td v-else>
        DESAPROBADO
      </td>
    </tr>

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
