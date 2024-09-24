<template>
  <div>
    <h2 class="mt-4">Lista de Cursos</h2>

    <!-- BOTÓN PARA ABRIR EL MODAL DE CREAR CURSO -->
    <div class="mb-3">
      <button class="btn btn-primary" @click="showCourseModal = true">Crear Curso</button>
    </div>

    <!-- LISTA DE CURSOS -->
    <div class="row">
      <div class="col-md-4" v-for="(course, index) in courses" :key="course.id">
        <div class="card mb-3">
          <div class="card-body">
            <h5 class="card-title">Curso: {{ course.nombre }}</h5>
            <h6 class="card-subtitle mb-2 text-muted">Categoría: {{ course.categoria }}</h6>
            <p class="card-text">
              N° alumnos: {{ course.cantidadAlumnos }} <br />
              Inicio: {{ course.inicio }} <br />
              Fin: {{ course.fin }} <br />
              Precio: {{ course.precio }}
            </p>
            <button class="btn btn-info" @click="toggleDescription(index)">
              {{ showDescription[index] ? 'Ocultar Descripción' : 'Mostrar Descripción' }}
            </button>
            <button class="btn btn-secondary ms-2" @click="toggleStudents(index)">
              {{ showStudents[index] ? 'Ocultar Estudiantes' : 'Mostrar Estudiantes' }}
            </button>
            <button class="btn btn-warning ms-2" @click="openEditCourseModal(course)">
              Editar Curso
            </button>
            <div v-if="showDescription[index]" class="mt-2">
              <p><strong>Descripción:</strong> {{ course.descripcion }}</p>
            </div>
            <div v-if="showStudents[index]" class="mt-2">
              <strong>Estudiantes:</strong>
              <ul>
                <li v-for="student in course.alumnos" :key="student.id">
                  {{ student.nombres }} {{ student.apellidos }} ({{ student.codigo }})
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL PARA CREAR CURSO -->
    <div v-if="showCourseModal" class="modal show" style="display: block">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Crear Curso</h5>
            <button type="button" class="btn-close" @click="showCourseModal = false"></button>
          </div>
          <div class="modal-body">
            <h6 class="mt-3">Nombre curso:</h6>
            <input
              v-model="newCourse.nombre"
              class="form-control mb-2"
              placeholder="Nombre del Curso"
            />
            <h6 class="mt-3">Categoría:</h6>
            <input
              v-model="newCourse.categoria"
              class="form-control mb-2"
              placeholder="Categoría"
            />
            <h6 class="mt-3">Descripción:</h6>
            <textarea
              v-model="newCourse.descripcion"
              class="form-control mb-2"
              placeholder="Descripción"
            ></textarea>
            <h6 class="mt-3">Cantidad de alumnos</h6>
            <input
              v-model="newCourse.cantidadAlumnos"
              type="number"
              class="form-control mb-2"
              placeholder="Cantidad de Alumnos"
            />
            <h6 class="mt-3">Inicio:</h6>
            <input v-model="newCourse.inicio" type="date" class="form-control mb-2" />
            <h6 class="mt-3">Fin:</h6>
            <input v-model="newCourse.fin" type="date" class="form-control mb-2" />
            <h6 class="mt-3">Precio en soles:</h6>
            <input
              v-model="newCourse.precio"
              type="number"
              class="form-control mb-2"
              placeholder="Precio"
            />
            <h6 class="mt-3">Selecciona Estudiantes:</h6>
            <div v-for="student in students" :key="student.id">
              <input type="checkbox" :value="student" v-model="newCourse.alumnos" />
              {{ student.nombres }} {{ student.apellidos }}
            </div>
          </div>
          <div class="modal-footer">
            <button class="btn btn-success" @click="createCourse">Guardar</button>
            <button class="btn btn-secondary" @click="showCourseModal = false">Cancelar</button>
          </div>
        </div>
      </div>
    </div>

    <!-- MODAL PARA EDITAR CURSO -->
    <div v-if="showEditModal" class="modal show" style="display: block">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Editar Curso</h5>
            <button type="button" class="btn-close" @click="showEditModal = false"></button>
          </div>
          <div class="modal-body">
            <h6 class="mt-3">Nombre curso:</h6>
            <input
              v-model="selectedCourse.nombre"
              class="form-control mb-2"
              placeholder="Nombre del Curso"
            />
            <h6 class="mt-3">Categoría:</h6>
            <input
              v-model="selectedCourse.categoria"
              class="form-control mb-2"
              placeholder="Categoría"
            />
            <h6 class="mt-3">Descripción:</h6>
            <textarea
              v-model="selectedCourse.descripcion"
              class="form-control mb-2"
              placeholder="Descripción"
            ></textarea>
            <h6 class="mt-3">Selecciona Estudiantes para añadir:</h6>
            <div v-for="student in students" :key="student.id">
              <input type="checkbox" :value="student" v-model="selectedCourse.alumnos" />
              {{ student.nombres }} {{ student.apellidos }}
            </div>
          </div>
          <div class="modal-footer">
            <button class="btn btn-success" @click="editCourse">Guardar Cambios</button>
            <button class="btn btn-secondary" @click="showEditModal = false">Cancelar</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

// States
const courses = ref([])
const students = ref([])
const showDescription = ref([])
const showStudents = ref([])
const showCourseModal = ref(false)
const showEditModal = ref(false)
const newCourse = ref({
  nombre: '',
  categoria: '',
  descripcion: '',
  cantidadAlumnos: 0,
  inicio: '',
  fin: '',
  precio: 0,
  alumnos: []
})
const selectedCourse = ref({})

// Mounted
onMounted(async () => {
  await getAllCourses()
  await getStudents()
})

// Fns
async function getAllCourses() {
  try {
    const response = await axios.get('https://662aa18dd3f63c12f4583be5.mockapi.io/api/pt/cursos')
    courses.value = response.data
    showDescription.value = new Array(response.data.length).fill(false)
    showStudents.value = new Array(response.data.length).fill(false)
  } catch (error) {
    console.log(error)
  }
}

async function getStudents() {
  try {
    const response = await axios.get('https://662aa18dd3f63c12f4583be5.mockapi.io/api/pt/alumnos')
    students.value = response.data
    console.log('los estudiantes', response.data)
  } catch (error) {
    console.log(error)
  }
}

async function createCourse() {
  try {
    const response = await axios.post(
      'https://662aa18dd3f63c12f4583be5.mockapi.io/api/pt/cursos',
      newCourse.value
    )
    courses.value.push(response.data)
    showCourseModal.value = false
    newCourse.value = {
      nombre: '',
      categoria: '',
      descripcion: '',
      cantidadAlumnos: 0,
      inicio: '',
      fin: '',
      precio: 0,
      alumnos: []
    }
  } catch (error) {
    console.log(error)
  }
}

function openEditCourseModal(course) {
  selectedCourse.value = { ...course }
  showEditModal.value = true
}

async function editCourse() {
  try {
    const response = await axios.put(
      `https://662aa18dd3f63c12f4583be5.mockapi.io/api/pt/cursos/${selectedCourse.value.id}`,
      selectedCourse.value
    )
    const index = courses.value.findIndex((course) => course.id === selectedCourse.value.id)
    courses.value[index] = response.data
    showEditModal.value = false
  } catch (error) {
    console.log(error)
  }
}

function toggleDescription(index) {
  showDescription.value[index] = !showDescription.value[index]
}

function toggleStudents(index) {
  showStudents.value[index] = !showStudents.value[index]
}
</script>

<style scoped>
.card {
  cursor: pointer;
}
</style>
