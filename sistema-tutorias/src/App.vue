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

        <div class="menu-contacto">
          <p class="contacto-titulo">Contacto con la institución</p>
          <p>📧 {{ contacto.email }}</p>
          <p>📞 {{ contacto.telefono }}</p>
          <p>📍 {{ contacto.direccion }}</p>
        </div>
      </aside>

      <!-- Contenido principal -->
      <main class="contenido">
        <!-- Encabezado: título + botón guardar, igual que en el boceto -->
        <div class="encabezado">
          <h1 class="titulo-pantalla">Mi perfil</h1>
          <button class="boton-guardar" @click="guardarCambios">
            Guardar Cambios
          </button>
        </div>

        <p v-if="mensajeGuardado" class="mensaje-guardado">
          ✔ Los cambios se guardaron correctamente.
        </p>

        <div class="perfil-grid">
          <!-- Datos personales -->
          <section class="tarjeta datos">
            <h3>Datos Personales</h3>

            <label>Nombre completo</label>
            <input v-model="usuario.nombreCompleto" type="text" />

            <label>Email</label>
            <input v-model="usuario.email" type="text" />

            <label>Teléfono</label>
            <input v-model="usuario.telefono" type="text" />

            <label>Rol</label>
            <input v-model="usuario.rol" type="text" />

            <label>Materia</label>
            <input v-model="usuario.materia" type="text" />

            <label>Cursos a cargo</label>
            <input v-model="usuario.cursosACargo" type="text" />
          </section>

          <!-- Foto de perfil -->
          <section class="tarjeta foto">
            <div class="foto-circulo">
              <span v-if="!fotoCargada">🙂</span>
              <span v-else>📷</span>
            </div>

            <button class="boton-foto" @click="cambiarFoto">
              Cambiar foto
            </button>

            <p class="foto-info">Formato permitido: jpg, png</p>
            <p class="foto-info">Tamaño máximo: 20 mb</p>
          </section>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      itemActivo: 'Inicio',
      fotoCargada: false,
      mensajeGuardado: false,
      usuario: {
        nombreCompleto: 'Juan Pérez',
        email: 'jperez@escuelas....',
        telefono: '3548 70 6458',
        rol: 'Docente',
        materia: 'Programación y Robótica',
        cursosACargo: '5°, 6° y 3° ro'
      },
      contacto: {
        email: 'escuelas...',
        telefono: '03548 60 7080',
        direccion: 'La falda, Córdoba'
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
      ]
    }
  },
  methods: {
    seleccionarMenu(nombre) {
      this.itemActivo = nombre
    },
    cambiarFoto() {
      this.fotoCargada = !this.fotoCargada
    },
    guardarCambios() {
      this.mensajeGuardado = true
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Lora:wght@500;600;700&family=Inter:wght@400;500;600;700&display=swap');

html, body {
  margin: 0;
  padding: 0;
}

* {
  box-sizing: border-box;
}

:root {
  --tinta: #22301f;
  --verde: #3a6b4a;
  --verde-oscuro: #244231;
  --verde-suave: #e7efe6;
  --fondo: #f2f1e9;
  --borde: #e2e0d4;
  --dorado: #a9821f;
  --dorado-suave: #f6ecd2;
  --texto-mudo: #7c7a6f;
}

.app {
  min-height: 100vh;
  width: 100vw;
  font-family: 'Inter', 'Segoe UI', Arial, sans-serif;
  background-color: var(--fondo);
  color: var(--tinta);
}

/* Barra superior */
.topbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 40px;
  background-color: #ffffff;
  border-bottom: 1px solid var(--borde);
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
  font-size: 22px;
  background-color: var(--verde-suave);
  padding: 9px;
  border-radius: 10px;
  line-height: 1;
}

.topbar-titulo {
  margin: 0;
  font-family: 'Lora', serif;
  font-size: 18px;
  font-weight: 600;
  color: var(--verde-oscuro);
  letter-spacing: 0.2px;
}

.topbar-subtitulo {
  margin: 3px 0 0 0;
  font-size: 12px;
  color: var(--texto-mudo);
}

.topbar-derecha {
  display: flex;
  align-items: center;
  gap: 16px;
}

.icono-boton {
  font-size: 18px;
  cursor: pointer;
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: background-color 0.2s;
}

.icono-boton:hover {
  background-color: var(--verde-suave);
}

.usuario-chip {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 6px 14px 6px 6px;
  border-radius: 24px;
  background-color: var(--fondo);
  border: 1px solid var(--borde);
  cursor: pointer;
  transition: border-color 0.2s;
}

.usuario-chip:hover {
  border-color: var(--verde);
}

.icono-usuario {
  font-size: 26px;
  color: var(--verde);
}

.usuario-info {
  text-align: left;
  line-height: 1.25;
}

.usuario-nombre {
  margin: 0;
  font-size: 13px;
  font-weight: 600;
  color: var(--tinta);
}

.usuario-rol {
  margin: 0;
  font-size: 11px;
  color: var(--texto-mudo);
}

/* Cuerpo: menú + contenido */
.cuerpo {
  display: flex;
  width: 100%;
  min-height: calc(100vh - 70px);
}

/* Menú lateral */
.menu {
  width: 18vw;
  min-width: 230px;
  background-color: #ffffff;
  border-right: 1px solid var(--borde);
  padding: 22px 0;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.menu-lista {
  list-style: none;
  margin: 0;
  padding: 0 12px;
}

.menu-lista li {
  position: relative;
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 16px 12px 18px;
  margin-bottom: 2px;
  font-size: 14px;
  font-weight: 500;
  color: #4a4a42;
  cursor: pointer;
  border-radius: 8px;
  transition: background-color 0.2s, color 0.2s;
}

.menu-lista li:hover {
  background-color: var(--verde-suave);
}

.menu-lista li.activo {
  background-color: var(--verde-suave);
  color: var(--verde-oscuro);
  font-weight: 700;
}

/* "pestaña" que marca el ítem activo, como un separador de carpeta */
.menu-lista li.activo::before {
  content: '';
  position: absolute;
  left: 0;
  top: 8px;
  bottom: 8px;
  width: 3px;
  border-radius: 3px;
  background-color: var(--verde);
}

.menu-icono {
  font-size: 15px;
}

.menu-contacto {
  padding: 18px 24px;
  border-top: 1px solid var(--borde);
  font-size: 11.5px;
  color: var(--texto-mudo);
  line-height: 1.8;
}

.contacto-titulo {
  font-weight: 700;
  color: #4a4a42;
  margin-bottom: 6px;
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

/* Contenido principal */
.contenido {
  flex: 1;
  padding: 4vw 5vw;
}

/* Encabezado: título + botón guardar (arriba, como en el boceto) */
.encabezado {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 22px;
  padding-bottom: 18px;
  border-bottom: 1px dashed var(--borde);
  flex-wrap: wrap;
  gap: 16px;
}

.titulo-pantalla {
  font-family: 'Lora', serif;
  font-size: 30px;
  margin: 0;
  color: var(--tinta);
  font-weight: 600;
  letter-spacing: 0.2px;
}

.boton-guardar {
  padding: 12px 30px;
  background-color: var(--verde);
  border: none;
  border-radius: 8px;
  font-weight: 600;
  font-size: 14px;
  color: #ffffff;
  cursor: pointer;
  box-shadow: 0 3px 10px rgba(36, 66, 49, 0.25);
  transition: background-color 0.2s, transform 0.15s;
}

.boton-guardar:hover {
  background-color: var(--verde-oscuro);
  transform: translateY(-1px);
}

.mensaje-guardado {
  color: var(--verde-oscuro);
  font-weight: 600;
  font-size: 14px;
  background-color: var(--verde-suave);
  border: 1px solid #cfe0cf;
  padding: 10px 18px;
  border-radius: 8px;
  margin: 0 0 24px 0;
  display: inline-block;
}

.perfil-grid {
  display: flex;
  gap: 24px;
  flex-wrap: wrap;
}

.tarjeta {
  background-color: #ffffff;
  border: 1px solid var(--borde);
  border-radius: 14px;
  padding: 30px;
  transition: box-shadow 0.2s, border-color 0.2s;
}

.tarjeta:hover {
  box-shadow: 0 8px 24px rgba(34, 48, 31, 0.06);
  border-color: #d6d3c4;
}

.datos {
  flex: 2;
  min-width: 340px;
  text-align: left;
}

.datos h3 {
  margin-top: 0;
  margin-bottom: 20px;
  font-family: 'Lora', serif;
  color: var(--verde-oscuro);
  font-size: 17px;
  font-weight: 600;
  padding-bottom: 12px;
  border-bottom: 2px solid var(--verde-suave);
}

.datos label {
  display: block;
  font-size: 11.5px;
  font-weight: 600;
  margin-bottom: 6px;
  color: var(--texto-mudo);
  text-transform: uppercase;
  letter-spacing: 0.4px;
}

.datos input {
  display: block;
  width: 100%;
  padding: 12px 14px;
  margin-bottom: 18px;
  border: 1.5px solid var(--borde);
  border-radius: 8px;
  font-family: 'Inter', sans-serif;
  font-size: 14px;
  color: var(--tinta);
  background-color: #fdfdfb;
  transition: border-color 0.2s, box-shadow 0.2s, background-color 0.2s;
}

.datos input:focus {
  outline: none;
  border-color: var(--verde);
  background-color: #ffffff;
  box-shadow: 0 0 0 3px rgba(58, 107, 74, 0.12);
}

.foto {
  flex: 1;
  min-width: 250px;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.foto-circulo {
  width: 128px;
  height: 128px;
  border-radius: 50%;
  background-color: var(--verde-suave);
  border: 2px dashed #c3d6c5;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 48px;
  margin-bottom: 22px;
}

.boton-foto {
  padding: 10px 22px;
  background-color: var(--dorado-suave);
  border: 1.5px solid #dcc27c;
  border-radius: 24px;
  font-weight: 600;
  font-size: 13px;
  color: var(--dorado);
  cursor: pointer;
  margin-bottom: 20px;
  transition: background-color 0.2s, transform 0.15s;
}

.boton-foto:hover {
  background-color: #f0e2b6;
  transform: translateY(-1px);
}

.foto-info {
  font-size: 11.5px;
  color: var(--texto-mudo);
  margin: 3px 0;
}
</style>