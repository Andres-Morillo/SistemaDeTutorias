<script setup>
import { computed, onMounted, onUnmounted, ref } from 'vue'
import Login from './components/login.vue'
import Sidebar from './components/sidebar.vue'
import NuevaCitacion from './components/nuevacitacion.vue'
import MisCitaciones from './components/miscitaciones.vue'
import Materias from './components/Materias.vue'
import SistemaPreceptoria from './components/SistemaPreceptoria.vue'
import EstudiantesCoordinacion from './components/EstudiantesCoordinacion.vue'
import ReportesDireccion from './components/ReportesDireccion.vue'
import ReporteDocentes from './components/ReporteDocentes.vue'
import MiPerfil from './components/MiPerfil.vue'
import HorarioTutorias from './components/HorarioTutorias.vue'
import Configuracion from './components/AjustesConfiguracion.vue'
import PaginasInstitucionales from './components/PaginasInstitucionales.vue'
import { supabase } from './lib/supabase'

const citationsStorageKey = 'tutoScript.citations'
const profileStorageKey = 'tutoScript.profile'
const roleSections = {
  estudiante: ['inicio'],
  docente: ['inicio', 'perfil-docente', 'nueva-citacion', 'mis-citaciones', 'materias', 'estudiantes-docente', 'horarios', 'reporte-docente', 'configuracion'],
  director: ['inicio', 'estudiantes-direccion', 'profesores-direccion', 'reportes', 'configuracion'],
  coordinador: ['inicio', 'estudiantes-coordinacion', 'profesores-coordinacion', 'observaciones-coordinacion', 'tutorias-coordinacion', 'configuracion'],
  preceptor: ['inicio', 'asistencia', 'estudiantes-preceptoria', 'tutorias-preceptoria', 'configuracion']
}
const session = ref(null)
const savedCitations = ref(loadSavedCitations())
const currentSection = ref('inicio')
const isWorkspaceVisible = ref(false)
const sectionViews = {
  inicio: PaginasInstitucionales,
  'nueva-citacion': NuevaCitacion,
  'mis-citaciones': MisCitaciones,
  materias: Materias,
  asistencia: SistemaPreceptoria,
  'perfil-docente': MiPerfil,
  'reporte-docente': ReporteDocentes,
  'estudiantes-coordinacion': EstudiantesCoordinacion,
  reportes: ReportesDireccion,
  horarios: HorarioTutorias,
  configuracion: Configuracion
}
const normalizedRole = computed(() => normalizeRole(session.value?.role))
const activeSectionView = computed(() => sectionViews[currentSection.value] ?? PaginasInstitucionales)

let authSubscription

onMounted(async () => {
  if (!supabase) return

  const { data } = supabase.auth.onAuthStateChange((event, authSession) => {
    if (event === 'PASSWORD_RECOVERY') {
      session.value = null
      isWorkspaceVisible.value = false
      return
    }

    if (authSession) {
      startSession(authSession.user)
      return
    }

    session.value = null
    isWorkspaceVisible.value = false
    currentSection.value = 'inicio'
  })
  authSubscription = data.subscription

  const { data: sessionData, error } = await supabase.auth.getSession()
  if (error) {
    console.error('No se pudo recuperar la sesión de Supabase.', error)
    return
  }
  if (sessionData.session) startSession(sessionData.session.user)
})

onUnmounted(() => authSubscription?.unsubscribe())

function loadSavedCitations() {
  try {
    const storedCitations = localStorage.getItem(citationsStorageKey)
    return storedCitations ? JSON.parse(storedCitations) : []
  } catch (error) {
    console.error('No se pudieron cargar las citaciones guardadas.', error)
    return []
  }
}

function normalizeRole(role) {
  const normalized = String(role ?? 'docente').toLocaleLowerCase('es')
  const aliases = {
    alumno: 'estudiante',
    directivo: 'director',
    coordinacion: 'coordinador',
    'coordinación': 'coordinador'
  }
  const canonical = aliases[normalized] || normalized
  return roleSections[canonical] ? canonical : 'docente'
}

function navigateTo(section) {
  const allowedSections = roleSections[normalizedRole.value] ?? roleSections.docente
  if (!allowedSections.includes(section)) {
    currentSection.value = 'inicio'
    return
  }
  currentSection.value = section
  isWorkspaceVisible.value = true
  window.scrollTo(0, 0)
}

function startSession(user) {
  const email = user.email || ''
  const metadata = user.user_metadata || {}
  const savedProfile = loadSavedProfile(email)
  session.value = {
    name: savedProfile?.name || metadata.full_name || metadata.name || email.split('@')[0] || 'Usuario',
    email,
    role: normalizeRole(metadata.role)
  }
  currentSection.value = 'inicio'
  isWorkspaceVisible.value = true
  window.scrollTo(0, 0)
}

function updateProfile(profile) {
  session.value = { ...session.value, ...profile }
  localStorage.setItem(profileStorageKey, JSON.stringify(session.value))
}

function loadSavedProfile(email) {
  try {
    const profile = JSON.parse(localStorage.getItem(profileStorageKey) || 'null')
    return profile?.email === email ? profile : null
  } catch (error) {
    console.error('No se pudo cargar el perfil guardado.', error)
    return null
  }
}

function handleCitation(citation, status = 'Pendiente') {
  const entry = {
    id: Date.now(),
    date: citation.date || new Date().toISOString().slice(0, 10),
    time: citation.time || '09:00',
    course: '4°',
    subject: 'Programación',
    teacher: session.value?.name || 'Docente',
    students: ['María Gómez', 'Lucas Díaz'],
    reason: citation.reason || citation.content || 'Citación de tutoría',
    status,
    notification: citation.notification || 'Email'
  }
  const nextCitations = [entry, ...savedCitations.value]
  try {
    localStorage.setItem(citationsStorageKey, JSON.stringify(nextCitations))
  } catch (error) {
    console.error('No se pudo guardar la citación en este navegador.', error)
    window.alert('No se pudo guardar la citación. Revisá el almacenamiento del navegador e intentá de nuevo.')
    return
  }
  savedCitations.value = nextCitations
  navigateTo('mis-citaciones')
}

async function logout() {
  if (!supabase) return

  try {
    const { error } = await supabase.auth.signOut()
    if (error) {
      console.error('No se pudo cerrar la sesión de Supabase.', error)
      window.alert(`No se pudo cerrar la sesión: ${error.message}`)
      return
    }
    window.scrollTo(0, 0)
  } catch (error) {
    console.error('No se pudo conectar con Supabase para cerrar la sesión.', error)
    window.alert('No se pudo conectar con Supabase para cerrar la sesión. Intentá de nuevo.')
  }
}
</script>

<template>
  <div class="app">

    <div v-show="!isWorkspaceVisible" class="auth-view">
      <Login @iniciar-sesion="startSession" />
    </div>
    <div v-if="isWorkspaceVisible" class="layout">
      <Sidebar
        :active-view="currentSection"
        :role="normalizedRole"
        @navigate="navigateTo"
        @logout="logout"
      />

      <main class="contenido">
        <component
          :is="activeSectionView"
          v-if="activeSectionView"
          :page="currentSection"
          :session="session"
          :citations="savedCitations"
          @save-profile="updateProfile"
          @guardar-borrador="handleCitation($event, 'Borrador')"
          @guardar-citacion="handleCitation"
          @anular-citacion="handleCitation($event, 'Anulada')"
          @cancelar="navigateTo('inicio')"
          @logout="logout"
        />

      </main>
    </div>

  </div>
</template>

<style>
*,
*::before,
*::after {
  box-sizing: border-box;
}

html,
body,
#app {
  margin: 0;
  padding: 0;
  width: 100%;
  min-width: 0;
  min-height: 100vh;
}

body {
  overflow-x: hidden;
}

.app {
  width: 100%;
  min-width: 0;
}

/* Layout general */
.layout {
  width: 100%;
  min-width: 0;
  min-height: 100vh;
  padding-left: 250px;

  display: flex;
  align-items: stretch;
}

/* Contenido situado al lado de la sidebar */
.contenido {
  flex: 1 1 auto;
  min-width: 0;
  width: auto;

  padding: clamp(1rem, 3vw, 2rem);
}

.section-placeholder {
  padding: 2rem;
  color: #2c3e50;
}

.section-placeholder h1 {
  margin: 0 0 0.75rem;
}

.section-placeholder p {
  color: #64748b;
}

/* Evita que el contenido rompa el ancho */
.contenido > * {
  max-width: 100%;
}

.contenido > .attendance-page,
.contenido > .students-page,
.contenido > .reports-page,
.contenido > .profile-page {
  max-width: none;
}

/* Pantallas estrechas */
@media (max-width: 700px) {
  .layout {
    flex-direction: column;
    padding-left: 0;
  }

  .contenido {
    width: 100%;
  }
}
</style>
