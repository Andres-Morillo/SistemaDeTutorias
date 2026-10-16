<script setup>
import { computed, ref, shallowRef } from 'vue'
import Login from './components/login.vue'
import Register from './components/register.vue'
import Sidebar from './components/sidebar.vue'
import Inicio from './components/inicio.vue'
import NuevaCitacion from './components/nuevacitacion.vue'
import MisCitaciones from './components/miscitaciones.vue'
import SistemaPreceptoria from './components/SistemaPreceptoria.vue'
import EstudiantesCoordinacion from './components/EstudiantesCoordinacion.vue'
import ReportesDireccion from './components/ReportesDireccion.vue'
import MiPerfil from './components/MiPerfil.vue'

const sessionStorageKey = 'tutoScript.session'
const recoveryRequested = new URLSearchParams(window.location.search).get('flujo') === 'recuperar'
const session = ref(loadSession())
const activeAuthComponent = shallowRef(Login)
const currentSection = ref('inicio')
const isWorkspaceVisible = ref(Boolean(session.value) && !recoveryRequested)
const sectionViews = {
  inicio: MiPerfil,
  'nueva-citacion': NuevaCitacion,
  'mis-citaciones': MisCitaciones,
  asistencia: SistemaPreceptoria,
  estudiantes: EstudiantesCoordinacion,
  reportes: ReportesDireccion
}
const sectionTitles = {
  'mis-citaciones': 'Mis Citaciones',
  materias: 'Materias',
  estudiantes: 'Estudiantes',
  horarios: 'Horarios de Tutorías',
  reportes: 'Reportes',
  configuracion: 'Configuración'
}
const activeSectionView = computed(() => sectionViews[currentSection.value] ?? null)

function loadSession() {
  try {
    const storedSession = localStorage.getItem(sessionStorageKey)
    return storedSession ? JSON.parse(storedSession) : null
  } catch {
    localStorage.removeItem(sessionStorageKey)
    return null
  }
}

function showRegister() {
  activeAuthComponent.value = Register
}

function showLogin() {
  activeAuthComponent.value = Login
}

function navigateTo(section) {
  currentSection.value = section
  isWorkspaceVisible.value = true
  window.scrollTo(0, 0)
}

function startSession(account) {
  const email = account.email?.trim() ?? ''
  const name = account.name?.trim() || account.username?.trim() || email.split('@')[0] || 'Usuario'
  session.value = {
    name,
    email,
    role: account.role || 'docente'
  }
  localStorage.setItem(sessionStorageKey, JSON.stringify(session.value))
  currentSection.value = 'inicio'
  isWorkspaceVisible.value = true
  window.scrollTo(0, 0)
}

function updateProfile(profile) {
  session.value = { ...session.value, ...profile }
  localStorage.setItem(sessionStorageKey, JSON.stringify(session.value))
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
          @cuenta-creada="startSession"
        />
      </KeepAlive>
    </div>

    <div v-if="isWorkspaceVisible" class="layout">
      <Sidebar
        :active-view="currentSection"
        @navigate="navigateTo"
        @logout="logout"
      />

      <main class="contenido">
        <component
          :is="activeSectionView"
          v-if="activeSectionView"
          :session="session"
          @save-profile="updateProfile"
        />

        <section v-if="!activeSectionView" class="section-placeholder">
          <h1>{{ sectionTitles[currentSection] }}</h1>
          <p>Esta sección todavía no tiene una pantalla disponible.</p>
        </section>
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