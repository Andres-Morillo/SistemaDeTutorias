<template>
  <div class="attendance-page">
    <main class="content">
      <header class="topbar">
        <div>
          <span class="eyebrow">Seguimiento de tutorías</span>
          <h1>Asistencia</h1>
          <p>Registrá la asistencia de tus estudiantes.</p>
        </div>
        <button class="bell">♧<b>3</b></button>
      </header>
      <section class="attendance-card">
        <div class="card-heading">
          <div>
            <span class="section-label">Registro diario</span>
            <h2>Control de asistencia</h2>
          </div>
          <span class="status">Clase de hoy</span>
        </div>
        <div class="filters">
          <label>Fecha<input v-model="date" type="date" /></label
          ><label
            >Curso<select v-model="course">
              <option>1</option>
              <option>2</option>
              <option>3</option>
              <option>4</option>
              <option>5</option>
              <option>6</option>
            </select></label
          >
        </div>
        <div class="list-heading">
          <span>Estudiante</span><span>Estado</span>
        </div>
        <div v-for="student in students" :key="student.id" class="student-row">
          <span class="student-name"
            ><span class="avatar">{{ initials(student.name) }}</span
            >{{ student.name }}</span
          >
          <div class="state-buttons">
            <button
              :class="{ selected: student.status === 'Presente' }"
              type="button"
              @click="student.status = 'Presente'"
            >
              Presente</button
            ><button
              :class="{ absent: student.status === 'Ausente' }"
              type="button"
              @click="student.status = 'Ausente'"
            >
              Ausente</button
            ><button
              :class="{ pending: student.status === 'Pendiente' }"
              type="button"
              @click="student.status = 'Pendiente'"
            >
              Pendiente
            </button>
          </div>
        </div>
        <div class="summary">
          <span
            ><b>{{ presentCount }}</b> presentes</span
          ><span
            ><b>{{ absentCount }}</b> ausentes</span
          ><span
            ><b>{{ pendingCount }}</b> pendientes</span
          >
        </div>
        <button class="save" type="button" @click="saveAttendance">
          Guardar asistencia <span>→</span>
        </button>
        <p v-if="message" class="message" role="status">{{ message }}</p>
      </section>
    </main>
  </div>
</template>

<script setup>
import { computed, reactive, ref, watch } from "vue";
const date = ref("2026-10-16");
const course = ref("2");
const message = ref("");
const sampleStudents = [
  { id: 1, name: "Juan Perez", status: "Pendiente" },
  { id: 2, name: "Marta Gomez", status: "Presente" },
  { id: 3, name: "Leandro Diaz", status: "Ausente" },
  { id: 4, name: "Camila Ruiz", status: "Presente" },
];
const students = reactive(sampleStudents.map((student) => ({ ...student })));
const presentCount = computed(
  () => students.filter((student) => student.status === "Presente").length,
);
const absentCount = computed(
  () => students.filter((student) => student.status === "Ausente").length,
);
const pendingCount = computed(
  () => students.filter((student) => student.status === "Pendiente").length,
);
watch([date, course], loadAttendance, { immediate: true });
function loadAttendance() {
  const key = `tutoScript.attendance.${date.value}.${course.value}`;
  try {
    const stored = localStorage.getItem(key);
    const records = stored ? JSON.parse(stored) : sampleStudents;
    if (!Array.isArray(records)) throw new TypeError("El registro de asistencia no es válido.");
    students.splice(0, students.length, ...records);
    message.value = "";
  } catch (error) {
    console.error("No se pudo cargar la asistencia.", error);
    students.splice(0, students.length, ...sampleStudents.map((student) => ({ ...student })));
    message.value = "No se pudo cargar la asistencia guardada; se muestran datos de ejemplo.";
  }
}
function saveAttendance() {
  const key = `tutoScript.attendance.${date.value}.${course.value}`;
  try {
    localStorage.setItem(key, JSON.stringify(students));
    message.value = "Asistencia guardada en este navegador.";
  } catch (error) {
    console.error("No se pudo guardar la asistencia.", error);
    message.value = "No se pudo guardar la asistencia. Revisá el almacenamiento del navegador.";
  }
}
const initials = (name) =>
  name
    .split(" ")
    .map((word) => word[0])
    .join("")
    .slice(0, 2);
</script>

<style scoped>
:global(*) {
  box-sizing: border-box;
}
:global(body) {
  margin: 0;
  background: #faf5ec;
  color: #231f2c;
  font-family: Arial, Helvetica, sans-serif;
}
.attendance-page {
  min-height: 100vh;
  margin: calc(-1 * clamp(1rem, 3vw, 2rem));
  padding: 38px clamp(20px, 5vw, 64px);
  background: #faf5ec;
}
.sidebar {
  width: 266px;
  min-height: 100vh;
  padding: 24px 18px;
  border-right: 2px solid;
  background: #fffdf9;
  display: flex;
  flex-direction: column;
}
.brand {
  display: flex;
  align-items: center;
  gap: 11px;
  color: inherit;
  text-decoration: none;
}
.brand strong {
  display: block;
  font-size: 20px;
}
.brand small,
.contact p {
  display: block;
  margin-top: 3px;
  color: #6b6577;
  font-size: 11px;
}
.logo {
  display: grid;
  place-items: center;
  width: 43px;
  height: 43px;
  border: 2px solid;
  border-radius: 12px;
  background: #dceef9;
}
.menu {
  display: grid;
  gap: 5px;
  margin-top: 42px;
}
.menu a {
  display: flex;
  gap: 12px;
  padding: 11px 12px;
  border-radius: 10px;
  color: #6b6577;
  text-decoration: none;
  font-size: 13px;
  font-weight: 600;
}
.menu a.active,
.menu a:hover {
  background: #f4eefb;
  color: #6c4b9c;
}
.contact {
  margin-top: auto;
  padding: 17px;
  border: 1.5px solid;
  border-radius: 13px;
  background: #faf5ec;
  font-size: 12px;
}
.contact b {
  font-size: 14px;
}
.contact p {
  margin: 8px 0;
  font-size: 12px;
}
.content {
  width: min(100%, 1000px);
  margin: 0 auto;
}
.topbar {
  display: flex;
  justify-content: space-between;
}
.eyebrow,
.section-label {
  color: #6c4b9c;
  font-size: 12px;
  font-weight: 700;
}
.topbar h1 {
  margin: 9px 0 5px;
  font-size: 32px;
  font-style: italic;
}
.topbar p {
  margin: 0;
  color: #6b6577;
}
.bell {
  position: relative;
  width: 40px;
  height: 40px;
  border: 1.5px solid;
  border-radius: 50%;
  background: #fffdf9;
  font-size: 20px;
}
.bell b {
  position: absolute;
  right: -4px;
  top: -5px;
  width: 17px;
  height: 17px;
  border: 1px solid;
  border-radius: 50%;
  background: #e5b54c;
  font-size: 10px;
  line-height: 16px;
}
.attendance-card {
  margin-top: 30px;
  padding: 27px;
  border: 1.5px solid;
  border-radius: 18px;
  background: #fffdf9;
}
.card-heading {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}
.card-heading h2 {
  margin: 6px 0 0;
  font-size: 22px;
  font-style: italic;
}
.status {
  padding: 7px 10px;
  border-radius: 99px;
  background: #e2eeda;
  color: #46733c;
  font-size: 11px;
  font-weight: 700;
}
.filters {
  display: flex;
  gap: 16px;
  margin-top: 27px;
  padding: 17px;
  border-radius: 12px;
  background: #faf5ec;
}
.filters label {
  display: flex;
  flex-direction: column;
  gap: 7px;
  color: #6b6577;
  font-size: 11px;
  font-weight: 700;
}
.filters input,
.filters select {
  height: 41px;
  padding: 0 12px;
  border: 1.5px solid;
  border-radius: 9px;
  background: #fffdf9;
  color: #231f2c;
  font: inherit;
}
.list-heading {
  display: grid;
  grid-template-columns: 1fr 320px;
  padding: 21px 8px 11px;
  color: #6b6577;
  font-size: 11px;
  font-weight: 700;
  border-bottom: 1.5px solid;
}
.student-row {
  display: grid;
  grid-template-columns: 1fr 320px;
  align-items: center;
  padding: 15px 8px;
  border-bottom: 1px solid #e7e0d3;
}
.student-name {
  display: flex;
  align-items: center;
  gap: 10px;
  font-weight: 600;
}
.avatar {
  display: grid;
  place-items: center;
  width: 32px;
  height: 32px;
  border: 1px solid;
  border-radius: 50%;
  background: #f4eefb;
  color: #6c4b9c;
  font-size: 10px;
  font-weight: 700;
}
.state-buttons {
  display: flex;
  gap: 6px;
}
.state-buttons button {
  padding: 7px 9px;
  border: 1px solid #bfb7aa;
  border-radius: 8px;
  background: #fffdf9;
  color: #6b6577;
  font-size: 10px;
  cursor: pointer;
}
.state-buttons button.selected {
  border-color: #46733c;
  background: #e2eeda;
  color: #46733c;
  font-weight: 700;
}
.state-buttons button.absent {
  border-color: #a65b55;
  background: #f6dddd;
  color: #914d48;
  font-weight: 700;
}
.state-buttons button.pending {
  border-color: #ad852c;
  background: #faebc7;
  color: #86651d;
  font-weight: 700;
}
.summary {
  display: flex;
  gap: 20px;
  margin-top: 19px;
  color: #6b6577;
  font-size: 12px;
}
.summary b {
  color: #231f2c;
  font-size: 17px;
  margin-right: 4px;
}
.save {
  display: block;
  margin: 25px 0 0 auto;
  padding: 13px 18px;
  border: 1.5px solid;
  border-radius: 10px;
  background: #6b9a55;
  font-weight: 700;
  cursor: pointer;
}
.save span {
  font-size: 18px;
}
.message {
  margin: 15px 0 0;
  text-align: right;
  color: #6b9a55;
  font-size: 12px;
  font-weight: 700;
}
@media (max-width: 750px) {
  .content {
    padding: 0;
  }
  .list-heading,
  .student-row {
    grid-template-columns: 1fr;
  }
  .list-heading span:last-child {
    display: none;
  }
  .state-buttons {
    margin-top: 10px;
    flex-wrap: wrap;
  }
}
@media (max-width: 550px) {
  .attendance-page {
    padding: 25px 16px;
  }
  .content {
    padding: 0;
  }
  .card-heading {
    display: block;
  }
  .status {
    display: inline-block;
    margin-top: 15px;
  }
  .filters {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
  .summary {
    flex-wrap: wrap;
  }
  .save {
    width: 100%;
  }
}

.attendance-card {
  border: 2px solid #231f2c;
  border-radius: 24px;
}

button,
input,
select {
  font: inherit;
}

button:focus-visible,
input:focus-visible,
select:focus-visible {
  outline: 3px solid rgb(107 154 85 / 35%);
  outline-offset: 2px;
}
</style>
