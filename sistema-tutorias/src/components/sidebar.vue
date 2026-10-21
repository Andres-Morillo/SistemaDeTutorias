<script setup>
import { computed } from 'vue'

const props = defineProps({
  activeView: {
    type: String,
    default: 'inicio'
  },
  role: {
    type: String,
    default: 'docente'
  }
})

const emit = defineEmits(['navigate', 'logout'])

const menuByRole = {
  estudiante: [{ id: 'inicio', icon: '🏠', label: 'Mi inicio' }],
  docente: [
    { id: 'inicio', icon: '🏠', label: 'Panel principal' },
    { id: 'perfil-docente', icon: '♙', label: 'Mi perfil' },
    { id: 'nueva-citacion', icon: '➕', label: 'Nueva citación' },
    { id: 'mis-citaciones', icon: '📋', label: 'Mis citaciones' },
    { id: 'materias', icon: '📖', label: 'Materias' },
    { id: 'estudiantes-docente', icon: '👥', label: 'Mis estudiantes' },
    { id: 'horarios', icon: '📅', label: 'Horario de tutorías' },
    { id: 'reporte-docente', icon: '📊', label: 'Reportes' },
    { id: 'configuracion', icon: '⚙️', label: 'Configuración' }
  ],
  preceptor: [
    { id: 'inicio', icon: '🏠', label: 'Panel principal' },
    { id: 'asistencia', icon: '✓', label: 'Asistencia' },
    { id: 'estudiantes-preceptoria', icon: '👥', label: 'Estudiantes' },
    { id: 'tutorias-preceptoria', icon: '📅', label: 'Tutorías' },
    { id: 'configuracion', icon: '⚙️', label: 'Configuración' }
  ],
  director: [
    { id: 'inicio', icon: '🏠', label: 'Panel principal' },
    { id: 'estudiantes-direccion', icon: '👥', label: 'Estudiantes' },
    { id: 'profesores-direccion', icon: '👩‍🏫', label: 'Profesores' },
    { id: 'reportes', icon: '📊', label: 'Reportes' },
    { id: 'configuracion', icon: '⚙️', label: 'Configuración' }
  ],
  coordinador: [
    { id: 'inicio', icon: '🏠', label: 'Panel principal' },
    { id: 'estudiantes-coordinacion', icon: '👥', label: 'Estudiantes' },
    { id: 'profesores-coordinacion', icon: '👩‍🏫', label: 'Profesores' },
    { id: 'observaciones-coordinacion', icon: '📝', label: 'Observaciones' },
    { id: 'tutorias-coordinacion', icon: '📅', label: 'Tutorías' },
    { id: 'configuracion', icon: '⚙️', label: 'Configuración' }
  ]
}

const menuItems = computed(() => menuByRole[props.role] ?? menuByRole.docente)
</script>

<template>
  <aside class="sidebar">
    <div class="sidebar-header">
      <div class="brand-mark" aria-hidden="true">
        <span class="brand-person brand-person-green"></span>
        <span class="brand-person brand-person-yellow"></span>
      </div>
      <div>
        <h2>Tuto Script</h2>
        <p>Gestión de tutorías</p>
      </div>
    </div>
    
    <nav class="sidebar-menu" aria-label="Secciones disponibles">
      <button
        v-for="item in menuItems"
        :key="item.id"
        type="button"
        class="menu-item"
        :class="{ 'is-active': activeView === item.id }"
        @click="emit('navigate', item.id)"
      >
        <span aria-hidden="true">{{ item.icon }}</span>
        <span>{{ item.label }}</span>
      </button>
    </nav>
     
    <div class="sidebar-footer">
      <button class="logout-btn" type="button" @click="emit('logout')">
        Cerrar sesión
      </button>
    </div>
  </aside>
</template>

<style scoped>
.sidebar {
  width: 250px;
  height: 100dvh;
  padding: 18px 14px;
  border-right: 2px solid #231f2c;
  background: #fffdf9;
  color: #231f2c;
  display: flex;
  flex-direction: column;
  position: fixed;
  left: 0;
  top: 0;
  z-index: 10;
}

.sidebar-header {
  display: flex;
  align-items: center;
  gap: 11px;
  padding: 4px 4px 18px;
  border-bottom: 1.5px solid #231f2c;
}

.brand-mark {
  position: relative;
  width: 46px;
  height: 46px;
  flex: 0 0 46px;
  overflow: hidden;
  border: 2px solid #231f2c;
  border-radius: 14px;
  background: #fffdf9;
}

.brand-person {
  position: absolute;
  bottom: 10px;
  width: 15px;
  height: 15px;
  border-radius: 50%;
}

.brand-person::after {
  position: absolute;
  bottom: -12px;
  left: 50%;
  width: 23px;
  height: 13px;
  border-radius: 14px 14px 0 0;
  content: '';
  transform: translateX(-50%);
}

.brand-person-green {
  left: 8px;
}

.brand-person-green,
.brand-person-green::after {
  background: #6b9a55;
}

.brand-person-yellow {
  right: 8px;
}

.brand-person-yellow,
.brand-person-yellow::after {
  background: #e5b54c;
}

.sidebar-header h2 {
  margin: 0;
  font-size: 19px;
}

.sidebar-header p {
  margin: 3px 0 0;
  color: #6b6577;
  font-size: 11px;
}

.sidebar-menu {
  display: flex;
  flex-direction: column;
  flex: 1 1 auto;
  gap: 4px;
  margin-top: 18px;
  min-height: 0;
  overflow-y: auto;
}

.menu-item {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 12px;
  min-height: 42px;
  padding: 9px 11px;
  color: #6b6577;
  text-decoration: none;
  text-align: left;
  font: inherit;
  font-size: 13px;
  font-weight: 600;
  border-radius: 9px;
  border: 0;
  background: transparent;
  cursor: pointer;
  transition: background-color 160ms ease, color 160ms ease;
}

.menu-item:hover,
.menu-item.is-active {
  background: #e2eeda;
  color: #46733c;
}

.menu-item:focus-visible,
.logout-btn:focus-visible {
  outline: 3px solid rgb(107 154 85 / 35%);
  outline-offset: 2px;
}

.sidebar-footer {
  margin-top: auto;
  padding: 12px 4px 0;
  border-top: 1px solid #e7e0d3;
}

.logout-btn {
  width: 100%;
  padding: 10px 11px;
  border: 1.5px solid #231f2c;
  border-radius: 9px;
  background: #fffdf9;
  color: #914d48;
  font: inherit;
  font-size: 13px;
  font-weight: 700;
  cursor: pointer;
}

.logout-btn:hover {
  background: #f6dddd;
}

@media (max-width: 700px) {
  .sidebar {
    position: static;
    width: 100%;
    height: auto;
    padding: 13px 16px;
    border-right: 0;
    border-bottom: 2px solid #231f2c;
  }

  .sidebar-menu {
    display: grid;
    flex: 0 1 180px;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    max-height: 180px;
    margin-top: 12px;
  }

  .sidebar-footer {
    margin-top: 10px;
    padding-top: 10px;
  }
}
</style scoped>