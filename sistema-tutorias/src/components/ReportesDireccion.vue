<template>
  <div class="reports-page">
    <main class="content">
      <header class="topbar">
        <div>
          <span class="eyebrow">Análisis institucional</span>
          <h1>Reportes</h1>
          <p>Seleccioná el informe que querés consultar.</p>
        </div>
        <div class="account">
          <button class="bell">♧</button><span class="avatar">AG</span
          ><span><b>Ana Gomez</b><small>Directora</small></span>
        </div>
      </header>
      <section class="report-card">
        <div class="section-title">
          <div>
            <span class="label">Informes disponibles</span>
            <h2>¿Qué reporte necesitás?</h2>
          </div>
          <span class="report-icon">▤</span>
        </div>
        <div class="report-list">
          <button
            v-for="report in reports"
            :key="report.id"
            :class="[
              'report-row',
              report.color,
              { selected: selected === report.id },
            ]"
            type="button"
            @click="selected = report.id"
          >
            <span class="row-icon">{{ report.icon }}</span
            ><span
              ><strong>{{ report.title }}</strong
              ><small>{{ report.description }}</small></span
            ><span class="radio">{{ selected === report.id ? "✓" : "○" }}</span>
          </button>
        </div>
        <button class="generate" type="button" @click="generate">
          Generar reporte <span>→</span>
        </button>
        <p v-if="message" class="message">{{ message }}</p>
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref } from "vue";
const selected = ref("attendance");
const message = ref("");
const reports = [
  {
    id: "attendance",
    title: "Asistencia general",
    description: "Consultá la asistencia de estudiantes y cursos",
    icon: "⌂",
    color: "green",
  },
  {
    id: "sessions",
    title: "Tutorías realizadas",
    description: "Revisá las tutorías completadas",
    icon: "▣",
    color: "purple",
  },
  {
    id: "observations",
    title: "Observaciones",
    description: "Analizá las observaciones registradas",
    icon: "▤",
    color: "yellow",
  },
  {
    id: "academic",
    title: "Rendimiento académico",
    description: "Visualizá el progreso académico",
    icon: "↗",
    color: "blue",
  },
  {
    id: "platform",
    title: "Uso de la plataforma",
    description: "Conocé cómo se utiliza el sistema",
    icon: "◌",
    color: "pink",
  },
];
function generate() {
  message.value = "Reporte seleccionado listo para generar.";
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
.reports-page {
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
  width: min(100%, 950px);
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
.report-card {
  max-width: 760px;
  margin: 35px auto 0;
  padding: 28px;
  border: 2px solid #231f2c;
  border-radius: 24px;
  background: #fffdf9;
}
.section-title {
  display: flex;
  justify-content: space-between;
}
.section-title h2 {
  margin: 6px 0 0;
  font-size: 21px;
  font-style: italic;
}
.report-icon {
  font-size: 26px;
}
.report-list {
  display: grid;
  gap: 12px;
  margin-top: 25px;
}
.report-row {
  position: relative;
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 15px 17px;
  border: 1.5px solid;
  border-radius: 13px;
  text-align: left;
  color: #231f2c;
  cursor: pointer;
}
.report-row.green {
  background: #e2eeda;
}
.report-row.purple {
  background: #f4eefb;
}
.report-row.yellow {
  background: #faebc7;
}
.report-row.blue {
  background: #dceef9;
}
.report-row.pink {
  background: #f4dce8;
}
.report-row.selected {
  box-shadow: 0 0 0 3px rgb(107 154 85 / 18%);
  transform: translateX(3px);
}
.row-icon {
  display: grid;
  place-items: center;
  width: 38px;
  height: 38px;
  border: 1.5px solid;
  border-radius: 50%;
  font-size: 18px;
}
.report-row strong {
  display: block;
  font-size: 13px;
}
.report-row small {
  display: block;
  margin-top: 5px;
  color: #6b6577;
  font-size: 11px;
}
.radio {
  position: absolute;
  right: 15px;
  font-size: 18px;
}
.generate {
  display: block;
  margin: 27px auto 0;
  padding: 13px 20px;
  border: 1.5px solid;
  border-radius: 10px;
  background: #6b9a55;
  font-weight: 700;
  cursor: pointer;
}
.generate span {
  font-size: 18px;
}
.message {
  margin: 15px 0 0;
  text-align: center;
  color: #6c4b9c;
  font-size: 12px;
  font-weight: 700;
}
@media (max-width: 700px) {
  .content {
    padding: 0;
  }
  .account {
    margin-left: 15px;
  }
  .report-card {
    margin-top: 25px;
  }
}
@media (max-width: 550px) {
  .reports-page {
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
  .report-card {
    padding: 20px;
  }
  .report-row {
    padding-right: 35px;
  }
}

button:focus-visible {
  outline: 3px solid rgb(107 154 85 / 35%);
  outline-offset: 2px;
}
</style>
