<template>
  <div class="app">
    <!-- Encabezado -->
    <header class="topbar">
      <div class="logo">
        <div class="logo-icono">📋</div>
        <div class="logo-texto">
          TUTO<br />
          <small>SCRIPT</small>
        </div>
      </div>
      <h1 class="titulo-pantalla">Mis citaciones</h1>
    </header>

    <!-- Filtros -->
    <section class="filtros">
      <div class="campo-filtro">
        <label>Fecha</label>
        <input v-model="filtroFecha" @change="resetearPagina" type="text" placeholder="dd/mm" />
      </div>

      <div class="campo-filtro">
        <label>Curso</label>
        <select v-model="filtroCurso" @change="resetearPagina">
          <option v-for="c in cursosDisponibles" :key="c">{{ c }}</option>
        </select>
      </div>

      <div class="campo-filtro">
        <label>Materia</label>
        <select v-model="filtroMateria" @change="resetearPagina">
          <option v-for="m in materiasDisponibles" :key="m">{{ m }}</option>
        </select>
      </div>

      <div class="campo-filtro">
        <label>Docente</label>
        <select v-model="filtroDocente" @change="resetearPagina">
          <option v-for="d in docentesDisponibles" :key="d">{{ d }}</option>
        </select>
      </div>

      <div class="campo-filtro">
        <label>Estado</label>
        <select v-model="filtroEstado" @change="resetearPagina">
          <option v-for="e in estadosDisponibles" :key="e">{{ e }}</option>
        </select>
      </div>

      <div class="campo-filtro">
        <label>Estudiantes</label>
        <select v-model="filtroEstudiantes" @change="resetearPagina">
          <option>Todos</option>
          <option>1-2</option>
          <option>3-4</option>
          <option>5+</option>
        </select>
      </div>

      <button class="btn-limpiar" @click="limpiarFiltros">Limpiar filtros</button>
    </section>

    <!-- Tabla de citaciones -->
    <section class="tabla-contenedor">
      <table v-if="citacionesFiltradas().length > 0" class="tabla-citaciones">
        <thead>
          <tr>
            <th>Fecha</th>
            <th>Hora</th>
            <th>Curso</th>
            <th>Materia</th>
            <th>Estudiantes</th>
            <th>Motivo</th>
            <th>Estado</th>
            <th>Notificación</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(c, index) in citacionesPagina()" :key="index">
            <td>{{ c.fecha }}</td>
            <td>{{ c.hora }}</td>
            <td>{{ c.curso }}</td>
            <td>{{ c.materia }}</td>
            <td class="centrado">{{ c.estudiantes }}</td>
            <td>{{ c.motivo }}</td>
            <td>
              <span class="badge" :class="claseEstado(c.estado)">{{ c.estado }}</span>
            </td>
            <td class="notif">
              <span v-if="c.email || c.whatsapp" class="check">✓✓</span>
              <span v-else class="guion">--</span>
              <button v-if="c.email || c.whatsapp" class="btn-ver" @click="verNotificacion(c)">
                Ver
              </button>
            </td>
          </tr>
        </tbody>
      </table>

      <p v-else class="mensaje-vacio">No se encontraron citaciones con esos filtros.</p>
    </section>

    <!-- Panel de detalle de notificación -->
    <div v-if="citacionSeleccionada" class="detalle-notif">
      <h3>Notificación · {{ citacionSeleccionada.materia }} ({{ citacionSeleccionada.curso }})</h3>
      <p><strong>Estudiantes citados:</strong> {{ citacionSeleccionada.estudiantes }}</p>
      <p><strong>Motivo:</strong> {{ citacionSeleccionada.motivo }}</p>
      <p>
        <strong>Correo electrónico:</strong>
        <span :class="citacionSeleccionada.email ? 'ok' : 'fail'">
          {{ citacionSeleccionada.email ? 'Enviado' : 'No enviado' }}
        </span>
      </p>
      <p>
        <strong>WhatsApp:</strong>
        <span :class="citacionSeleccionada.whatsapp ? 'ok' : 'fail'">
          {{ citacionSeleccionada.whatsapp ? 'Enviado' : 'No enviado' }}
        </span>
      </p>
      <button class="btn-cerrar" @click="cerrarDetalle">Cerrar</button>
    </div>

    <!-- Pie de tabla: contador y paginación -->
    <footer class="footer-tabla">
      <p class="contador">
        Mostrando {{ citacionesPagina().length }} de {{ citacionesFiltradas().length }} citaciones
      </p>
      <div class="paginacion">
        <button class="btn-pagina" @click="paginaAnterior">‹</button>
        <button
          v-for="p in paginas()"
          :key="p"
          class="btn-pagina"
          :class="{ activo: p === paginaActual }"
          @click="irAPagina(p)"
        >
          {{ p }}
        </button>
        <button class="btn-pagina" @click="paginaSiguiente">›</button>
      </div>
    </footer>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      filtroFecha: '',
      filtroCurso: 'Todos',
      filtroMateria: 'Todas',
      filtroDocente: 'Todos',
      filtroEstado: 'Todos',
      filtroEstudiantes: 'Todos',

      paginaActual: 1,
      porPagina: 5,

      citacionSeleccionada: null,

      cursosDisponibles: ['Todos', '3°', '4°', '5°', '6°'],
      materiasDisponibles: ['Todas', 'Programación', 'Matemática', 'Robótica', 'Ética y C.', 'Sistemas'],
      docentesDisponibles: ['Todos', 'Gómez', 'Pérez', 'Ruiz'],
      estadosDisponibles: ['Todos', 'Confirmada', 'Realizada', 'Proceso', 'Reprogramada', 'Cancelada', 'Ausente'],

      citaciones: [
        { fecha: '05/06', hora: '15:30', curso: '4°', materia: 'Programación', docente: 'Gómez', estudiantes: 2, motivo: 'Recuperación', estado: 'Confirmada', email: true, whatsapp: true },
        { fecha: '03/06', hora: '10:00', curso: '5°', materia: 'Matemática', docente: 'Pérez', estudiantes: 3, motivo: 'Consulta', estado: 'Realizada', email: true, whatsapp: true },
        { fecha: '01/06', hora: '11:00', curso: '4°', materia: 'Robótica', docente: 'Gómez', estudiantes: 6, motivo: 'Entrega TP', estado: 'Proceso', email: false, whatsapp: false },
        { fecha: '28/05', hora: '15:30', curso: '3°', materia: 'Ética y C.', docente: 'Ruiz', estudiantes: 2, motivo: 'Evaluación', estado: 'Reprogramada', email: true, whatsapp: true },
        { fecha: '27/05', hora: '09:00', curso: '5°', materia: 'Sistemas', docente: 'Pérez', estudiantes: 3, motivo: 'Consulta', estado: 'Cancelada', email: false, whatsapp: false },
        { fecha: '25/05', hora: '14:00', curso: '6°', materia: 'Programación', docente: 'Gómez', estudiantes: 4, motivo: 'Recuperación', estado: 'Confirmada', email: true, whatsapp: true },
        { fecha: '22/05', hora: '08:30', curso: '4°', materia: 'Matemática', docente: 'Pérez', estudiantes: 5, motivo: 'Consulta', estado: 'Realizada', email: true, whatsapp: false },
        { fecha: '20/05', hora: '13:00', curso: '3°', materia: 'Robótica', docente: 'Gómez', estudiantes: 3, motivo: 'Entrega TP', estado: 'Proceso', email: false, whatsapp: false },
        { fecha: '18/05', hora: '10:30', curso: '5°', materia: 'Ética y C.', docente: 'Ruiz', estudiantes: 2, motivo: 'Evaluación', estado: 'Confirmada', email: true, whatsapp: true },
        { fecha: '15/05', hora: '16:00', curso: '4°', materia: 'Sistemas', docente: 'Pérez', estudiantes: 6, motivo: 'Consulta', estado: 'Ausente', email: true, whatsapp: true },
        { fecha: '12/05', hora: '09:00', curso: '6°', materia: 'Programación', docente: 'Gómez', estudiantes: 1, motivo: 'Recuperación', estado: 'Realizada', email: true, whatsapp: true },
        { fecha: '10/05', hora: '11:30', curso: '3°', materia: 'Matemática', docente: 'Ruiz', estudiantes: 2, motivo: 'Consulta', estado: 'Cancelada', email: false, whatsapp: false }
      ]
    }
  },
  methods: {
    citacionesFiltradas() {
      return this.citaciones.filter((c) => {
        if (this.filtroFecha !== '' && c.fecha.indexOf(this.filtroFecha) === -1) return false
        if (this.filtroCurso !== 'Todos' && c.curso !== this.filtroCurso) return false
        if (this.filtroMateria !== 'Todas' && c.materia !== this.filtroMateria) return false
        if (this.filtroDocente !== 'Todos' && c.docente !== this.filtroDocente) return false
        if (this.filtroEstado !== 'Todos' && c.estado !== this.filtroEstado) return false
        if (this.filtroEstudiantes === '1-2' && (c.estudiantes < 1 || c.estudiantes > 2)) return false
        if (this.filtroEstudiantes === '3-4' && (c.estudiantes < 3 || c.estudiantes > 4)) return false
        if (this.filtroEstudiantes === '5+' && c.estudiantes < 5) return false
        return true
      })
    },
    citacionesPagina() {
      const filtradas = this.citacionesFiltradas()
      const inicio = (this.paginaActual - 1) * this.porPagina
      return filtradas.slice(inicio, inicio + this.porPagina)
    },
    totalPaginas() {
      const total = this.citacionesFiltradas().length
      const paginas = Math.ceil(total / this.porPagina)
      if (paginas === 0) {
        return 1
      }
      return paginas
    },
    paginas() {
      const lista = []
      for (let i = 1; i <= this.totalPaginas(); i++) {
        lista.push(i)
      }
      return lista
    },
    irAPagina(n) {
      this.paginaActual = n
    },
    paginaAnterior() {
      if (this.paginaActual > 1) {
        this.paginaActual = this.paginaActual - 1
      }
    },
    paginaSiguiente() {
      if (this.paginaActual < this.totalPaginas()) {
        this.paginaActual = this.paginaActual + 1
      }
    },
    resetearPagina() {
      this.paginaActual = 1
    },
    limpiarFiltros() {
      this.filtroFecha = ''
      this.filtroCurso = 'Todos'
      this.filtroMateria = 'Todas'
      this.filtroDocente = 'Todos'
      this.filtroEstado = 'Todos'
      this.filtroEstudiantes = 'Todos'
      this.paginaActual = 1
    },
    claseEstado(estado) {
      if (estado === 'Confirmada') return 'estado-confirmada'
      if (estado === 'Realizada') return 'estado-realizada'
      if (estado === 'Proceso') return 'estado-proceso'
      if (estado === 'Reprogramada') return 'estado-reprogramada'
      if (estado === 'Cancelada') return 'estado-cancelada'
      if (estado === 'Ausente') return 'estado-ausente'
      return ''
    },
    verNotificacion(citacion) {
      this.citacionSeleccionada = citacion
    },
    cerrarDetalle() {
      this.citacionSeleccionada = null
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
}

body {
  background: #f2f4f8;
}

.app {
  max-width: 1000px;
  margin: 30px auto;
  padding: 24px;
  font-family: 'Segoe UI', Arial, sans-serif;
  color: #263244;
}

/* Encabezado */
.topbar {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 24px;
  border-bottom: 3px solid #3a5bd9;
  padding-bottom: 16px;
}

.logo {
  display: flex;
  align-items: center;
  gap: 10px;
  background: #3a5bd9;
  color: white;
  padding: 8px 14px;
  border-radius: 10px;
}

.logo-icono {
  font-size: 22px;
}

.logo-texto {
  font-weight: bold;
  line-height: 1.1;
  letter-spacing: 1px;
}

.logo-texto small {
  font-size: 10px;
  font-weight: normal;
  letter-spacing: 2px;
  opacity: 0.85;
}

.titulo-pantalla {
  font-size: 24px;
  margin: 0;
  color: #1c2b4a;
}

/* Filtros */
.filtros {
  display: flex;
  flex-wrap: wrap;
  gap: 14px;
  align-items: flex-end;
  background: white;
  padding: 18px;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
  margin-bottom: 22px;
}

.campo-filtro {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.campo-filtro label {
  font-size: 12px;
  font-weight: bold;
  color: #55627a;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.campo-filtro input,
.campo-filtro select {
  padding: 8px 10px;
  border: 1px solid #d3d9e6;
  border-radius: 8px;
  font-size: 14px;
  min-width: 110px;
  background: #f8f9fc;
}

.btn-limpiar {
  padding: 9px 16px;
  border: none;
  border-radius: 8px;
  background: #e7ebf5;
  color: #3a5bd9;
  font-weight: bold;
  cursor: pointer;
  height: 38px;
}

.btn-limpiar:hover {
  background: #d8dff2;
}

/* Tabla */
.tabla-contenedor {
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
  padding: 8px;
  overflow-x: auto;
}

.tabla-citaciones {
  width: 100%;
  border-collapse: collapse;
  font-size: 14px;
}

.tabla-citaciones th {
  text-align: left;
  padding: 12px 14px;
  background: #eef1fa;
  color: #3a5bd9;
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.tabla-citaciones td {
  padding: 12px 14px;
  border-top: 1px solid #eef0f5;
}

.centrado {
  text-align: center;
}

/* Badges de estado */
.badge {
  display: inline-block;
  padding: 4px 10px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: bold;
}

.estado-confirmada {
  background: #dcefff;
  color: #1565c0;
}

.estado-realizada {
  background: #ddf5e4;
  color: #23884a;
}

.estado-proceso {
  background: #fff3d6;
  color: #a06a00;
}

.estado-reprogramada {
  background: #ece1ff;
  color: #6a3fc4;
}

.estado-cancelada {
  background: #fde1e1;
  color: #c62828;
}

.estado-ausente {
  background: #f0f0f0;
  color: #616161;
}

/* Notificación */
.notif {
  white-space: nowrap;
}

.check {
  color: #23884a;
  font-weight: bold;
  margin-right: 6px;
}

.guion {
  color: #9aa2b1;
  margin-right: 6px;
}

.btn-ver {
  border: none;
  background: #3a5bd9;
  color: white;
  padding: 4px 10px;
  border-radius: 6px;
  font-size: 12px;
  cursor: pointer;
}

.btn-ver:hover {
  background: #2c47b3;
}

.mensaje-vacio {
  text-align: center;
  padding: 30px;
  color: #7a8296;
}

/* Detalle de notificación */
.detalle-notif {
  background: white;
  border: 1px solid #d3d9e6;
  border-left: 5px solid #3a5bd9;
  border-radius: 10px;
  padding: 18px;
  margin-top: 18px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
}

.detalle-notif h3 {
  margin-top: 0;
  color: #1c2b4a;
}

.ok {
  color: #23884a;
  font-weight: bold;
}

.fail {
  color: #c62828;
  font-weight: bold;
}

.btn-cerrar {
  margin-top: 8px;
  border: none;
  background: #e7ebf5;
  color: #3a5bd9;
  padding: 8px 16px;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
}

/* Pie / paginación */
.footer-tabla {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 18px;
  flex-wrap: wrap;
  gap: 10px;
}

.contador {
  color: #55627a;
  font-size: 14px;
}

.paginacion {
  display: flex;
  gap: 6px;
}

.btn-pagina {
  border: 1px solid #d3d9e6;
  background: white;
  color: #3a5bd9;
  width: 34px;
  height: 34px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
}

.btn-pagina:hover {
  background: #eef1fa;
}

.activo {
  background: #3a5bd9;
  color: white;
  border-color: #3a5bd9;
}
</style>