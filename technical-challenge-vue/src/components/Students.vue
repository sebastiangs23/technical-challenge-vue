<template>
  <div class="container mt-4">
    <!-- MODAL -->
    <div v-if="showModal" class="modal show" style="display: block">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Crear Alumno</h5>
            <button type="button" class="btn-close" @click="showModal = false"></button>
          </div>
          <div class="modal-body">
            <input v-model="newStudent.nombres" class="form-control mb-2" placeholder="Nombres" />
            <input
              v-model="newStudent.apellidos"
              class="form-control mb-2"
              placeholder="Apellidos"
            />
            <input v-model="newStudent.correo" class="form-control mb-2" placeholder="Correo" />
            <input v-model="newStudent.codigo" class="form-control mb-2" placeholder="Código" />
            <div v-if="emailError" class="text-danger">
              El correo debe ser @gmail.com o @outlook.com
            </div>
          </div>
          <div class="modal-footer">
            <button class="btn btn-success" @click="createStudent">Guardar</button>
            <button class="btn btn-secondary" @click="showModal = false">Cancelar</button>
          </div>
        </div>
      </div>
    </div>

    <!-- LOS ESTUDIANTES Y EL BUTTON-->
    <h2>Lista de Alumnos</h2>

    <div class="mb-3">
      <button class="btn btn-primary" @click="showModal = true">Crear alumno</button>
    </div>

    <div>
      <ul class="list-group mt-3">
        <li v-for="(student, index) in students" :key="index" class="list-group-item">
          Alumno: {{ student.nombres }} {{ student.apellidos }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

// States
const students = ref([])
const showModal = ref(false)
const newStudent = ref({
  nombres: '',
  apellidos: '',
  correo: '',
  codigo: ''
})
const emailError = ref(false) // Estado para el error de correo

// Mounted
onMounted(async () => {
  console.log('se montooo')
  await getStudents()
})

// Fns
async function getStudents() {
  try {
    const response = await axios.get('https://662aa18dd3f63c12f4583be5.mockapi.io/api/pt/alumnos')
    students.value = response.data
    console.log('los estudiantes', response.data)
  } catch (error) {
    console.log(error)
  }
}

async function createStudent() {
  emailError.value = false

  // Validación del correo
  if (!validateEmail(newStudent.value.correo)) {
    emailError.value = true
    return
  }

  try {
    const response = await axios.post(
      'https://662aa18dd3f63c12f4583be5.mockapi.io/api/pt/alumnos',
      newStudent.value
    )

    students.value.push(response.data)
    showModal.value = false
    newStudent.value = { nombres: '', apellidos: '', correo: '', codigo: '' }
  } catch (error) {
    console.log(error)
  }
}

function validateEmail(email) {
  const regex = /^[a-zA-Z0-9._%+-]+@(gmail\.com|outlook\.com)$/
  return regex.test(email)
}
</script>

<style scoped></style>
