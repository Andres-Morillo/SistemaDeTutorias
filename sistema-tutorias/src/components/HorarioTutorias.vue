<template>
  <div class="schedule-page">
    <main class="content">
      <header class="topbar">
        <div>
          <span class="eyebrow">Organización semanal</span>
          <h1>Horarios de tutorías</h1>
          <p>
            Consultá y administrá los horarios disponibles para tus estudiantes.
          </p>
        </div>
        <button class="bell" type="button" aria-label="Actualizar horarios" :disabled="isLoading" @click="loadSchedules">
          ↻
        </button>
      </header>

      <section class="overview">
        <article class="overview-card purple">
          <span class="icon">▦</span>
          <div>
            <small>Horarios cargados</small><strong>{{ schedules.length }}</strong
            ><span>esta semana</span>
          </div>
        </article>
        <article class="overview-card green">
          <span class="icon">✓</span>
          <div>
            <small>Materias disponibles</small><strong>{{ subjectCount }}</strong
            ><span>en la agenda</span>
          </div>
        </article>
        <article class="overview-card yellow">
          <span class="icon">◷</span>
          <div>
            <small>Primer horario</small><strong>{{ firstSchedule?.time || '—' }}</strong
            ><span>{{ firstSchedule ? `${firstSchedule.day} · ${firstSchedule.subject}` : 'Sin horarios cargados' }}</span>
          </div>
        </article>
      </section>

      <section class="schedule-card">
        <div class="card-heading">
          <div>
            <span class="section-label">Agenda docente</span>
            <h2>Mis horarios</h2>
          </div>
          <span class="section-label">Datos sincronizados con Supabase</span>
        </div>
        <div class="toolbar">
          <label
            >Buscar materia o curso<input
              v-model="search"
              type="search"
              placeholder="Ej. Robótica" /></label
          ><label
            >Día<select v-model="day">
              <option value="Todos">Todos</option>
              <option>Lunes</option>
              <option>Martes</option>
              <option>Miércoles</option>
              <option>Jueves</option>
              <option>Viernes</option>
            </select></label
          ><button class="filter" type="button" @click="resetFilters">
            Limpiar filtros
          </button>
        </div>
        <div class="table-wrap">
          <table>
            <thead>
              <tr>
                <th>Materia</th>
                <th>Curso</th>
                <th>Día</th>
                <th>Horario</th>
              </tr>
            </thead>
            <tbody>
              <tr v-if="isLoading">
                <td colspan="4" class="empty">Cargando tutorías…</td>
              </tr>
              <tr v-else-if="errorMessage">
                <td colspan="4" class="empty" role="alert">{{ errorMessage }}</td>
              </tr>
              <tr v-for="item in filteredSchedules" :key="item.id">
                <td>
                  <span class="subject">{{ item.subject }}</span>
                </td>
                <td>
                  <b>{{ item.course }}</b>
                </td>
                <td>{{ item.day }}</td>
                <td>
                  <span class="time">{{ item.time }}</span>
                </td>
              </tr>
              <tr v-if="!isLoading && !errorMessage && !filteredSchedules.length">
                <td colspan="4" class="empty">
                  {{ schedules.length ? 'No encontramos tutorías con esos filtros.' : 'Todavía no hay tutorías. Agregá una desde Supabase Studio.' }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </section>

      <footer class="landscape">
        <div class="hill back"></div>
        <div class="hill front"></div>
      </footer>
    </main>
  </div>
</template>

<script setup>
import { computed, onMounted, ref } from 'vue'
import { supabase, supabaseConfigured } from '../lib/supabase'

const search = ref('')
const day = ref('Todos')
const schedules = ref([])
const isLoading = ref(false)
const errorMessage = ref('')
const firstSchedule = computed(() => schedules.value[0])
const subjectCount = computed(() => new Set(schedules.value.map((item) => item.subject)).size)
const filteredSchedules = computed(() =>
  schedules.value.filter(
    (item) =>
      (day.value === "Todos" || item.day === day.value) &&
      `${item.subject} ${item.course}`
        .toLowerCase()
        .includes(search.value.toLowerCase()),
  ),
);
onMounted(loadSchedules)

async function loadSchedules() {
  if (!supabaseConfigured || !supabase) {
    errorMessage.value = 'Falta configurar la URL y la clave publicable de Supabase en .env.local.'
    return
  }

  isLoading.value = true
  errorMessage.value = ''

  try {
    const { data, error } = await supabase
      .from('tutorias')
      .select('id, materia, curso, dia, horario')
      .order('id')

    if (error) {
      console.error('No se pudieron cargar las tutorías desde Supabase.', error)
      errorMessage.value = `No se pudieron cargar las tutorías: ${error.message}`
      schedules.value = []
      return
    }

    schedules.value = data.map((item) => ({
      id: item.id,
      subject: item.materia,
      course: item.curso,
      day: item.dia,
      time: item.horario
    }))
  } catch (error) {
    console.error('No se pudo conectar con Supabase para cargar las tutorías.', error)
    errorMessage.value = 'No se pudo conectar con Supabase. Revisá tu conexión e intentá actualizar.'
    schedules.value = []
  } finally {
    isLoading.value = false
  }
}

function resetFilters() {
  search.value = ''
  day.value = 'Todos'
}
</script>

<style scoped>
:global(*) {
  box-sizing: border-box;
}
:global(body) {
  margin: 0;
  background: #faf5ec;
  color: #231f2c;
  font-family: Arial, Helvetica, sans-serif;
}
.schedule-page {
  min-height: 100vh;
  margin: calc(-1 * clamp(1rem, 3vw, 2rem));
  padding: 38px clamp(20px, 5vw, 64px);
  background: #faf5ec;
}
.sidebar {
  width: 266px;
  min-height: 100vh;
  padding: 24px 18px;
  border-right: 2px solid #231f2c;
  background: #fffdf9;
  display: flex;
  flex-direction: column;
}
.brand {
  display: flex;
  align-items: center;
  gap: 11px;
  color: inherit;
  text-decoration: none;
}
.brand strong {
  display: block;
  font-size: 20px;
}
.brand small,
.profile small {
  display: block;
  margin-top: 3px;
  color: #6b6577;
  font-size: 11px;
}
.logo {
  position: relative;
  width: 43px;
  height: 43px;
  border: 2px solid;
  border-radius: 12px;
  overflow: hidden;
  background: #fff;
}
.person {
  position: absolute;
  bottom: 8px;
  width: 14px;
  height: 14px;
  border-radius: 50%;
}
.person:after {
  content: "";
  position: absolute;
  left: 50%;
  bottom: -10px;
  width: 21px;
  height: 12px;
  transform: translateX(-50%);
  border-radius: 12px 12px 0 0;
}
.person.green {
  left: 7px;
  background: #6b9a55;
}
.person.green:after {
  background: #6b9a55;
}
.person.yellow {
  right: 7px;
  background: #e5b54c;
}
.person.yellow:after {
  background: #e5b54c;
}
.menu {
  display: grid;
  gap: 5px;
  margin-top: 42px;
}
.menu a {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 11px 12px;
  border-radius: 10px;
  color: #6b6577;
  text-decoration: none;
  font-size: 13px;
  font-weight: 600;
}
.menu a:hover,
.menu a.active {
  background: #f4eefb;
  color: #6c4b9c;
}
.contact {
  margin-top: auto;
  padding: 17px;
  border: 1.5px solid;
  border-radius: 13px;
  background: #faf5ec;
  font-size: 12px;
}
.contact p {
  margin: 8px 0;
  color: #6b6577;
}
.profile {
  display: flex;
  align-items: center;
  gap: 9px;
  margin-top: 16px;
  font-size: 12px;
}
.profile > span:last-child {
  margin-left: auto;
  font-size: 20px;
}
.avatar {
  display: grid;
  place-items: center;
  width: 35px;
  height: 35px;
  border: 1.5px solid;
  border-radius: 50%;
  background: #dceef9;
  font-size: 11px;
  font-weight: bold;
}
.content {
  width: min(100%, 1200px);
  margin: 0 auto;
}
.topbar {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}
.eyebrow,
.section-label {
  color: #6c4b9c;
  font-size: 12px;
  font-weight: 700;
}
.topbar h1 {
  margin: 9px 0 5px;
  font-size: 32px;
  font-style: italic;
}
.topbar p {
  margin: 0;
  color: #6b6577;
}
.bell {
  position: relative;
  width: 40px;
  height: 40px;
  border: 1.5px solid;
  border-radius: 50%;
  background: #fffdf9;
  font-size: 20px;
}
.bell b {
  position: absolute;
  right: -4px;
  top: -5px;
  width: 17px;
  height: 17px;
  border: 1px solid;
  border-radius: 50%;
  background: #e5b54c;
  font-size: 10px;
  line-height: 16px;
}
.overview {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  margin-top: 30px;
}
.overview-card {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 17px;
  border: 1.5px solid;
  border-radius: 15px;
}
.overview-card.purple {
  background: #f4eefb;
}
.overview-card.green {
  background: #e2eeda;
}
.overview-card.yellow {
  background: #faebc7;
}
.overview-card .icon {
  display: grid;
  place-items: center;
  width: 39px;
  height: 39px;
  border: 1.5px solid;
  border-radius: 50%;
  font-size: 19px;
}
.overview-card small,
.overview-card span {
  display: block;
  color: #6b6577;
  font-size: 11px;
}
.overview-card strong {
  display: block;
  margin: 3px 0;
  font-size: 25px;
}
.schedule-card {
  margin-top: 24px;
  padding: 25px;
  border: 2px solid #231f2c;
  border-radius: 24px;
  background: #fffdf9;
}
.card-heading {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}
.card-heading h2 {
  margin: 6px 0 0;
  font-size: 21px;
  font-style: italic;
}
.primary {
  padding: 12px 16px;
  border: 1.5px solid;
  border-radius: 10px;
  background: #6b9a55;
  font-weight: 700;
  cursor: pointer;
}
.toolbar {
  display: flex;
  align-items: end;
  gap: 14px;
  margin: 24px 0 18px;
  padding: 15px;
  border-radius: 12px;
  background: #faf5ec;
}
.toolbar label {
  display: flex;
  flex-direction: column;
  gap: 7px;
  color: #6b6577;
  font-size: 11px;
  font-weight: 700;
}
.toolbar input,
.toolbar select {
  height: 38px;
  padding: 0 11px;
  border: 1.5px solid;
  border-radius: 8px;
  background: #fffdf9;
  color: #231f2c;
  font: inherit;
}
.toolbar input {
  width: 220px;
}
.filter {
  height: 38px;
  border: 0;
  background: transparent;
  color: #6c4b9c;
  font-weight: 700;
  cursor: pointer;
}
.table-wrap {
  overflow-x: auto;
}
table {
  width: 100%;
  border-collapse: collapse;
  min-width: 770px;
}
th {
  padding: 12px 10px;
  border-bottom: 1.5px solid #231f2c;
  text-align: left;
  color: #6b6577;
  font-size: 11px;
}
td {
  padding: 16px 10px;
  border-bottom: 1px solid #e7e0d3;
  font-size: 12px;
}
.subject {
  display: flex;
  align-items: center;
  gap: 8px;
  font-weight: 700;
}
.subject i {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  border: 1px solid #231f2c;
}
.green-dot {
  background: #6b9a55;
}
.purple-dot {
  background: #a78bd4;
}
.blue-dot {
  background: #61a9dc;
}
.yellow-dot {
  background: #e5b54c;
}
.pink-dot {
  background: #d78cb2;
}
.time {
  padding: 7px 9px;
  border-radius: 8px;
  background: #f4eefb;
  color: #6c4b9c;
  font-weight: 700;
  font-size: 11px;
}
.students small {
  color: #6b6577;
  font-size: 10px;
}
.status {
  padding: 6px 9px;
  border-radius: 99px;
  font-size: 10px;
  font-weight: 700;
}
.available {
  background: #e2eeda;
  color: #46733c;
}
.full {
  background: #faebc7;
  color: #8a6722;
}
.actions {
  display: flex;
  gap: 5px;
}
.actions button {
  width: 28px;
  height: 28px;
  border: 1px solid;
  border-radius: 7px;
  background: #fffdf9;
  cursor: pointer;
}
.actions button:hover {
  background: #f4eefb;
}
.message,
.empty {
  margin: 18px 0 0;
  color: #6c4b9c;
  font-size: 12px;
  font-weight: 700;
}
.landscape {
  height: 95px;
  position: relative;
  overflow: hidden;
  margin: 28px -46px 0;
  background: #e7ddf6;
  border-top: 2px solid;
  border-radius: 50% 50% 0 0;
}
@media (max-width: 950px) {
  .overview {
    grid-template-columns: 1fr;
  }
  .landscape {
    margin-left: 0;
    margin-right: 0;
  }
}
@media (max-width: 600px) {
  .schedule-page {
    padding: 26px 16px 0;
  }
  .content {
    padding: 0;
  }
  .card-heading {
    display: block;
  }
  .primary {
    margin-top: 17px;
  }
  .toolbar {
    display: block;
  }
  .toolbar label {
    margin-bottom: 12px;
  }
  .toolbar input {
    width: 100%;
  }
  .filter {
    padding: 0;
  }
  .landscape {
    margin-left: 0;
    margin-right: 0;
  }
}

button:focus-visible,
input:focus-visible,
select:focus-visible {
  outline: 3px solid rgb(107 154 85 / 35%);
  outline-offset: 2px;
}
</style>
