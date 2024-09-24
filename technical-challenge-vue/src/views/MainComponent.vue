<template>
  <div class="container-fluid">
    <div class="row">
      <aside class="col-md-3 col-lg-2 sidebar bg-light p-3">
        <h2>Módulos</h2>
        <ul class="list-group">
          <li class="btn btn-primary m-1" @click="selectComponent('Students')">
            <i class="mdi mdi-account" aria-hidden="true"></i> Estudiantes
          </li>
          <li class="btn btn-primary m-1" @click="selectComponent('Courses')">
            <i class="mdi mdi-book" aria-hidden="true"></i> Cursos
          </li>
        </ul>
      </aside>
      <div class="col-md-9 col-lg-10 content p-3">
        <component v-if="currentComponent" :is="currentComponent" />
        <div v-else>
          <i class="mdi mdi-hand-wave-outline" style="font-size: 50px"></i>
          <h3>Buenos días, seleccione un módulo</h3>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Students from '../components/Students.vue'
import Courses from '../components/courses.vue'

const currentComponent = ref(null) //COn esto arreglo el byug incial

function selectComponent(component) {
  currentComponent.value = component === 'Students' ? Students : Courses
}
</script>

<style scoped>
.main-container {
  display: flex;
  height: 600px;
}

.sidebar {
  width: 200px;
  background-color: #f0f0f0;
  padding: 10px;
}

.content {
  flex: 1;
  padding: 20px;
}
</style>
