<template>
  <div class="students-page">
    <main class="content">
      <header class="topbar">
        <div>
          <span class="eyebrow">Gestión institucional</span>
          <h1>Estudiantes</h1>
          <p>Consultá y administrá la información de los estudiantes.</p>
        </div>
        <div class="account">
          <button class="bell">♧</button><span class="avatar">AG</span
          ><span><b>Ana Gomez</b><small>Coordinadora</small></span>
        </div>
      </header>
      <section class="students-card">
        <div class="card-head">
          <div>
            <span class="label">Listado general</span>
            <h2>Estudiantes registrados</h2>
          </div>
          <button class="view-all" type="button" @click="reset">
            Ver todos <span>→</span>
          </button>
        </div>
        <div class="tools">
          <label class="search"
            >⌕<input
              v-model="search"
              type="search"
              placeholder="Buscar estudiante" /></label
          ><select v-model="course">
            <option value="Todos">Todos los cursos</option>
            <option>1</option>
            <option>2</option>
            <option>3</option>
            <option>4</option>
            <option>5</option>
            <option>6</option></select
          ><button class="filter" type="button" @click="reset">
            Limpiar filtros
          </button>
        </div>
        <div class="list-head">
          <span>Estudiante</span><span>Curso</span><span>Estado</span
          ><span></span>
        </div>
        <div
          v-for="student in filteredStudents"
          :key="student.id"
          class="student-row"
        >
          <span class="student"
            ><span class="avatar student-avatar">{{
              initials(student.name)
            }}</span
            ><b>{{ student.name }}</b></span
          ><span class="course">{{ student.course }}</span
          ><span class="status" :class="student.statusClass">{{
            student.status
          }}</span
          ><button
            class="more"
            type="button"
            @click="message = `Viendo el perfil de ${student.name}`"
          >
            →
          </button>
        </div>
        <p v-if="message" class="message">{{ message }}</p>
      </section>
    </main>
  </div>
</template>

<script setup>
import { computed, ref } from "vue";
const search = ref("");
const course = ref("Todos");
const message = ref("");
const students = [
  {
    id: 1,
    name: "Juan Perez",
    course: "2°",
    status: "Activo",
    statusClass: "active-status",
  },
  {
    id: 2,
    name: "Maria Gomez",
    course: "3°",
    status: "Activo",
    statusClass: "active-status",
  },
  {
    id: 3,
    name: "Lautaro Diaz",
    course: "4°",
    status: "En seguimiento",
    statusClass: "follow-status",
  },
  {
    id: 4,
    name: "Camila Ruiz",
    course: "5°",
    status: "Activo",
    statusClass: "active-status",
  },
];
const filteredStudents = computed(() =>
  students.filter(
    (student) =>
      (course.value === "Todos" || student.course.startsWith(course.value)) &&
      student.name.toLowerCase().includes(search.value.toLowerCase()),
  ),
);
const initials = (name) =>
  name
    .split(" ")
    .map((word) => word[0])
    .join("")
    .slice(0, 2);
function reset() {
  search.value = "";
  course.value = "Todos";
}
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
.students-page {
  min-height: 100vh;
  margin: calc(-1 * clamp(1rem, 3vw, 2rem));
  padding: 38px clamp(20px, 5vw, 64px);
  background: #faf5ec;
}
.sidebar {
  width: 265px;
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
  font-size: 19px;
}
.brand small,
.account small {
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
.logout {
  margin-top: auto;
  border: 0;
  background: transparent;
  text-align: left;
  color: #6b6577;
  font-weight: 600;
  padding: 12px;
}
.content {
  width: min(100%, 1100px);
  margin: 0 auto;
}
.topbar {
  display: flex;
  justify-content: space-between;
}
.eyebrow,
.label {
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
.account {
  display: flex;
  align-items: center;
  gap: 9px;
  font-size: 12px;
}
.account b {
  display: block;
}
.bell {
  width: 39px;
  height: 39px;
  border: 1.5px solid;
  border-radius: 50%;
  background: #fffdf9;
  font-size: 19px;
}
.avatar {
  display: grid;
  place-items: center;
  width: 36px;
  height: 36px;
  border: 1.5px solid;
  border-radius: 50%;
  background: #f4eefb;
  font-weight: bold;
}
.students-card {
  max-width: 900px;
  margin: 35px auto 0;
  padding: 27px;
  border: 2px solid #231f2c;
  border-radius: 24px;
  background: #fffdf9;
}
.card-head {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}
.card-head h2 {
  margin: 6px 0;
  font-size: 21px;
  font-style: italic;
}
.view-all {
  border: 0;
  background: transparent;
  color: #46733c;
  font-weight: 700;
  cursor: pointer;
}
.view-all span {
  font-size: 18px;
}
.tools {
  display: flex;
  gap: 12px;
  align-items: center;
  margin-top: 24px;
  padding: 14px;
  background: #faf5ec;
  border-radius: 12px;
}
.search {
  display: flex;
  align-items: center;
  gap: 8px;
  flex: 1;
  height: 40px;
  padding: 0 11px;
  border: 1.5px solid;
  border-radius: 9px;
  background: #fffdf9;
  color: #6b6577;
}
.search input {
  width: 100%;
  border: 0;
  outline: 0;
  background: transparent;
  font: inherit;
}
.tools select {
  height: 40px;
  padding: 0 11px;
  border: 1.5px solid;
  border-radius: 9px;
  background: #fffdf9;
  color: #231f2c;
}
.filter {
  border: 0;
  background: transparent;
  color: #46733c;
  font-weight: 700;
  cursor: pointer;
}
.list-head,
.student-row {
  display: grid;
  grid-template-columns: 1.5fr 0.7fr 1fr 35px;
  align-items: center;
  gap: 12px;
}
.list-head {
  padding: 22px 8px 11px;
  color: #6b6577;
  font-size: 11px;
  font-weight: 700;
  border-bottom: 1.5px solid;
}
.student-row {
  padding: 14px 8px;
  border-bottom: 1px solid #e7e0d3;
  font-size: 12px;
}
.student {
  display: flex;
  align-items: center;
  gap: 10px;
}
.student-avatar {
  width: 31px;
  height: 31px;
  font-size: 10px;
}
.course {
  font-weight: 700;
}
.status {
  justify-self: start;
  padding: 6px 9px;
  border-radius: 99px;
  font-size: 10px;
  font-weight: 700;
}
.active-status {
  background: #e2eeda;
  color: #46733c;
}
.follow-status {
  background: #faebc7;
  color: #86651d;
}
.more {
  width: 28px;
  height: 28px;
  border: 1px solid;
  border-radius: 7px;
  background: #fffdf9;
  cursor: pointer;
}
.message {
  margin: 18px 0 0;
  color: #6c4b9c;
  font-size: 12px;
  font-weight: 700;
}
@media (max-width: 750px) {
  .content {
    padding: 0;
  }
  .tools {
    flex-wrap: wrap;
  }
  .search {
    min-width: 200px;
  }
  .list-head,
  .student-row {
    grid-template-columns: 1fr 0.6fr 1fr 30px;
  }
}
@media (max-width: 550px) {
  .students-page {
    padding: 25px 16px;
  }
  .content {
    padding: 0;
  }
  .topbar {
    display: block;
  }
  .account {
    margin-top: 16px;
  }
  .card-head {
    display: block;
  }
  .view-all {
    margin-top: 12px;
  }
  .tools {
    display: block;
  }
  .search,
  .tools select {
    width: 100%;
    margin-bottom: 10px;
  }
  .filter {
    padding: 0;
  }
  .list-head {
    display: none;
  }
  .student-row {
    grid-template-columns: 1fr 1fr;
    gap: 9px;
    padding: 15px 4px;
  }
  .student {
    grid-column: 1/-1;
  }
  .status {
    justify-self: start;
  }
  .more {
    grid-column: 2;
    grid-row: 2;
    justify-self: end;
  }
}

button:focus-visible,
input:focus-visible,
select:focus-visible {
  outline: 3px solid rgb(107 154 85 / 35%);
  outline-offset: 2px;
}
</style>
