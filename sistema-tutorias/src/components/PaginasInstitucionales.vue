<template>
  <section class="portal-page">
    <main class="portal-content">
      <header class="page-heading">
        <div>
          <p class="eyebrow">{{ roleName }} · {{ page === 'inicio' ? 'Panel principal' : 'Gestión institucional' }}</p>
          <h1>{{ pageTitle }}</h1>
          <p class="subtitle">{{ pageDescription }}</p>
        </div>
        <span class="heading-mark" aria-hidden="true">{{ page === 'inicio' ? '⌂' : '▤' }}</span>
      </header>

      <template v-if="page === 'inicio' && role === 'estudiante'">
        <section class="stats-grid student-stats" aria-label="Resumen académico">
          <article class="stat-card green"><span class="stat-icon">✓</span><small>Asistencia del mes</small><strong>92%</strong><span>Muy buen ritmo</span></article>
          <article class="stat-card purple"><span class="stat-icon">▣</span><small>Tutorías próximas</small><strong>2</strong><span>Esta semana</span></article>
          <article class="stat-card yellow"><span class="stat-icon">▤</span><small>Materias en seguimiento</small><strong>1</strong><span>Matemática</span></article>
        </section>
        <section class="portal-card">
          <div class="card-heading"><div><span class="section-label">Agenda</span><h2>Próximas tutorías</h2></div></div>
          <div v-for="item in studentAgenda" :key="item.id" class="list-row">
            <span class="row-icon" :class="item.color">{{ item.icon }}</span>
            <span class="row-copy"><strong>{{ item.title }}</strong><small>{{ item.detail }}</small></span>
            <span class="row-date">{{ item.date }}</span>
          </div>
          <p class="sample-note">Información de ejemplo para la vista previa del sistema.</p>
        </section>
      </template>

      <template v-else-if="page === 'inicio'">
        <section class="stats-grid">
          <article v-for="stat in dashboard.stats" :key="stat.label" class="stat-card" :class="stat.color">
            <span class="stat-icon">{{ stat.icon }}</span>
            <small>{{ stat.label }}</small>
            <strong>{{ stat.value }}</strong>
            <span>{{ stat.note }}</span>
          </article>
        </section>
        <section class="portal-card dashboard-card">
          <div class="card-heading">
            <div><span class="section-label">Accesos directos</span><h2>¿Qué necesitás gestionar?</h2></div>
          </div>
          <div class="quick-grid">
            <button
              v-for="item in dashboard.links"
              :key="item.id"
              class="quick-card"
              :class="item.color"
              type="button"
              @click="emit('navigate', item.id)"
            >
              <span>{{ item.icon }}</span><strong>{{ item.label }}</strong><small>{{ item.description }}</small>
            </button>
          </div>
          <p class="sample-note">Los indicadores y registros de este panel son datos de muestra.</p>
        </section>
      </template>

      <template v-else-if="page.startsWith('estudiantes-') || page.startsWith('profesores-')">
        <section class="portal-card">
          <div class="card-heading">
            <div><span class="section-label">Listado institucional</span><h2>{{ pageTitle }} registrados</h2></div>
            <span class="count-badge">{{ filteredRecords.length }} registros</span>
          </div>
          <div class="toolbar">
            <label class="search-box">
              <span aria-hidden="true">⌕</span>
              <input v-model="search" type="search" :aria-label="`Buscar ${pageTitle.toLocaleLowerCase('es')}`" :placeholder="`Buscar ${pageTitle.toLocaleLowerCase('es')}`" />
            </label>
            <label class="filter-field">
              <span>Año</span>
              <select v-model="yearFilter">
                <option value="Todos">Todos</option>
                <option v-for="year in years" :key="year" :value="year">{{ year }}°</option>
              </select>
            </label>
          </div>
          <div class="table-scroll">
            <table>
              <thead><tr><th>Nombre</th><th v-if="isStudentList">Año / curso</th><th v-else>Área</th><th v-if="isStudentList">Docente / referente</th><th>Estado</th><th>Seguimiento</th></tr></thead>
              <tbody>
                <tr v-for="record in filteredRecords" :key="record.id">
                  <td class="person-cell"><span class="avatar">{{ initials(record.name) }}</span><strong>{{ record.name }}</strong></td>
                  <td>{{ isStudentList ? `${record.year}° ${record.group}` : record.area }}</td>
                  <td v-if="isStudentList">{{ record.teacher }}</td>
                  <td><span class="status-badge" :class="record.statusClass">{{ record.status }}</span></td>
                  <td>{{ record.followUp }}</td>
                </tr>
                <tr v-if="filteredRecords.length === 0"><td class="empty-cell" :colspan="isStudentList ? 5 : 4">No hay registros que coincidan con la búsqueda.</td></tr>
              </tbody>
            </table>
          </div>
          <p class="sample-note">Listado de ejemplo. Las modificaciones locales se mantienen en esta sesión de prueba.</p>
        </section>
      </template>

      <template v-else-if="page.startsWith('tutorias-')">
        <section class="portal-card">
          <div class="card-heading">
            <div><span class="section-label">Agenda de acompañamiento</span><h2>Tutorías programadas</h2></div>
            <label class="filter-field"><span>Estado</span><select v-model="statusFilter"><option value="Todos">Todos</option><option>Programada</option><option>Realizada</option><option>Pendiente</option></select></label>
          </div>
          <div class="table-scroll">
            <table>
              <thead><tr><th>Fecha y hora</th><th>Estudiante / curso</th><th>Materia</th><th>Docente</th><th>Estado</th><th>Acción</th></tr></thead>
              <tbody>
                <tr v-for="item in filteredTutoring" :key="item.id">
                  <td>{{ item.date }}<small class="table-secondary">{{ item.time }}</small></td>
                  <td>{{ item.student }}<small class="table-secondary">{{ item.course }}</small></td>
                  <td>{{ item.subject }}</td><td>{{ item.teacher }}</td>
                  <td><span class="status-badge" :class="statusClass(item.status)">{{ item.status }}</span></td>
                  <td><button class="action-link" type="button" @click="toggleTutoring(item)">{{ item.status === 'Realizada' ? 'Marcar pendiente' : 'Marcar realizada' }}</button></td>
                </tr>
                <tr v-if="filteredTutoring.length === 0"><td class="empty-cell" colspan="6">No hay tutorías para este filtro.</td></tr>
              </tbody>
            </table>
          </div>
          <p v-if="actionMessage" class="feedback-message" role="status">{{ actionMessage }}</p>
          <p class="sample-note">Las acciones actualizan el estado en el navegador. La agenda mostrada es de ejemplo.</p>
        </section>
      </template>

      <template v-else-if="page === 'observaciones-coordinacion'">
        <section class="portal-card">
          <div class="card-heading"><div><span class="section-label">Acompañamiento escolar</span><h2>Observaciones registradas</h2></div><span class="count-badge">{{ observations.length }} observaciones</span></div>
          <form class="observation-form" @submit.prevent="addObservation">
            <label class="field"><span>Estudiante</span><select v-model="newObservation.student"><option v-for="student in studentNames" :key="student">{{ student }}</option></select></label>
            <label class="field"><span>Tipo</span><select v-model="newObservation.type"><option>Académica</option><option>Asistencia</option><option>Convivencia</option><option>Reconocimiento</option></select></label>
            <label class="field observation-description"><span>Detalle de seguimiento</span><input v-model.trim="newObservation.detail" type="text" placeholder="Escribí una observación" required maxlength="180" /></label>
            <button class="primary-button" type="submit">Agregar observación <span>＋</span></button>
          </form>
          <div class="observation-list">
            <article v-for="item in observations" :key="item.id" class="observation-row">
              <span class="row-icon purple">{{ item.type === 'Asistencia' ? '◷' : item.type === 'Reconocimiento' ? '☆' : '▤' }}</span>
              <div class="row-copy"><strong>{{ item.student }} <span class="type-tag">{{ item.type }}</span></strong><small>{{ item.detail }}</small><small>{{ item.date }} · {{ item.author }}</small></div>
              <button class="remove-button" type="button" :aria-label="`Eliminar observación de ${item.student}`" @click="removeObservation(item.id)">×</button>
            </article>
          </div>
          <p v-if="actionMessage" class="feedback-message" role="status">{{ actionMessage }}</p>
          <p class="sample-note">Las nuevas observaciones se guardan solo en este navegador para la demostración.</p>
        </section>
      </template>

      <section v-else class="portal-card">
        <span class="section-label">Vista institucional</span>
        <h2>{{ pageTitle }}</h2>
        <p class="subtitle">Esta sección todavía no tiene una pantalla disponible para este perfil.</p>
      </section>
    </main>
  </section>
</template>

<script setup>
import { computed, reactive, ref } from 'vue'

const props = defineProps({
  page: { type: String, default: 'inicio' },
  session: { type: Object, default: () => ({}) }
})

const emit = defineEmits(['navigate'])

const pageInfo = {
  inicio: { title: 'Panel principal', description: 'Un resumen para acompañar el día a día.' },
  'estudiantes-docente': { title: 'Mis estudiantes', description: 'Consultá los cursos y el seguimiento de tus estudiantes.' },
  'estudiantes-preceptoria': { title: 'Estudiantes', description: 'Consultá estudiantes, cursos y referencias de acompañamiento.' },
  'estudiantes-direccion': { title: 'Estudiantes', description: 'Vista general de la comunidad estudiantil.' },
  'profesores-direccion': { title: 'Profesores', description: 'Consultá el equipo docente de la institución.' },
  'profesores-coordinacion': { title: 'Profesores', description: 'Consultá docentes y sus áreas de acompañamiento.' },
  'tutorias-preceptoria': { title: 'Tutorías', description: 'Revisá las tutorías programadas y su estado.' },
  'tutorias-coordinacion': { title: 'Tutorías', description: 'Supervisá las tutorías programadas en la institución.' },
  'observaciones-coordinacion': { title: 'Observaciones', description: 'Registrá y consultá notas de seguimiento escolar.' }
}

const dashboards = {
  estudiante: {
    title: 'Mi espacio de estudiante',
    stats: [],
    links: []
  },
  docente: {
    title: 'Panel principal del docente',
    stats: [
      { label: 'Mis estudiantes', value: '28', note: 'en tus cursos', icon: '👥', color: 'green' },
      { label: 'Tutorías próximas', value: '4', note: 'esta semana', icon: '▣', color: 'purple' },
      { label: 'Citaciones pendientes', value: '3', note: 'para seguimiento', icon: '◷', color: 'yellow' },
      { label: 'Materias a cargo', value: '2', note: 'este ciclo lectivo', icon: '▤', color: 'blue' }
    ],
    links: [
      { id: 'nueva-citacion', label: 'Nueva citación', description: 'Programá un encuentro', icon: '＋', color: 'yellow' },
      { id: 'estudiantes-docente', label: 'Mis estudiantes', description: 'Ver cursos y seguimiento', icon: '👥', color: 'green' },
      { id: 'horarios', label: 'Mis horarios', description: 'Consultar tutorías', icon: '◷', color: 'blue' },
      { id: 'reporte-docente', label: 'Reportes', description: 'Revisar actividad', icon: '▤', color: 'purple' }
    ]
  },
  preceptor: {
    title: 'Panel principal de preceptoría',
    stats: [
      { label: 'Estudiantes', value: '124', note: 'a cargo', icon: '👥', color: 'green' },
      { label: 'Asistencia de hoy', value: '92%', note: 'registro de muestra', icon: '✓', color: 'blue' },
      { label: 'Tutorías activas', value: '8', note: 'esta semana', icon: '▣', color: 'purple' },
      { label: 'Alertas de seguimiento', value: '5', note: 'para revisar', icon: '△', color: 'yellow' }
    ],
    links: [
      { id: 'asistencia', label: 'Asistencia', description: 'Registrar presentes y ausentes', icon: '✓', color: 'green' },
      { id: 'estudiantes-preceptoria', label: 'Estudiantes', description: 'Consultar cursos', icon: '👥', color: 'blue' },
      { id: 'tutorias-preceptoria', label: 'Tutorías', description: 'Revisar agenda', icon: '▣', color: 'yellow' }
    ]
  },
  director: {
    title: 'Panel principal de dirección',
    stats: [
      { label: 'Estudiantes', value: '530', note: 'matrícula de muestra', icon: '👥', color: 'green' },
      { label: 'Profesores', value: '58', note: 'equipo docente', icon: '♙', color: 'purple' },
      { label: 'Asistencia promedio', value: '94%', note: 'este mes', icon: '✓', color: 'blue' },
      { label: 'Reportes disponibles', value: '6', note: 'para consultar', icon: '▤', color: 'yellow' }
    ],
    links: [
      { id: 'estudiantes-direccion', label: 'Estudiantes', description: 'Vista general', icon: '👥', color: 'green' },
      { id: 'profesores-direccion', label: 'Profesores', description: 'Equipo institucional', icon: '♙', color: 'purple' },
      { id: 'reportes', label: 'Reportes', description: 'Indicadores institucionales', icon: '▤', color: 'blue' }
    ]
  },
  coordinador: {
    title: 'Panel principal de coordinación',
    stats: [
      { label: 'Estudiantes', value: '530', note: 'en seguimiento', icon: '👥', color: 'green' },
      { label: 'Profesores', value: '58', note: 'activos', icon: '♙', color: 'purple' },
      { label: 'Tutorías', value: '45', note: 'este mes', icon: '▣', color: 'yellow' },
      { label: 'Observaciones', value: '12', note: 'por revisar', icon: '▤', color: 'blue' }
    ],
    links: [
      { id: 'estudiantes-coordinacion', label: 'Estudiantes', description: 'Ver y gestionar', icon: '👥', color: 'green' },
      { id: 'profesores-coordinacion', label: 'Profesores', description: 'Ver equipo docente', icon: '♙', color: 'purple' },
      { id: 'observaciones-coordinacion', label: 'Observaciones', description: 'Registrar seguimientos', icon: '▤', color: 'blue' },
      { id: 'tutorias-coordinacion', label: 'Tutorías', description: 'Gestionar agenda', icon: '▣', color: 'yellow' }
    ]
  }
}

const role = computed(() => props.session.role || 'docente')
const roleNames = { estudiante: 'Estudiante', docente: 'Docente', preceptor: 'Preceptoría', director: 'Dirección', coordinador: 'Coordinación' }
const roleName = computed(() => roleNames[role.value] || 'Institución')
const dashboard = computed(() => dashboards[role.value] || dashboards.docente)
const pageTitle = computed(() => props.page === 'inicio' ? dashboard.value.title : (pageInfo[props.page]?.title || 'Gestión institucional'))
const pageDescription = computed(() => props.page === 'inicio' ? `Hola, ${props.session.name || roleName.value}. ${pageInfo.inicio.description}` : (pageInfo[props.page]?.description || ''))
const isStudentList = computed(() => props.page.startsWith('estudiantes-'))
const search = ref('')
const yearFilter = ref('Todos')
const statusFilter = ref('Todos')
const actionMessage = ref('')
const years = [1, 2, 3, 4, 5, 6]

const seedStudents = [
  { id: 1, name: 'María Gómez', year: 4, group: 'A', teacher: 'Juan Pérez', area: '4° A', status: 'En seguimiento', statusClass: 'status-follow', followUp: 'Tutoría de Matemática' },
  { id: 2, name: 'Lucas Díaz', year: 4, group: 'A', teacher: 'Juan Pérez', area: '4° A', status: 'Activo', statusClass: 'status-active', followUp: 'Citación pendiente' },
  { id: 3, name: 'Sofía López', year: 3, group: 'B', teacher: 'Ana Torres', area: '3° B', status: 'Activo', statusClass: 'status-active', followUp: 'Seguimiento de trabajos' },
  { id: 4, name: 'Bruno Acosta', year: 2, group: 'A', teacher: 'Carla Méndez', area: '2° A', status: 'En seguimiento', statusClass: 'status-follow', followUp: 'Asistencia' },
  { id: 5, name: 'Valentina Cruz', year: 5, group: 'A', teacher: 'Diego Romero', area: '5° A', status: 'Activo', statusClass: 'status-active', followUp: 'Sin alertas' },
  { id: 6, name: 'Emma Gil', year: 6, group: 'B', teacher: 'Ana Torres', area: '6° B', status: 'Activo', statusClass: 'status-active', followUp: 'Tutoría de Historia' }
]
const seedTeachers = [
  { id: 1, name: 'Juan Pérez', area: 'Programación', status: 'Activo', statusClass: 'status-active', followUp: '4° A · 5° B' },
  { id: 2, name: 'Ana Torres', area: 'Matemática e Historia', status: 'Activo', statusClass: 'status-active', followUp: '3° B · 6° B' },
  { id: 3, name: 'Carla Méndez', area: 'Lengua y Literatura', status: 'Activo', statusClass: 'status-active', followUp: '2° A · 3° A' },
  { id: 4, name: 'Diego Romero', area: 'Física', status: 'A revisar', statusClass: 'status-follow', followUp: 'Falta actualizar horario' }
]

const records = computed(() => isStudentList.value ? seedStudents : seedTeachers)
const filteredRecords = computed(() => records.value.filter((record) => {
  const matchesSearch = `${record.name} ${record.area} ${record.teacher || ''} ${record.followUp}`.toLocaleLowerCase('es').includes(search.value.trim().toLocaleLowerCase('es'))
  const matchesYear = yearFilter.value === 'Todos' || String(record.year) === String(yearFilter.value)
  return matchesSearch && (!isStudentList.value || matchesYear)
}))

const studentAgenda = [
  { id: 1, title: 'Tutoría de Matemática', detail: 'Ana Torres · Aula 2', date: 'Martes · 10:30', icon: '∑', color: 'green' },
  { id: 2, title: 'Apoyo en Historia', detail: 'Ana Torres · Biblioteca', date: 'Jueves · 12:00', icon: '▤', color: 'purple' }
]

const seedTutorings = [
  { id: 1, date: '22/10/2026', time: '10:30', student: 'María Gómez', course: '4° A', subject: 'Matemática', teacher: 'Ana Torres', status: 'Programada' },
  { id: 2, date: '22/10/2026', time: '12:00', student: 'Lucas Díaz', course: '4° A', subject: 'Programación', teacher: 'Juan Pérez', status: 'Pendiente' },
  { id: 3, date: '23/10/2026', time: '09:00', student: 'Emma Gil', course: '6° B', subject: 'Historia', teacher: 'Ana Torres', status: 'Realizada' }
]
const tutorings = ref(loadCollection('tutoScript.tutorings', seedTutorings))
const filteredTutoring = computed(() => tutorings.value.filter((item) => statusFilter.value === 'Todos' || item.status === statusFilter.value))
const seedObservations = [
  { id: 1, student: 'María Gómez', type: 'Académica', detail: 'Reforzar contenidos de fracciones y resolución de problemas.', date: '20/10/2026', author: 'Ana Torres' },
  { id: 2, student: 'Bruno Acosta', type: 'Asistencia', detail: 'Se acordó seguimiento semanal de asistencia.', date: '19/10/2026', author: 'Equipo de preceptoría' }
]
const observations = ref(loadCollection('tutoScript.observations', seedObservations))
const studentNames = seedStudents.map((student) => student.name)
const newObservation = reactive({ student: studentNames[0], type: 'Académica', detail: '' })

function loadCollection(key, fallback) {
  try {
    const stored = localStorage.getItem(key)
    return stored ? JSON.parse(stored) : fallback
  } catch (error) {
    console.error(`No se pudieron cargar los datos de muestra (${key}).`, error)
    return fallback
  }
}

function initials(name) {
  return name.split(' ').map((part) => part[0]).slice(0, 2).join('').toLocaleUpperCase('es')
}

function statusClass(status) {
  return status === 'Realizada' ? 'status-active' : status === 'Programada' ? 'status-follow' : 'status-pending'
}

function persistCollection(key, value) {
  try {
    localStorage.setItem(key, JSON.stringify(value))
    return true
  } catch (error) {
    console.error(`No se pudieron guardar los datos de muestra (${key}).`, error)
    return false
  }
}

function toggleTutoring(item) {
  const previousStatus = item.status
  item.status = item.status === 'Realizada' ? 'Pendiente' : 'Realizada'
  if (persistCollection('tutoScript.tutorings', tutorings.value)) {
    actionMessage.value = 'Estado de tutoría actualizado en este navegador.'
    return
  }
  item.status = previousStatus
  actionMessage.value = 'No se pudo guardar el cambio. Revisá el almacenamiento del navegador.'
}

function addObservation() {
  const observation = {
    id: Date.now(),
    student: newObservation.student,
    type: newObservation.type,
    detail: newObservation.detail,
    date: new Intl.DateTimeFormat('es-AR').format(new Date()),
    author: props.session.name || roleName.value
  }
  observations.value.unshift(observation)
  if (!persistCollection('tutoScript.observations', observations.value)) {
    observations.value.shift()
    actionMessage.value = 'No se pudo guardar la observación. Revisá el almacenamiento del navegador.'
    return
  }
  actionMessage.value = 'Observación agregada en este navegador.'
  newObservation.detail = ''
}

function removeObservation(id) {
  const index = observations.value.findIndex((item) => item.id === id)
  if (index < 0) return
  const [removed] = observations.value.splice(index, 1)
  if (persistCollection('tutoScript.observations', observations.value)) {
    actionMessage.value = 'Observación eliminada.'
    return
  }
  if (removed) observations.value.splice(index, 0, removed)
  actionMessage.value = 'No se pudo eliminar la observación. Revisá el almacenamiento del navegador.'
}
</script>

<style scoped>
.portal-page {
  min-height: 100vh;
  margin: calc(-1 * clamp(1rem, 3vw, 2rem));
  padding: 38px clamp(20px, 5vw, 64px);
  background: #faf5ec;
  color: #231f2c;
  font-family: Arial, Helvetica, sans-serif;
}

.portal-content {
  width: min(100%, 1160px);
  margin: 0 auto;
}

.page-heading,
.card-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 18px;
}

.page-heading {
  padding-bottom: 24px;
  border-bottom: 1px solid rgb(35 31 44 / 14%);
}

.eyebrow,
.section-label {
  color: #6b9a55;
  font-size: 12px;
  font-weight: 700;
}

.page-heading h1 {
  margin: 9px 0 5px;
  font-size: 32px;
  font-style: italic;
}

.subtitle {
  margin: 0;
  color: #6b6577;
  font-size: 14px;
}

.heading-mark {
  display: grid;
  width: 42px;
  height: 42px;
  flex: 0 0 42px;
  place-items: center;
  border: 1.5px solid #231f2c;
  border-radius: 50%;
  background: #faebc7;
  font-size: 22px;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 14px;
  margin-top: 24px;
}

.stat-card {
  display: flex;
  min-height: 150px;
  flex-direction: column;
  align-items: flex-start;
  padding: 17px;
  border: 1.5px solid #231f2c;
  border-radius: 15px;
}

.stat-card.green,
.quick-card.green {
  background: #e2eeda;
}

.stat-card.purple,
.quick-card.purple {
  background: #f4eefb;
}

.stat-card.yellow,
.quick-card.yellow {
  background: #faebc7;
}

.stat-card.blue,
.quick-card.blue {
  background: #dceef9;
}

.stat-icon {
  margin-bottom: 8px;
  font-size: 19px;
}

.stat-card small {
  font-size: 11px;
  font-weight: 700;
}

.stat-card strong {
  margin-top: 3px;
  font-size: 28px;
}

.stat-card > span:last-child {
  margin-top: 3px;
  color: #6b6577;
  font-size: 11px;
}

.portal-card {
  margin-top: 23px;
  padding: 23px;
  border: 1.5px solid #231f2c;
  border-radius: 16px;
  background: #fffdf9;
}

.card-heading h2,
.portal-card > h2 {
  margin: 6px 0 0;
  font-size: 20px;
  font-style: italic;
}

.quick-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 12px;
  margin-top: 18px;
}

.quick-card {
  display: flex;
  min-height: 130px;
  flex-direction: column;
  align-items: flex-start;
  gap: 7px;
  padding: 15px;
  border: 1.5px solid #231f2c;
  border-radius: 12px;
  color: inherit;
  text-align: left;
  cursor: pointer;
}

.quick-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgb(35 31 44 / 10%);
}

.quick-card > span {
  font-size: 21px;
}

.quick-card strong {
  font-size: 13px;
}

.quick-card small {
  color: #6b6577;
  font-size: 11px;
}

.sample-note {
  margin: 17px 0 0;
  color: #827b86;
  font-size: 11px;
}

.feedback-message {
  margin: 14px 0 0;
  color: #46733c;
  font-size: 12px;
  font-weight: 700;
}

.student-stats {
  grid-template-columns: repeat(3, minmax(0, 1fr));
}

.list-row,
.observation-row {
  display: flex;
  align-items: center;
  gap: 13px;
  padding: 15px 0;
  border-bottom: 1px solid rgb(35 31 44 / 10%);
}

.list-row:last-of-type {
  border-bottom: 0;
}

.row-icon {
  display: grid;
  width: 38px;
  height: 38px;
  flex: 0 0 38px;
  place-items: center;
  border: 1px solid rgb(35 31 44 / 15%);
  border-radius: 10px;
}

.row-icon.green {
  background: #e2eeda;
}

.row-icon.purple {
  background: #f4eefb;
}

.row-copy {
  display: grid;
  flex: 1;
  gap: 5px;
}

.row-copy strong {
  font-size: 13px;
}

.row-copy small,
.row-date {
  color: #6b6577;
  font-size: 11px;
}

.row-date {
  text-align: right;
}

.count-badge {
  padding: 6px 10px;
  border-radius: 99px;
  background: #e2eeda;
  color: #46733c;
  font-size: 11px;
  font-weight: 700;
  white-space: nowrap;
}

.toolbar {
  display: flex;
  align-items: flex-end;
  gap: 12px;
  margin: 19px 0 14px;
}

.search-box,
.filter-field,
.field {
  display: flex;
  flex-direction: column;
  gap: 7px;
}

.search-box {
  position: relative;
  flex: 1;
}

.search-box > span {
  position: absolute;
  top: 8px;
  left: 11px;
  color: #6b6577;
  font-size: 20px;
}

.search-box input,
.filter-field select,
.field input,
.field select {
  min-height: 40px;
  padding: 0 11px;
  border: 1px solid #c9c2b7;
  border-radius: 8px;
  background: #fffdf9;
  color: #231f2c;
  font: inherit;
  font-size: 12px;
}

.search-box input {
  width: 100%;
  padding-left: 38px;
}

.filter-field {
  min-width: 130px;
}

.filter-field > span,
.field > span {
  color: #6b6577;
  font-size: 11px;
  font-weight: 700;
}

.table-scroll {
  overflow-x: auto;
  border: 1px solid rgb(35 31 44 / 14%);
  border-radius: 10px;
}

table {
  width: 100%;
  min-width: 660px;
  border-collapse: collapse;
  text-align: left;
}

th,
td {
  padding: 12px;
  border-bottom: 1px solid rgb(35 31 44 / 9%);
  font-size: 12px;
}

th {
  background: #f5efe4;
  color: #5f5965;
  font-size: 11px;
}

tbody tr:last-child td {
  border-bottom: 0;
}

.person-cell {
  display: flex;
  align-items: center;
  gap: 9px;
}

.avatar {
  display: grid;
  width: 29px;
  height: 29px;
  flex: 0 0 29px;
  place-items: center;
  border: 1px solid #231f2c;
  border-radius: 50%;
  background: #dceef9;
  font-size: 9px;
  font-weight: 700;
}

.status-badge {
  display: inline-block;
  padding: 5px 8px;
  border-radius: 99px;
  font-size: 10px;
  font-weight: 700;
  white-space: nowrap;
}

.status-active {
  background: #e2eeda;
  color: #46733c;
}

.status-follow,
.status-pending {
  background: #faebc7;
  color: #765915;
}

.table-secondary {
  display: block;
  margin-top: 4px;
  color: #6b6577;
}

.empty-cell {
  padding: 25px;
  color: #6b6577;
  text-align: center;
}

.action-link {
  padding: 6px 8px;
  border: 1px solid #c9c2b7;
  border-radius: 7px;
  background: #fffdf9;
  color: #46733c;
  font: inherit;
  font-size: 10px;
  cursor: pointer;
}

.observation-form {
  display: grid;
  grid-template-columns: 1fr 1fr auto;
  gap: 13px;
  margin: 20px 0;
  padding: 16px;
  border-radius: 11px;
  background: #faf5ec;
}

.observation-description {
  grid-column: 1 / 3;
}

.primary-button {
  align-self: end;
  min-height: 40px;
  padding: 0 13px;
  border: 1.5px solid #231f2c;
  border-radius: 8px;
  background: #e2eeda;
  color: #231f2c;
  font: inherit;
  font-size: 11px;
  font-weight: 700;
  cursor: pointer;
}

.type-tag {
  margin-left: 5px;
  padding: 3px 6px;
  border-radius: 99px;
  background: #f4eefb;
  color: #6c4b9c;
  font-size: 9px;
  font-style: normal;
}

.remove-button {
  width: 29px;
  height: 29px;
  border: 1px solid #e5c9c5;
  border-radius: 50%;
  background: #fffdf9;
  color: #914d48;
  font-size: 18px;
  cursor: pointer;
}

@media (max-width: 820px) {
  .stats-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .quick-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 560px) {
  .portal-page {
    padding: 26px 16px;
  }

  .page-heading h1 {
    font-size: 27px;
  }

  .stats-grid,
  .student-stats {
    grid-template-columns: 1fr 1fr;
  }

  .portal-card {
    padding: 17px;
  }

  .toolbar {
    align-items: stretch;
    flex-direction: column;
  }

  .observation-form {
    grid-template-columns: 1fr;
  }

  .observation-description {
    grid-column: auto;
  }
}
</style>
