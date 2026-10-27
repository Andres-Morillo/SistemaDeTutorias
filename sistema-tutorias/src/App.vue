<script setup>
import { computed, ref, shallowRef } from 'vue'
import Login from './components/login.vue'
import Register from './components/register.vue'
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

const sessionStorageKey = 'tutoScript.session'
const accountsStorageKey = 'tutoScript.accounts'
const citationsStorageKey = 'tutoScript.citations'
const recoveryRequested = new URLSearchParams(window.location.search).get('flujo') === 'recuperar'
const roleSections = {
  estudiante: ['inicio'],
  docente: ['inicio', 'perfil-docente', 'nueva-citacion', 'mis-citaciones', 'materias', 'estudiantes-docente', 'horarios', 'reporte-docente', 'configuracion'],
  director: ['inicio', 'estudiantes-direccion', 'profesores-direccion', 'reportes', 'configuracion'],
  coordinador: ['inicio', 'estudiantes-coordinacion', 'profesores-coordinacion', 'observaciones-coordinacion', 'tutorias-coordinacion', 'configuracion'],
  preceptor: ['inicio', 'asistencia', 'estudiantes-preceptoria', 'tutorias-preceptoria', 'configuracion']
}
const session = ref(loadSession())
const savedCitations = ref(loadSavedCitations())
const activeAuthComponent = shallowRef(Login)
const currentSection = ref('inicio')
const isWorkspaceVisible = ref(Boolean(session.value) && !recoveryRequested)
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

function loadSession() {
  try {
    const storedSession = localStorage.getItem(sessionStorageKey)
    if (!storedSession) return null
    const loadedSession = JSON.parse(storedSession)
    const savedAccount = loadAccounts().find((account) => (
      account.email?.toLocaleLowerCase('es') === loadedSession.email?.toLocaleLowerCase('es')
    ))
    return { ...loadedSession, role: normalizeRole(savedAccount?.role || loadedSession.role) }
  } catch {
    localStorage.removeItem(sessionStorageKey)
    return null
  }
}

function loadSavedCitations() {
  try {
    const storedCitations = localStorage.getItem(citationsStorageKey)
    return storedCitations ? JSON.parse(storedCitations) : []
  } catch (error) {
    console.error('No se pudieron cargar las citaciones guardadas.', error)
    return []
  }
}

function loadAccounts() {
  try {
    const accounts = JSON.parse(localStorage.getItem(accountsStorageKey) || '[]')
    return Array.isArray(accounts) ? accounts : []
  } catch (error) {
    console.error('No se pudieron cargar las cuentas guardadas.', error)
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

function showRegister() {
  activeAuthComponent.value = Register
}

function showLogin() {
  activeAuthComponent.value = Login
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

function startSession(account) {
  const loginIdentifier = account.email?.trim() ?? ''
  const savedAccount = loadAccounts().find((saved) => (
    saved.email?.toLocaleLowerCase('es') === loginIdentifier.toLocaleLowerCase('es')
    || saved.username?.toLocaleLowerCase('es') === loginIdentifier.toLocaleLowerCase('es')
  ))
  const email = savedAccount?.email || loginIdentifier
  const name = savedAccount?.name || account.name?.trim() || account.username?.trim() || email.split('@')[0] || 'Usuario'
  session.value = {
    name,
    email,
    role: normalizeRole(savedAccount?.role || account.role)
  }
  localStorage.setItem(sessionStorageKey, JSON.stringify({ name, email }))
  currentSection.value = 'inicio'
  isWorkspaceVisible.value = true
  window.scrollTo(0, 0)
}

function registerAccount(account) {
  const accounts = loadAccounts()
  const normalizedEmail = account.email.trim().toLocaleLowerCase('es')
  const index = accounts.findIndex((saved) => saved.email?.toLocaleLowerCase('es') === normalizedEmail)
  const savedAccount = {
    username: account.username?.trim() || account.name?.trim() || '',
    name: account.name?.trim() || account.username?.trim() || '',
    email: account.email.trim(),
    role: normalizeRole(account.role)
  }
  if (index === -1) accounts.push(savedAccount)
  else accounts[index] = savedAccount
  try {
    localStorage.setItem(accountsStorageKey, JSON.stringify(accounts))
    startSession(savedAccount)
  } catch (error) {
    console.error('No se pudo guardar la cuenta en este navegador.', error)
    window.alert('No se pudo guardar la cuenta. Revisá el almacenamiento del navegador e intentá de nuevo.')
  }
}

function updateProfile(profile) {
  session.value = { ...session.value, ...profile }
  localStorage.setItem(sessionStorageKey, JSON.stringify(session.value))
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

function logout() {
  localStorage.removeItem(sessionStorageKey)
  document.cookie = `${sessionStorageKey}=; Max-Age=0; path=/; SameSite=Lax`
  session.value = null
  isWorkspaceVisible.value = false
  currentSection.value = 'inicio'
  showLogin()
  window.scrollTo(0, 0)
}
</script>

<template>
  <div class="app">

    <div v-show="!isWorkspaceVisible" class="auth-view">
      <KeepAlive>
        <component
          :is="activeAuthComponent"
          @cambiar-a-registro="showRegister"
          @cambiar-a-login="showLogin"
          @iniciar-sesion="startSession"
          @cuenta-creada="registerAccount"
        />
      </KeepAlive>
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
