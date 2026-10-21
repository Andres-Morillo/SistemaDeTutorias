<template>
  <section class="citation-page" aria-labelledby="citation-title">
    <div class="citation-shell">
      <header class="page-heading">
        <div>
          <p class="eyebrow">ACOMPAÑAMIENTO ESCOLAR</p>
          <h1 id="citation-title">Nueva citación</h1>
          <p class="subtitle">Programá un encuentro con los estudiantes seleccionados.</p>
        </div>
        <span class="heading-mark" aria-hidden="true">＋</span>
      </header>

      <form class="citation-form" @submit.prevent="submitCitation">
        <section class="form-section summary-section" aria-labelledby="summary-title">
          <h2 id="summary-title">Datos del curso</h2>
          <table class="summary-table">
            <tbody>
              <tr v-for="item in summary" :key="item.label">
                <th scope="row">{{ item.label }}</th>
                <td>{{ item.value }}</td>
              </tr>
            </tbody>
          </table>
        </section>

        <section class="form-section" aria-labelledby="schedule-title">
          <h2 id="schedule-title">Fecha y lugar</h2>
          <div class="fields-grid">
            <div class="field">
              <label for="appointment-date">Día</label>
              <input
                id="appointment-date"
                v-model="form.date"
                type="date"
                required
              />
              <p v-if="formattedDate" class="date-preview" aria-live="polite">
                {{ formattedDate }}
              </p>
            </div>

            <div class="field">
              <label for="appointment-time">Horario</label>
              <input
                id="appointment-time"
                v-model="form.time"
                type="time"
                required
              />
            </div>

            <div class="field field--full">
              <label for="appointment-place">Lugar</label>
              <select id="appointment-place" v-model="form.place" required>
                <option disabled value="">Seleccioná un lugar</option>
                <option v-for="place in places" :key="place" :value="place">
                  {{ place }}
                </option>
              </select>
            </div>
          </div>
        </section>

        <div class="section-divider" role="separator"></div>

        <section class="form-section details-section" aria-labelledby="details-title">
          <h2 id="details-title">Datos de la citación</h2>
          <div class="fields-grid">
            <div class="field field--full">
              <label for="reason">Motivo de citación</label>
              <input id="reason" v-model="form.reason" type="text" required />
            </div>

            <div class="field field--full">
              <label for="content">Contenido y trabajos</label>
              <textarea id="content" v-model="form.content" rows="3" required></textarea>
            </div>

            <div class="field field--full">
              <label for="observations">Observaciones</label>
              <textarea id="observations" v-model="form.observations" rows="3"></textarea>
            </div>

            <fieldset class="field field--full notification-field">
              <legend>Notificación</legend>
              <div class="notification-options">
                <label
                  v-for="option in notificationOptions"
                  :key="option.value"
                  class="notification-option"
                  :class="{ 'is-selected': form.notification === option.value }"
                >
                  <input
                    v-model="form.notification"
                    type="radio"
                    name="notification"
                    :value="option.value"
                    required
                  />
                  <span>{{ option.label }}</span>
                </label>
              </div>
            </fieldset>
          </div>
        </section>

        <footer class="form-actions">
          <button
            class="action-button action-button--submit"
            type="submit"
          >
            Guardar citación
          </button>
          <button
            class="action-button action-button--draft"
            type="button"
            @click="emit('guardar-borrador', { ...form })"
          >
            Guardar borrador
          </button>
          <button
            class="action-button action-button--void"
            type="button"
            @click="emit('anular-citacion', { ...form })"
          >
            Anular citación
          </button>
          <button
            class="action-button action-button--cancel"
            type="button"
            @click="emit('cancelar')"
          >
            Cancelar
          </button>
        </footer>
      </form>
    </div>
  </section>
</template>

<script setup>
import { computed, reactive } from 'vue'

const emit = defineEmits(['guardar-borrador', 'guardar-citacion', 'anular-citacion', 'cancelar'])

const summary = [
  { label: 'Curso', value: '4°' },
  { label: 'Materia', value: 'Programación' },
  { label: 'Docente', value: 'Juan Pérez' },
  { label: 'Estud. Seleccionados', value: 'María Gómez, Lucas Díaz' }
]

const places = [
  'Aula 1',
  'Aula 2',
  'Aula 3',
  'Aula 4',
  'Aula 5',
  'Aula 6',
  'Laboratorio',
  'Taller de arte'
]

const notificationOptions = [
  { label: 'Email', value: 'email' },
  { label: 'WhatsApp', value: 'whatsapp' },
  { label: 'Ambos', value: 'ambos' }
]

const form = reactive({
  date: '',
  time: '',
  place: '',
  reason: 'Recuperación de contenido',
  content: 'Programación y HTML',
  observations: '3 + P y carpeta completa',
  notification: ''
})

const formattedDate = computed(() => {
  if (!form.date) return ''

  const [year, month, day] = form.date.split('-').map(Number)
  const date = new Date(year, month - 1, day)
  const label = new Intl.DateTimeFormat('es-AR', {
    weekday: 'long',
    day: 'numeric',
    month: 'long'
  }).format(date)

  return label.charAt(0).toLocaleUpperCase('es-AR') + label.slice(1)
})

function submitCitation() {
  emit('guardar-citacion', { ...form })
}
</script>

<style scoped>
.citation-page {
  min-height: 100vh;
  padding: clamp(24px, 5vw, 56px) 20px;
  background: #faf5ec;
  color: #231f2c;
  font-family: Arial, Helvetica, sans-serif;
}

.citation-shell {
  width: min(100%, 900px);
  margin: 0 auto;
}

.page-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
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

.subtitle {
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
  font-size: 24px;
  line-height: 1;
}

.citation-form {
  padding-top: 8px;
}

.form-section {
  padding: 24px 0;
}

.form-section h2 {
  margin: 0 0 16px;
  font-size: 18px;
  font-weight: 700;
}

.summary-table {
  width: 100%;
  border-collapse: collapse;
  text-align: left;
}

.summary-table tr + tr {
  border-top: 1px solid rgb(35 31 44 / 8%);
}

.summary-table th,
.summary-table td {
  padding: 10px 8px;
  vertical-align: top;
  font-size: 14px;
  line-height: 1.45;
}

.summary-table th {
  width: 190px;
  color: #6b6577;
  font-weight: 600;
}

.summary-table td {
  font-weight: 600;
}

.fields-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 18px 22px;
}

.field {
  min-width: 0;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.field--full {
  grid-column: 1 / -1;
}

.field > label,
.notification-field legend {
  color: #393441;
  font-size: 13px;
  font-weight: 700;
}

.field input,
.field select,
.field textarea {
  width: 100%;
  min-height: 44px;
  padding: 10px 12px;
  border: 1px solid #c9c2b7;
  border-radius: 8px;
  outline: none;
  background: #fffdf9;
  color: #231f2c;
  font: inherit;
  font-size: 14px;
  transition: border-color 160ms ease, box-shadow 160ms ease;
}

.field textarea {
  min-height: 84px;
  resize: vertical;
}

.field input:focus,
.field select:focus,
.field textarea:focus {
  border-color: #6b9a55;
  box-shadow: 0 0 0 3px rgb(107 154 85 / 12%);
}

.date-preview {
  margin: 0;
  color: #6b6577;
  font-size: 13px;
}

.section-divider {
  height: 1px;
  background: rgb(35 31 44 / 18%);
}

.details-section {
  padding-bottom: 28px;
}

.notification-field {
  min-width: 0;
  margin: 0;
  padding: 0;
  border: 0;
}

.notification-field legend {
  margin-bottom: 10px;
  padding: 0;
}

.notification-options {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.notification-option {
  min-height: 42px;
  display: inline-flex;
  align-items: center;
  gap: 9px;
  padding: 0 14px;
  border: 1px solid #c9c2b7;
  border-radius: 8px;
  background: #fffdf9;
  color: #393441;
  font-size: 14px;
  cursor: pointer;
  transition: border-color 160ms ease, background 160ms ease;
}

.notification-option.is-selected {
  border-color: #6b9a55;
  background: #eaf1e5;
}

.notification-option:focus-within {
  outline: 3px solid rgb(107 154 85 / 18%);
}

.notification-option input {
  width: 16px;
  height: 16px;
  margin: 0;
  accent-color: #6b9a55;
}

.form-actions {
  display: flex;
  justify-content: flex-end;
  flex-wrap: wrap;
  gap: 12px;
  padding-top: 20px;
  border-top: 1px solid rgb(35 31 44 / 14%);
}

.action-button {
  min-height: 46px;
  padding: 0 18px;
  border: 1.5px solid #231f2c;
  border-radius: 9px;
  color: #231f2c;
  font: inherit;
  font-size: 14px;
  font-weight: 700;
  cursor: pointer;
  transition: transform 140ms ease, filter 140ms ease;
}

.action-button:hover {
  filter: brightness(1.04);
  transform: translateY(-1px);
}

.action-button:active {
  transform: translateY(0);
}

.action-button--draft {
  background: #f0cf78;
}

.action-button--submit {
  border-color: #46733c;
  background: #e2eeda;
  color: #36542f;
}

.action-button--void {
  background: #8ec7e8;
}

.action-button--cancel {
  background: #e88a82;
}

@media (max-width: 560px) {
  .citation-page {
    padding-right: 16px;
    padding-left: 16px;
  }

  .page-heading h1 {
    font-size: 28px;
  }

  .fields-grid {
    grid-template-columns: 1fr;
  }

  .field--full {
    grid-column: auto;
  }

  .summary-table th {
    width: 42%;
  }

  .summary-table th,
  .summary-table td {
    padding-right: 5px;
    padding-left: 5px;
  }

  .form-actions {
    display: grid;
    grid-template-columns: 1fr;
  }
}
</style>
