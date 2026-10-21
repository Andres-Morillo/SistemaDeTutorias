<template>
  <section class="subjects-page" aria-labelledby="subjects-title">
    <div class="subjects-shell">
      <header class="page-heading">
        <div>
          <p class="eyebrow">ACOMPAÑAMIENTO ESCOLAR</p>
          <h1 id="subjects-title">Administracion de materias</h1>
          <p class="subtitle">Consultá las materias, docentes y horarios por año.</p>
        </div>
        <span class="heading-mark" aria-hidden="true">▦</span>
      </header>

      <section class="results-section" aria-labelledby="results-title">
        <div class="section-heading results-heading">
          <div>
            <h2 id="results-title">Materias registradas</h2>
            <p class="results-count">
              {{ filteredSubjects.length }}
              {{ filteredSubjects.length === 1 ? 'resultado' : 'resultados' }}
            </p>
          </div>
        </div>

        <label class="search-control">
          <span class="search-icon" aria-hidden="true">⌕</span>
          <input
            v-model="search"
            type="search"
            aria-label="Buscar materias"
            placeholder="Buscar materia, año, docente o día y horario"
          />
        </label>

        <div class="table-scroll">
          <table class="subjects-table">
            <thead>
              <tr>
                <th scope="col">Materia</th>
                <th scope="col">Año</th>
                <th scope="col">Docente</th>
                <th scope="col">Día y horario</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="subject in filteredSubjects" :key="subject.id">
                <td class="subject-name">{{ subject.name }}</td>
                <td><span class="year-badge">{{ subject.year }}°</span></td>
                <td>{{ subject.teacher }}</td>
                <td class="schedule-cell">{{ subject.schedule }}</td>
              </tr>
              <tr v-if="filteredSubjects.length === 0">
                <td class="empty-state" colspan="4">
                  No hay materias que coincidan con la búsqueda.
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </section>
    </div>
  </section>
</template>

<script setup>
import { computed, ref } from 'vue'

const search = ref('')

const subjects = [
  { id: 1, name: 'Matemática', year: 1, teacher: 'Ana Torres', schedule: 'Lunes · 08:00 a 09:20' },
  { id: 2, name: 'Lengua y Literatura', year: 1, teacher: 'Carla Méndez', schedule: 'Martes · 09:30 a 10:50' },
  { id: 3, name: 'Historia', year: 2, teacher: 'Diego Romero', schedule: 'Miércoles · 10:00 a 11:20' },
  { id: 4, name: 'Biología', year: 3, teacher: 'Lucía Fernández', schedule: 'Jueves · 08:00 a 09:20' },
  { id: 5, name: 'Física', year: 4, teacher: 'Martín Acosta', schedule: 'Viernes · 11:00 a 12:20' },
  { id: 6, name: 'Programación', year: 4, teacher: 'Juan Pérez', schedule: 'Lunes · 13:00 a 14:20' },
  { id: 7, name: 'Química', year: 5, teacher: 'Sofía Benítez', schedule: 'Martes · 14:00 a 15:20' },
  { id: 8, name: 'Economía', year: 6, teacher: 'Pablo Ríos', schedule: 'Jueves · 10:30 a 11:50' },
]

const filteredSubjects = computed(() => {
  const query = normalize(search.value.trim())
  if (!query) return subjects

  return subjects.filter((subject) =>
    normalize(`${subject.name} ${subject.year} ${subject.teacher} ${subject.schedule}`).includes(query),
  )
})

function normalize(value) {
  return value.normalize('NFD').replace(/[\u0300-\u036f]/g, '').toLocaleLowerCase('es')
}
</script>

<style scoped>
.subjects-page {
  min-height: 100vh;
  padding: clamp(24px, 4vw, 48px) 20px;
  background: #faf5ec;
  color: #231f2c;
  font-family: Arial, Helvetica, sans-serif;
}

.subjects-shell {
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

.results-section {
  padding-top: 24px;
}

.section-heading h2 {
  margin: 0;
  font-size: 18px;
  font-weight: 700;
}

.results-heading {
  margin-bottom: 14px;
}

.search-control {
  display: flex;
  align-items: center;
  gap: 10px;
  min-height: 46px;
  margin-bottom: 16px;
  padding: 0 13px;
  border: 1px solid #c9c2b7;
  border-radius: 8px;
  background: #fffdf9;
  color: #6b6577;
}

.search-control:focus-within {
  outline: 3px solid rgb(107 154 85 / 18%);
  border-color: #6b9a55;
}

.search-icon {
  font-size: 22px;
  line-height: 1;
}

.search-control input {
  width: 100%;
  min-width: 0;
  border: 0;
  outline: 0;
  background: transparent;
  color: #231f2c;
  font: inherit;
  font-size: 14px;
}

.search-control input::placeholder {
  color: #8a8490;
}

.table-scroll {
  width: 100%;
  overflow-x: auto;
  border: 1px solid rgb(35 31 44 / 14%);
  border-radius: 10px;
  background: #fffdf9;
}

.subjects-table {
  width: 100%;
  min-width: 620px;
  border-collapse: collapse;
  text-align: left;
}

.subjects-table th,
.subjects-table td {
  padding: 14px 16px;
  border-bottom: 1px solid rgb(35 31 44 / 9%);
  font-size: 13px;
  vertical-align: middle;
}

.subjects-table th {
  background: #f5efe4;
  color: #5f5965;
  font-size: 12px;
  font-weight: 700;
  white-space: nowrap;
}

.subjects-table tbody tr:last-child td {
  border-bottom: 0;
}

.subjects-table tbody tr:hover {
  background: #fffaf0;
}

.subject-name {
  font-weight: 700;
}

.year-badge {
  display: inline-grid;
  min-width: 34px;
  height: 28px;
  place-items: center;
  border-radius: 999px;
  background: #e8efe2;
  color: #46663a;
  font-weight: 700;
}

.schedule-cell {
  white-space: nowrap;
}

.empty-state {
  padding: 30px 16px !important;
  color: #6b6577;
  text-align: center;
}

@media (max-width: 420px) {
  .page-heading h1 {
    font-size: 28px;
  }
}
</style>
