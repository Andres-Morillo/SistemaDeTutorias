<template>
  <div class="app">
    <!-- Barra superior -->
    <header class="topbar">
      <div class="topbar-izquierda">
        <span class="topbar-icono">📁</span>
        <div>
          <h2 class="topbar-titulo">TUTORIAS Script</h2>
          <p class="topbar-subtitulo">Gestión de tutorías escolares</p>
        </div>
      </div>
      <div class="topbar-derecha">
        <span class="icono-boton">🔔</span>
        <div class="usuario-chip">
          <span class="icono-usuario">⭘</span>
          <div class="usuario-info">
            <p class="usuario-nombre">{{ usuario.nombreCompleto }}</p>
            <p class="usuario-rol">{{ usuario.rol }}</p>
          </div>
        </div>
      </div>
    </header>

    <div class="cuerpo">
      <!-- Menú lateral -->
      <aside class="menu">
        <ul class="menu-lista">
          <li
            v-for="item in menuItems"
            :key="item.nombre"
            :class="{ activo: item.nombre === itemActivo }"
            @click="seleccionarMenu(item.nombre)"
          >
            <span class="menu-icono">{{ item.icono }}</span>
            {{ item.nombre }}
          </li>
        </ul>
      </aside>

      <!-- Contenido principal -->
      <main class="contenido">
        <h1 class="titulo-pantalla">Configuración</h1>

        <div class="acordeon">
          <div
            v-for="opcion in opciones"
            :key="opcion.titulo"
            class="opcion"
          >
            <div class="opcion-encabezado" @click="alternarOpcion(opcion.titulo)">
              <span class="opcion-icono">{{ opcion.icono }}</span>
              <span class="opcion-titulo">{{ opcion.titulo }}</span>
              <span
                class="flecha"
                :class="{ abierta: opcionAbierta === opcion.titulo }"
              >
                ⌃
              </span>
            </div>

            <p v-if="opcionAbierta === opcion.titulo" class="opcion-descripcion">
              {{ opcion.descripcion }}
            </p>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      itemActivo: 'Configuración',
      opcionAbierta: 'Datos de la cuenta',
      usuario: {
        nombreCompleto: 'Juan Pérez',
        rol: 'Docente'
      },
      menuItems: [
        { nombre: 'Inicio', icono: '🏠' },
        { nombre: 'Nueva citación', icono: '➕' },
        { nombre: 'Mis citaciones', icono: '📋' },
        { nombre: 'Materias', icono: '📚' },
        { nombre: 'Estudiantes', icono: '👥' },
        { nombre: 'Horario de tutorías', icono: '🗓️' },
        { nombre: 'Reportes', icono: '📊' },
        { nombre: 'Configuración', icono: '⚙️' }
      ],
      opciones: [
        {
          titulo: 'Datos de la cuenta',
          icono: '👤',
          descripcion: 'Editá tu información personal.'
        },
        {
          titulo: 'Cambiar contraseña',
          icono: '🔑',
          descripcion: 'Actualizá tu contraseña de acceso.'
        },
        {
          titulo: 'Notificaciones',
          icono: '🔔',
          descripcion: 'Configurá cómo y cuándo recibir notificaciones.'
        },
        {
          titulo: 'Preferencias',
          icono: '⚙️',
          descripcion: 'Ajustá las preferencias generales de la aplicación.'
        },
        {
          titulo: 'Seguridad',
          icono: '🛡️',
          descripcion: 'Revisá la seguridad y los accesos de tu cuenta.'
        }
      ]
    }
  },
  methods: {
    seleccionarMenu(nombre) {
      this.itemActivo = nombre
    },
    alternarOpcion(titulo) {
      if (this.opcionAbierta === titulo) {
        this.opcionAbierta = ''
      } else {
        this.opcionAbierta = titulo
      }
    }
  }
}
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
}

* {
  box-sizing: border-box;
}

.app {
  min-height: 100vh;
  width: 100vw;
  font-family: 'Segoe UI', Arial, sans-serif;
  background-color: #f4f6f8;
}

/* Barra superior */
.topbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 18px 40px;
  background-color: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
  position: sticky;
  top: 0;
  z-index: 10;
}

.topbar-izquierda {
  display: flex;
  align-items: center;
  gap: 14px;
}

.topbar-icono {
  font-size: 26px;
  background-color: #e4f0e6;
  padding: 8px;
  border-radius: 10px;
}

.topbar-titulo {
  margin: 0;
  font-size: 19px;
  color: #1f4e2c;
  letter-spacing: 0.3px;
}

.topbar-subtitulo {
  margin: 2px 0 0 0;
  font-size: 12.5px;
  color: #8a8a8a;
}

.topbar-derecha {
  display: flex;
  align-items: center;
  gap: 18px;
}

.icono-boton {
  font-size: 19px;
  cursor: pointer;
  width: 38px;
  height: 38px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: background-color 0.2s;
}

.icono-boton:hover {
  background-color: #f0f2f4;
}

.usuario-chip {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 6px 14px 6px 6px;
  border-radius: 24px;
  background-color: #f4f6f8;
  cursor: pointer;
  transition: background-color 0.2s;
}

.usuario-chip:hover {
  background-color: #e9ecef;
}

.icono-usuario {
  font-size: 28px;
  color: #2e6b3e;
}

.usuario-info {
  text-align: left;
  line-height: 1.25;
}

.usuario-nombre {
  margin: 0;
  font-size: 13.5px;
  font-weight: 600;
  color: #222;
}

.usuario-rol {
  margin: 0;
  font-size: 11.5px;
  color: #8a8a8a;
}

/* Cuerpo: menú + contenido */
.cuerpo {
  display: flex;
  width: 100%;
  min-height: calc(100vh - 76px);
}

/* Menú lateral */
.menu {
  width: 18vw;
  min-width: 230px;
  background-color: white;
  box-shadow: 2px 0 8px rgba(0, 0, 0, 0.04);
  padding: 24px 0;
}

.menu-lista {
  list-style: none;
  margin: 0;
  padding: 0 12px;
}

.menu-lista li {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 13px 16px;
  margin-bottom: 4px;
  font-size: 14.5px;
  color: #444;
  cursor: pointer;
  border-radius: 10px;
  transition: background-color 0.2s, color 0.2s;
}

.menu-lista li:hover {
  background-color: #f0f5f0;
}

.menu-lista li.activo {
  background-color: #2e6b3e;
  color: white;
  font-weight: 600;
  box-shadow: 0 3px 8px rgba(46, 107, 62, 0.3);
}

.menu-icono {
  font-size: 16px;
}

/* Contenido principal */
.contenido {
  flex: 1;
  padding: 4vw 5vw;
}

.titulo-pantalla {
  font-size: 30px;
  margin: 0 0 28px 0;
  color: #1a1a1a;
  font-weight: 700;
}

/* Acordeón */
.acordeon {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.opcion {
  background-color: white;
  border-radius: 12px;
  box-shadow: 0 3px 12px rgba(0, 0, 0, 0.05);
  overflow: hidden;
}

.opcion-encabezado {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 20px 26px;
  cursor: pointer;
}

.opcion-encabezado:hover {
  background-color: #f8faf8;
}

.opcion-icono {
  font-size: 22px;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #eef3ee;
  border-radius: 50%;
}

.opcion-titulo {
  flex: 1;
  font-size: 16px;
  font-weight: 600;
  color: #222;
}

.flecha {
  font-size: 16px;
  color: #999;
  transform: rotate(180deg);
  transition: transform 0.2s;
}

.flecha.abierta {
  transform: rotate(0deg);
  color: #2e6b3e;
}

.opcion-descripcion {
  margin: 0;
  padding: 0 26px 22px 82px;
  font-size: 14px;
  color: #777;
  line-height: 1.5;
}
</style>