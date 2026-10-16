<template>
  <div class="profile-page">
    <main class="content">
      <header class="topbar">
        <div>
          <span class="eyebrow">Configuración de cuenta</span>
          <h1>Mi perfil</h1>
          <p>Administrá tu información personal y tus datos como docente.</p>
        </div>
        <button class="bell" type="button" aria-label="Notificaciones">
          ♧<b>3</b>
        </button>
      </header>

      <section class="profile-card">
        <div class="profile-heading">
          <div>
            <span class="section-label">Información personal</span>
            <h2>Datos del docente</h2>
          </div>
          <span class="status">Perfil activo</span>
        </div>
        <div class="profile-layout">
          <div class="photo-column">
            <div class="photo-frame">
              <img
                v-if="photoUrl"
                :src="photoUrl"
                :alt="`Foto de perfil de ${form.name}`"
              /><span v-else class="photo-placeholder">{{ initials }}</span>
            </div>
            <label class="upload-button" for="photo-upload"
              >＋ Cambiar foto</label
            >
            <input
              id="photo-upload"
              class="hidden-input"
              type="file"
              accept="image/png,image/jpeg,image/webp"
              @change="handlePhotoChange"
            />
            <small class="hint">JPG, PNG o WEBP · máximo 2 MB</small>
          </div>
          <form class="form" @submit.prevent="saveProfile">
            <div class="field">
              <label for="name">Nombre completo</label
              ><input id="name" v-model="form.name" type="text" />
            </div>
            <div class="field">
              <label for="email">Correo electrónico</label
              ><input id="email" v-model="form.email" type="email" />
            </div>
            <div class="field">
              <label for="phone">Teléfono</label
              ><input id="phone" v-model="form.phone" type="tel" />
            </div>
            <div class="field">
              <label for="role">Rol</label
              ><input id="role" v-model="form.role" type="text" disabled />
            </div>
            <div class="field full">
              <label for="school">Institución</label
              ><input id="school" v-model="form.school" type="text" />
            </div>
            <div class="field full">
              <label for="address">Ubicación</label
              ><input id="address" v-model="form.address" type="text" />
            </div>
            <div class="actions">
              <span v-if="message" class="message">{{ message }}</span
              ><button class="save" type="submit">
                Guardar cambios <span>→</span>
              </button>
            </div>
          </form>
        </div>
      </section>

      <section class="courses-card">
        <div class="profile-heading">
          <div>
            <span class="section-label">Información académica</span>
            <h2>Cursos a cargo</h2>
          </div>
          <button class="add" type="button">＋ Agregar curso</button>
        </div>
        <div class="course-list">
          <span class="course green-bg">4° A <small>Matemática</small></span
          ><span class="course purple-bg"
            >5° B <small>Programación y robótica</small></span
          ><span class="course yellow-bg"
            >6° A <small>Proyecto final</small></span
          >
        </div>
      </section>
      <footer class="landscape"><div></div></footer>
    </main>
  </div>
</template>

<script setup>
import { computed, onBeforeUnmount, reactive, ref, watch } from "vue";

const props = defineProps({
  session: {
    type: Object,
    default: () => ({}),
  },
});
const emit = defineEmits(["save-profile"]);

const photoUrl = ref("");
const message = ref("");
const form = reactive({
  name: props.session.name || "Juan Perez",
  email: props.session.email || "j.perez@escuelasproa.edu.ar",
  phone: "03548 123456",
  role: props.session.role || "Docente",
  school: "Escuela PROA",
  address: "La Falda, Córdoba",
});
const initials = computed(() =>
  form.name
    .split(" ")
    .filter(Boolean)
    .map((word) => word[0])
    .join("")
    .slice(0, 2)
    .toUpperCase(),
);

watch(
  () => props.session,
  (session) => {
    Object.assign(form, {
      name: session.name || form.name,
      email: session.email || form.email,
      role: session.role || form.role,
    });
  },
);

function handlePhotoChange(event) {
  const file = event.target.files?.[0];
  if (!file) return;
  if (file.size > 2 * 1024 * 1024) {
    message.value = "La imagen no puede superar los 2 MB.";
    return;
  }
  if (photoUrl.value) URL.revokeObjectURL(photoUrl.value);
  photoUrl.value = URL.createObjectURL(file);
  message.value = "Foto lista para guardar.";
}

function saveProfile() {
  emit("save-profile", { ...form });
  message.value = "Cambios guardados correctamente.";
}

onBeforeUnmount(() => {
  if (photoUrl.value) URL.revokeObjectURL(photoUrl.value);
});
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
.profile-page {
  min-height: 100vh;
  margin: calc(-1 * clamp(1rem, 3vw, 2rem));
  padding: 38px clamp(20px, 5vw, 64px);
  background: #faf5ec;
}
.sidebar {
  width: 266px;
  min-height: 100vh;
  padding: 24px 18px;
  border-right: 2px solid #231f2c;
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
.mini-profile small {
  display: block;
  margin-top: 3px;
  color: #6b6577;
  font-size: 11px;
}
.logo {
  position: relative;
  width: 43px;
  height: 43px;
  border: 2px solid;
  border-radius: 12px;
  overflow: hidden;
  background: #fff;
}
.person {
  position: absolute;
  bottom: 8px;
  width: 14px;
  height: 14px;
  border-radius: 50%;
}
.person:after {
  content: "";
  position: absolute;
  left: 50%;
  bottom: -10px;
  width: 21px;
  height: 12px;
  transform: translateX(-50%);
  border-radius: 12px 12px 0 0;
}
.person.green {
  left: 7px;
  background: #6b9a55;
}
.person.green:after {
  background: #6b9a55;
}
.person.yellow {
  right: 7px;
  background: #e5b54c;
}
.person.yellow:after {
  background: #e5b54c;
}
.menu {
  display: grid;
  gap: 5px;
  margin-top: 42px;
}
.menu a {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 11px 12px;
  border-radius: 10px;
  color: #6b6577;
  text-decoration: none;
  font-size: 13px;
  font-weight: 600;
}
.menu a:first-child {
  margin-bottom: 5px;
}
.menu a:hover,
.menu a.active {
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
  color: #6b6577;
}
.mini-profile {
  display: flex;
  align-items: center;
  gap: 9px;
  margin-top: 16px;
  font-size: 12px;
}
.mini-profile > span:last-child {
  margin-left: auto;
  font-size: 20px;
}
.avatar {
  display: grid;
  place-items: center;
  width: 35px;
  height: 35px;
  border: 1.5px solid;
  border-radius: 50%;
  background: #dceef9;
  font-size: 11px;
  font-weight: bold;
}
.content {
  width: min(100%, 1160px);
  margin: 0 auto;
}
.topbar {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
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
.profile-card,
.courses-card {
  margin-top: 30px;
  padding: 25px;
  border: 1.5px solid;
  border-radius: 18px;
  background: #fffdf9;
}
.courses-card {
  margin-top: 20px;
}
.profile-heading {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}
.profile-heading h2 {
  margin: 6px 0 0;
  font-size: 21px;
  font-style: italic;
}
.status {
  padding: 6px 10px;
  border-radius: 99px;
  background: #e2eeda;
  color: #46733c;
  font-size: 11px;
  font-weight: 700;
}
.profile-layout {
  display: grid;
  grid-template-columns: 180px 1fr;
  gap: 35px;
  margin-top: 27px;
}
.photo-column {
  text-align: center;
}
.photo-frame {
  display: grid;
  place-items: center;
  width: 150px;
  height: 150px;
  margin: auto;
  border: 2px solid;
  border-radius: 50%;
  overflow: hidden;
  background: #dceef9;
}
.photo-frame img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
.photo-placeholder {
  font-size: 42px;
  font-weight: bold;
}
.upload-button {
  display: inline-block;
  margin-top: 17px;
  padding: 10px 14px;
  border: 1.5px solid;
  border-radius: 10px;
  background: #f4eefb;
  color: #6c4b9c;
  font-size: 12px;
  font-weight: 700;
  cursor: pointer;
}
.upload-button:hover {
  background: #e7ddf6;
}
.hidden-input {
  display: none;
}
.hint {
  display: block;
  margin-top: 9px;
  color: #6b6577;
  font-size: 10px;
}
.form {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 18px;
}
.field {
  display: flex;
  flex-direction: column;
  gap: 7px;
}
.field.full {
  grid-column: 1/-1;
}
.field label {
  font-size: 12px;
  font-weight: 700;
}
.field input {
  height: 43px;
  padding: 0 13px;
  border: 1.5px solid;
  border-radius: 10px;
  background: #fffdf9;
  color: #231f2c;
  font: inherit;
}
.field input:focus {
  outline: 0;
  border-color: #6b9a55;
  box-shadow: 0 0 0 3px rgb(107 154 85 / 12%);
}
.field input:disabled {
  background: #f4f0e8;
  color: #6b6577;
}
.actions {
  grid-column: 1/-1;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  gap: 15px;
  margin-top: 5px;
}
.message {
  color: #6c4b9c;
  font-size: 12px;
  font-weight: 700;
}
.save,
.add {
  padding: 12px 17px;
  border: 1.5px solid;
  border-radius: 10px;
  background: #4e9cd4;
  color: #231f2c;
  font-weight: 700;
  cursor: pointer;
}
.save span {
  font-size: 18px;
}
.add {
  padding: 8px 12px;
  background: #faebc7;
  font-size: 11px;
}
.course-list {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-top: 20px;
}
.course {
  padding: 12px 15px;
  border: 1.5px solid;
  border-radius: 11px;
  font-weight: 700;
  font-size: 13px;
}
.course small {
  display: block;
  margin-top: 5px;
  color: #6b6577;
  font-size: 11px;
  font-weight: normal;
}
.green-bg {
  background: #e2eeda;
}
.purple-bg {
  background: #f4eefb;
}
.yellow-bg {
  background: #faebc7;
}
.landscape {
  height: 90px;
  position: relative;
  overflow: hidden;
  margin: 28px 0 0;
  background: #e7ddf6;
  border-top: 2px solid;
  border-radius: 50% 50% 0 0;
}
@media (max-width: 850px) {
  .profile-layout {
    grid-template-columns: 1fr;
  }
  .photo-column {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .landscape {
    margin-left: 0;
    margin-right: 0;
  }
}
@media (max-width: 600px) {
  .profile-page {
    padding: 26px 16px 0;
  }
  .content {
    padding: 0;
  }
  .form {
    grid-template-columns: 1fr;
  }
  .field.full {
    grid-column: auto;
  }
  .actions {
    grid-column: auto;
    display: block;
  }
  .message {
    display: block;
    margin-bottom: 12px;
  }
  .save {
    width: 100%;
  }
  .profile-card,
  .courses-card {
    padding: 20px;
  }
  .landscape {
    margin-left: 0;
    margin-right: 0;
  }
}
</style>
