<template>
  <section class="citations-page" aria-labelledby="citations-title">
    <div class="citations-shell">
      <header class="page-heading">
        <div>
          <p class="eyebrow">ACOMPAÑAMIENTO ESCOLAR</p>
          <h1 id="citations-title">Mis citaciones</h1>
          <p class="subtitle">Consultá y filtrá las citaciones realizadas.</p>
        </div>
        <span class="heading-mark" aria-hidden="true">▤</span>
      </header>

      <section class="filters-section" aria-labelledby="filters-title">
        <div class="section-heading">
          <h2 id="filters-title">Filtrar citaciones</h2>
          <button class="clear-filters" type="button" @click="clearFilters">
            Limpiar filtros
          </button>
        </div>

        <div class="filter-grid">
          <label class="filter-control">
            <span>Fecha</span>
            <select v-model="filters.date" :class="{ 'is-active': filters.date !== 'all' }">
              <option value="all">Todas</option>
              <option v-for="date in filterOptions.dates" :key="date" :value="date">
                {{ formatDate(date) }}
              </option>
            </select>
          </label>

          <label class="filter-control">
            <span>Curso</span>
            <select v-model="filters.course" :class="{ 'is-active': filters.course !== 'all' }">
              <option value="all">Todos</option>
              <option v-for="course in filterOptions.courses" :key="course" :value="course">
                {{ course }}
              </option>
            </select>
          </label>

          <label class="filter-control">
            <span>Materia</span>
            <select v-model="filters.subject" :class="{ 'is-active': filters.subject !== 'all' }">
              <option value="all">Todas</option>
              <option v-for="subject in filterOptions.subjects" :key="subject" :value="subject">
                {{ subject }}
              </option>
            </select>
          </label>

          <label class="filter-control">
            <span>Docente</span>
            <select v-model="filters.teacher" :class="{ 'is-active': filters.teacher !== 'all' }">
              <option value="all">Todos</option>
              <option v-for="teacher in filterOptions.teachers" :key="teacher" :value="teacher">
                {{ teacher }}
              </option>
            </select>
          </label>

          <label class="filter-control">
            <span>Estudiantes</span>
            <select v-model="filters.student" :class="{ 'is-active': filters.student !== 'all' }">
              <option value="all">Todos</option>
              <option v-for="student in filterOptions.students" :key="student" :value="student">
                {{ student }}
              </option>
            </select>
          </label>

          <label class="filter-control">
            <span>Estado</span>
            <select v-model="filters.status" :class="{ 'is-active': filters.status !== 'all' }">
              <option value="all">Todos</option>
              <option v-for="status in filterOptions.statuses" :key="status" :value="status">
                {{ status }}
              </option>
            </select>
          </label>
        </div>
      </section>

      <section class="results-section" aria-labelledby="results-title">
        <div class="section-heading results-heading">
          <div>
            <h2 id="results-title">Citaciones realizadas</h2>
            <p class="results-count">
              {{ filteredCitations.length }} {{ filteredCitations.length === 1 ? 'resultado' : 'resultados' }}
            </p>
          </div>
          <span class="page-indicator">{{ currentPage }} / {{ totalPages }}</span>
        </div>

        <div class="table-scroll">
          <table class="citations-table">
            <thead>
              <tr>
                <th scope="col">Fecha</th>
                <th scope="col">Hora</th>
                <th scope="col">Curso</th>
                <th scope="col">Materia</th>
                <th scope="col">Estudiantes</th>
                <th scope="col">Motivo</th>
                <th scope="col">Estado</th>
                <th scope="col">Notificación</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="citation in paginatedCitations" :key="citation.id">
                <td class="date-cell">{{ formatDate(citation.date) }}</td>
                <td>{{ citation.time }}</td>
                <td>{{ citation.course }}</td>
                <td>{{ citation.subject }}</td>
                <td>
                  <span class="student-count">{{ citation.students.length }}</span>
                  {{ citation.students.length === 1 ? 'citado' : 'citados' }}
                </td>
                <td class="reason-cell">{{ citation.reason }}</td>
                <td>
                  <span class="status-badge" :class="statusClass(citation.status)">
                    {{ citation.status }}
                  </span>
                </td>
                <td class="notification-cell">
                  <button
                    class="view-button"
                    type="button"
                    :aria-expanded="activeNotificationId === citation.id"
                    @click="toggleNotification(citation.id)"
                  >
                    Ver
                  </button>
                  <div
                    v-if="activeNotificationId === citation.id"
                    class="notification-popover"
                    role="status"
                  >
                    Sin detalles adicionales.
                  </div>
                </td>
              </tr>
              <tr v-if="paginatedCitations.length === 0">
                <td class="empty-state" colspan="8">
                  No hay citaciones que coincidan con esos filtros.
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <nav class="pagination" aria-label="Paginación de citaciones">
          <button
            class="page-button page-direction"
            type="button"
            :disabled="currentPage === 1"
            @click="goToPage(currentPage - 1)"
          >
            ‹ Anterior
          </button>

          <button
            v-for="page in totalPages"
            :key="page"
            class="page-button page-number"
            :class="{ 'is-current': currentPage === page }"
            type="button"
            :aria-current="currentPage === page ? 'page' : undefined"
            @click="goToPage(page)"
          >
            {{ page }}
          </button>

          <button
            class="page-button page-direction"
            type="button"
            :disabled="currentPage === totalPages"
            @click="goToPage(currentPage + 1)"
          >
            Siguiente ›
          </button>
        </nav>
      </section>
    </div>
  </section>
</template>

<script setup>
import { computed, reactive, ref, watch } from 'vue'

const pageSize = 5
const currentPage = ref(1)
const activeNotificationId = ref(null)

const citations = [
  { id: 1, date: '2026-10-02', time: '09:00', course: '4°', subject: 'Programación', teacher: 'Juan Pérez', students: ['María Gómez', 'Lucas Díaz'], reason: 'Recuperación de contenido', status: 'Pendiente', notification: 'Email' },
  { id: 2, date: '2026-10-01', time: '10:30', course: '3°', subject: 'Matemática', teacher: 'Ana Torres', students: ['Sofía López'], reason: 'Seguimiento de trabajos', status: 'Realizada', notification: 'WhatsApp' },
  { id: 3, date: '2026-09-30', time: '11:00', course: '4°', subject: 'Programación', teacher: 'Juan Pérez', students: ['Lucas Díaz', 'Tomás Ruiz', 'Malena Sosa'], reason: 'Recuperación de contenido', status: 'Pendiente', notification: 'Ambos' },
  { id: 4, date: '2026-09-29', time: '08:15', course: '2°', subject: 'Lengua', teacher: 'Carla Méndez', students: ['Bruno Acosta'], reason: 'Inasistencias reiteradas', status: 'Realizada', notification: 'Email' },
  { id: 5, date: '2026-09-28', time: '13:00', course: '5°', subject: 'Física', teacher: 'Diego Romero', students: ['Valentina Cruz', 'Julián Paz'], reason: 'Evaluación pendiente', status: 'Anulada', notification: 'WhatsApp' },
  { id: 6, date: '2026-09-25', time: '09:45', course: '4°', subject: 'Programación', teacher: 'Juan Pérez', students: ['María Gómez'], reason: 'Revisión de carpeta', status: 'Realizada', notification: 'Ambos' },
  { id: 7, date: '2026-09-24', time: '10:00', course: '1°', subject: 'Historia', teacher: 'Ana Torres', students: ['Nicolás Vera', 'Emma Gil'], reason: 'Acompañamiento académico', status: 'Pendiente', notification: 'Email' },
  { id: 8, date: '2026-09-23', time: '12:15', course: '3°', subject: 'Matemática', teacher: 'Ana Torres', students: ['Sofía López', 'Mateo Díaz'], reason: 'Seguimiento de trabajos', status: 'Realizada', notification: 'WhatsApp' },
  { id: 9, date: '2026-09-22', time: '08:30', course: '2°', subject: 'Lengua', teacher: 'Carla Méndez', students: ['Bruno Acosta', 'Lola Ríos', 'Pedro Luna'], reason: 'Recuperación de contenido', status: 'Pendiente', notification: 'Ambos' },
  { id: 10, date: '2026-09-21', time: '11:30', course: '5°', subject: 'Física', teacher: 'Diego Romero', students: ['Valentina Cruz'], reason: 'Consulta por evaluación', status: 'Realizada', notification: 'Email' },
  { id: 11, date: '2026-09-18', time: '09:15', course: '6°', subject: 'Historia', teacher: 'Ana Torres', students: ['Emma Gil'], reason: 'Acompañamiento académico', status: 'Anulada', notification: 'WhatsApp' },
  { id: 12, date: '2026-09-17', time: '12:00', course: '4°', subject: 'Programación', teacher: 'Juan Pérez', students: ['Tomás Ruiz', 'Malena Sosa'], reason: 'Entrega de proyecto', status: 'Pendiente', notification: 'Ambos' }
]

const filters = reactive({
  date: 'all',
  course: 'all',
  subject: 'all',
  teacher: 'all',
  student: 'all',
  status: 'all'
})

const filterOptions = {
  dates: uniqueValues('date'),
  courses: uniqueValues('course'),
  subjects: uniqueValues('subject'),
  teachers: uniqueValues('teacher'),
  students: uniqueValues('students'),
  statuses: uniqueValues('status')
}

const filteredCitations = computed(() => citations.filter((citation) => {
  return (filters.date === 'all' || citation.date === filters.date)
    && (filters.course === 'all' || citation.course === filters.course)
    && (filters.subject === 'all' || citation.subject === filters.subject)
    && (filters.teacher === 'all' || citation.teacher === filters.teacher)
    && (filters.student === 'all' || citation.students.includes(filters.student))
    && (filters.status === 'all' || citation.status === filters.status)
}))

const totalPages = computed(() => Math.max(1, Math.ceil(filteredCitations.value.length / pageSize)))
const paginatedCitations = computed(() => {
  const start = (currentPage.value - 1) * pageSize
  return filteredCitations.value.slice(start, start + pageSize)
})

watch(filters, () => {
  currentPage.value = 1
  activeNotificationId.value = null
})

watch(totalPages, (pageCount) => {
  if (currentPage.value > pageCount) currentPage.value = pageCount
})

function uniqueValues(key) {
  const values = citations.flatMap((citation) => key === 'students' ? citation.students : [citation[key]])
  return [...new Set(values)].sort((first, second) => first.localeCompare(second, 'es'))
}

function formatDate(value) {
  const [year, month, day] = value.split('-').map(Number)
  return new Intl.DateTimeFormat('es-AR', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric'
  }).format(new Date(year, month - 1, day))
}

function statusClass(status) {
  return `status-${status.toLocaleLowerCase('es').replace('á', 'a')}`
}

function goToPage(page) {
  if (page < 1 || page > totalPages.value) return
  currentPage.value = page
  activeNotificationId.value = null
}

function toggleNotification(id) {
  activeNotificationId.value = activeNotificationId.value === id ? null : id
}

function clearFilters() {
  Object.assign(filters, {
    date: 'all',
    course: 'all',
    subject: 'all',
    teacher: 'all',
    student: 'all',
    status: 'all'
  })
}
</script>

<style scoped>
.citations-page {
  min-height: 100vh;
  padding: clamp(24px, 4vw, 48px) 20px;
  background: #faf5ec;
  color: #231f2c;
  font-family: Arial, Helvetica, sans-serif;
}

.citations-shell {
  width: min(100%, 1240px);
  margin: 0 auto;
}

.page-heading,
.section-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
}

.page-heading {
  padding-bottom: 24px;
  border-bottom: 1px solid rgb(35 31 44 / 14%);
}

.eyebrow {
  margin: 0 0 8px;
  color: #6b9a55;
  font-size: 12px;
  font-weight: 700;
}

.page-heading h1 {
  margin: 0;
  font-size: 32px;
  font-style: italic;
  line-height: 1.15;
}

.subtitle,
.results-count {
  margin: 8px 0 0;
  color: #6b6577;
  font-size: 14px;
}

.heading-mark {
  width: 42px;
  height: 42px;
  flex: 0 0 42px;
  display: grid;
  place-items: center;
  border: 1.5px solid #231f2c;
  border-radius: 50%;
  background: #faebc7;
  font-size: 22px;
}

.filters-section,
.results-section {
  padding-top: 24px;
}

.section-heading h2 {
  margin: 0;
  font-size: 18px;
  font-weight: 700;
}

.clear-filters {
  padding: 7px 10px;
  border: 0;
  border-radius: 7px;
  background: transparent;
  color: #6b6577;
  font: inherit;
  font-size: 13px;
  cursor: pointer;
}

.clear-filters:hover {
  background: #faebc7;
  color: #231f2c;
}

.filter-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 14px 18px;
  margin-top: 16px;
}

.filter-control {
  min-width: 0;
  display: flex;
  flex-direction: column;
  gap: 7px;
}

.filter-control > span {
  color: #6b6577;
  font-size: 12px;
  font-weight: 700;
}

.filter-control select {
  width: 100%;
  min-height: 42px;
  padding: 0 34px 0 12px;
  border: 1px solid #c9c2b7;
  border-radius: 8px;
  appearance: auto;
  background: #fffdf9;
  color: #231f2c;
  font: inherit;
  font-size: 14px;
  cursor: pointer;
}

.filter-control select:focus-visible {
  outline: 3px solid rgb(107 154 85 / 18%);
  border-color: #6b9a55;
}

.filter-control select.is-active {
  border-color: #d3a63d;
  background: #faebc7;
}

.results-heading {
  margin-bottom: 14px;
}

.page-indicator {
  padding: 5px 10px;
  border-radius: 999px;
  background: #fffdf9;
  color: #6b6577;
  font-size: 12px;
  font-weight: 700;
}

.table-scroll {
  width: 100%;
  overflow-x: auto;
  border: 1px solid rgb(35 31 44 / 14%);
  border-radius: 10px;
  background: #fffdf9;
}

.citations-table {
  width: 100%;
  min-width: 980px;
  border-collapse: collapse;
  text-align: left;
}

.citations-table th,
.citations-table td {
  padding: 13px 12px;
  border-bottom: 1px solid rgb(35 31 44 / 9%);
  font-size: 13px;
  vertical-align: middle;
}

.citations-table th {
  background: #f5efe4;
  color: #5f5965;
  font-size: 12px;
  font-weight: 700;
  white-space: nowrap;
}

.citations-table tbody tr:last-child td {
  border-bottom: 0;
}

.citations-table tbody tr:hover {
  background: #fffaf0;
}

.date-cell {
  white-space: nowrap;
  font-variant-numeric: tabular-nums;
}

.student-count {
  display: inline-grid;
  min-width: 23px;
  height: 23px;
  margin-right: 4px;
  place-items: center;
  border-radius: 50%;
  background: #e8efe2;
  color: #46663a;
  font-weight: 700;
}

.reason-cell {
  min-width: 170px;
}

.status-badge {
  display: inline-block;
  padding: 5px 9px;
  border-radius: 999px;
  font-size: 11px;
  font-weight: 700;
  white-space: nowrap;
}

.status-pendiente {
  background: #faebc7;
  color: #765915;
}

.status-realizada {
  background: #e2eeda;
  color: #45683b;
}

.status-anulada {
  background: #f6dfdc;
  color: #8a4942;
}

.notification-cell {
  position: relative;
}

.view-button {
  padding: 5px 10px;
  border: 1px solid #d5cdbf;
  border-radius: 7px;
  background: #fffdf9;
  color: #4e9cd4;
  font: inherit;
  font-size: 12px;
  font-weight: 700;
  cursor: pointer;
}

.view-button:hover,
.view-button[aria-expanded='true'] {
  border-color: #d3a63d;
  background: #faebc7;
  color: #231f2c;
}

.notification-popover {
  position: absolute;
  z-index: 2;
  top: calc(100% + 4px);
  right: 0;
  width: max-content;
  max-width: 210px;
  padding: 9px 11px;
  border: 1px solid rgb(35 31 44 / 16%);
  border-radius: 8px;
  background: #fffdf9;
  box-shadow: 0 6px 18px rgb(35 31 44 / 12%);
  color: #6b6577;
  font-size: 12px;
}

.empty-state {
  padding: 30px 16px !important;
  color: #6b6577;
  text-align: center;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  gap: 8px;
  padding-top: 20px;
}

.page-button {
  min-width: 38px;
  height: 38px;
  padding: 0 11px;
  border: 1px solid #d2cabb;
  border-radius: 8px;
  background: #fffdf9;
  color: #393441;
  font: inherit;
  font-size: 13px;
  cursor: pointer;
}

.page-button:hover:not(:disabled) {
  border-color: #d3a63d;
  background: #fff5d8;
}

.page-button.is-current {
  border-color: #d3a63d;
  background: #f0cf78;
  font-weight: 700;
}

.page-button:disabled {
  opacity: 0.45;
  cursor: not-allowed;
}

.page-direction {
  min-width: 96px;
}

@media (max-width: 760px) {
  .citations-page {
    padding-right: 16px;
    padding-left: 16px;
  }

  .filter-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 420px) {
  .page-heading h1 {
    font-size: 28px;
  }

  .filter-grid {
    grid-template-columns: 1fr;
  }

  .section-heading {
    align-items: flex-start;
  }

  .clear-filters {
    padding-right: 0;
  }
}
</style>
