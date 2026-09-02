<template>
  <div class="page">
    <header class="container">
      <div class="brand">
        <svg class="mark" width="56" height="56" viewBox="0 0 56 56" fill="none">
          <rect x="1" y="1" width="54" height="54" rx="0" fill="#FFFDF9" stroke="#231F2C" stroke-width="2" />
          <circle cx="20" cy="24" r="9" fill="#6B9A55" />
          <circle cx="38" cy="22" r="8" fill="#E5B54C" />
          <path d="M8 48c0-9 6-15 14-15s14 6 14 15" fill="#6B9A55" />
          <path d="M32 48c0-8 5-13 12-13s12 5 12 13" fill="#E5B54C" />
          <path d="M42 10l1 3 3 1-3 1-1 3-1-3-3-1 3-1z" fill="#E5B54C" />
        </svg>
        <div>
          <p class="name">Tuto Script</p>
          <p class="tag">Gestión de tutorías escolares</p>
        </div>
      </div>
    </header>

    <main class="container hero">
      <section>
        <span class="eyebrow">Hecho para tutores y equipos de acompañamiento</span>

        <h1>Organizá,<br />acompañá,<br />transformá.</h1>

        <ul class="features">
          <li v-for="feature in features" :key="feature.label">
            <span class="icon" :style="{ background: feature.bg }" v-html="feature.icon"></span>
            {{ feature.label }}
          </li>
        </ul>
      </section>

      <form class="login" novalidate @submit="handleSubmit">
        <h2>Iniciar sesión</h2>
        <p class="sub">Ingresá tus datos para continuar</p>

        <label for="email">Correo o usuario</label>
        <input id="email" v-model="form.email" type="text" autocomplete="username" placeholder="nombre@escuela.edu.ar" required />

        <label for="password">Contraseña</label>
        <div class="password-field">
          <input
            id="password"
            v-model="form.password"
            :type="showPassword ? 'text' : 'password'"
            autocomplete="current-password"
            placeholder="Tu contraseña"
            required
          />
          <button type="button" @click="showPassword = !showPassword">{{ showPassword ? 'Ocultar' : 'Mostrar' }}</button>
        </div>
        <a href="#" class="forgot" @click.prevent>¿Olvidaste tu contraseña?</a>

        <label class="remember">
          <input v-model="form.remember" type="checkbox" />
          Recordar usuario
        </label>

        <button type="submit" class="submit">Iniciar sesión →</button>
        <p v-if="statusMessage" class="msg">{{ statusMessage }}</p>

        <hr />
        <p class="signup">¿No tenés cuenta? <a href="#" @click.prevent>Registrarse</a></p>
      </form>
    </main>

    <svg class="landscape" viewBox="0 0 1280 220" preserveAspectRatio="none" aria-hidden="true">
      <path d="M0 172 Q 210 32 430 152 T 760 142 Q 950 97 1130 167 T 1280 157 V220 H0 Z" fill="#E7DDF6" />
      <path d="M0 170 Q 210 30 430 150 T 760 140 Q 950 95 1130 165 T 1280 155" fill="none" stroke="#231F2C" stroke-width="2.5" />

    </svg>

    <footer id="contacto" class="container">
      <span>03548 123456</span>
      <span>contacto@tutoscript.edu.ar</span>
      <span>La Falda, Córdoba</span>
    </footer>
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'
const showPassword = ref(false)
const statusMessage = ref('')
const form = reactive({
  email: '',
  password: '',
  remember: false,
})
const features = [
  { label: 'Gestión de tutorías', bg: '#F4EEFB', icon: '<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#231F2C" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="6" y="4" width="12" height="17" rx="2"/><path d="M9 4V3a1 1 0 0 1 1-1h4a1 1 0 0 1 1 1v1"/><path d="M9 12l2 2 4-4"/></svg>' },
  { label: 'Notificaciones automáticas', bg: '#FAEBC7', icon: '<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#231F2C" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M18 8a6 6 0 0 0-12 0c0 7-3 9-3 9h18s-3-2-3-9"/><path d="M13.7 21a2 2 0 0 1-3.4 0"/></svg>' },
  { label: 'Seguimiento académico', bg: '#DCEEF9', icon: '<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#231F2C" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 3v18h18"/><path d="M7 15l4-5 3 3 5-7"/></svg>' },
  { label: 'Reportes personalizados', bg: '#E2EEDA', icon: '<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#231F2C" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M14 3H7a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V8z"/><path d="M14 3v5h5"/><path d="M9.5 14.5h5M9.5 17.5h3.5"/></svg>' },
]
function handleSubmit(event) {
  event.preventDefault()
  statusMessage.value = 'Esto es una vista de demostración — no hay backend conectado todavía.'
}
</script>

<style>
html, body, #app {
  margin: 0;
  width: 100%;
  height: 100%;}
</style>

<style scoped>
* {
  box-sizing: border-box;
}
.page {
  width: 100%;
  min-height: 100vh;
  background: #faf5ec;
  color: #231f2c;
  font-family: sans-serif;
}
.container {
  /*max-width: 1200px;*/
  margin: 0 auto;
  padding: 0 32px;
}
/* ---------- header ---------- */
header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 16px;
  padding-top: 32px;
}
.brand {
  display: flex;
  align-items: center;
  gap: 16px;
}
.name {
  margin: 0;
  font-size: 26px;
  font-weight: 700;
}
.tag {
  margin: 2px 0 0;
  font-size: 14px;
  color: #6b6577;
}
nav {
  display: flex;
  gap: 24px;
  font-size: 14px;
  font-weight: 600;
}
nav a {
  color: #6b6577;
  text-decoration: none;
}
/* ---------- hero ---------- */
.hero {
  display: grid;
  grid-template-columns: 1.1fr 0.9fr;
  gap: 48px;
  padding-top: 56px;
}
@media (max-width: 860px) {
  .hero {
    grid-template-columns: 1fr;
  }
}
.eyebrow {
  display: inline-block;
  background: #f4eefb;
  color: #6c4b9c;
  font-size: 13px;
  font-weight: 600;
  padding: 6px 14px;
  border-radius: 999px;
  margin-bottom: 20px;
}
h1 {
  margin: 0;
  font-style: italic;
  font-size: clamp(36px, 5vw, 56px);
  line-height: 1.08;
}
.lede {
  margin-top: 20px;
  font-size: 16px;
  line-height: 1.6;
  color: #6b6577;
  max-width: 46ch;
}
.features {
  list-style: none;
  margin: 30px 0 0;
  padding: 0;
}
.features li {
  display: flex;
  align-items: center;
  gap: 14px;
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 16px;
}
.icon {
  width: 38px;
  height: 38px;
  border-radius: 50%;
  border: 1.5px solid #231f2c;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}
/* ---------- login form ---------- */
.login {
  background: #fffdf9;
  border: 2px solid #231f2c;
  padding: 32px;
}
.login h2 {
  margin: 0;
  font-style: italic;
  font-size: 24px;
}
.login .sub {
  margin: 6px 0 0;
  font-size: 14px;
  color: #6b6577;
}
.login label {
  display: block;
  font-size: 13px;
  font-weight: 700;
  margin: 20px 0 8px;
}
.login input[type='text'],
.login input[type='email'],
.login input[type='password'] {
  width: 100%;
  border: 1.5px solid #231f2c;
  border-radius: 10px;
  padding: 12px 14px;
  font-size: 15px;
  font-family: inherit;
}
.password-field {
  display: flex;
  gap: 8px;
}
.password-field input {
  flex: 1;
}
.password-field button {
  border: 1.5px solid #231f2c;
  background: #fffdf9;
  border-radius: 10px;
  padding: 0 14px;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
}
.forgot {
  display: block;
  text-align: right;
  margin-top: 8px;
  font-size: 13px;
  font-weight: 600;
  color: #6b9a55;
  text-decoration: none;
}
.remember {
  display: flex !important;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  font-weight: 500;
  color: #6b6577;
}
.remember input {
  width: 16px;
  height: 16px;
}
.submit {
  width: 100%;
  margin-top: 24px;
  border: 1.5px solid #231f2c;
  background: #4e9cd4;
  color: #231f2c;
  font-weight: 700;
  font-size: 15px;
  padding: 14px;
  border-radius: 10px;
  cursor: pointer;
}
.msg {
  margin-top: 12px;
  font-size: 13px;
  font-weight: 600;
  color: #6c4b9c;
  text-align: center;
}
.login hr {
  border: none;
  border-top: 1.5px solid #e7e0d3;
  margin: 24px 0 16px;
}
.signup {
  margin: 0;
  text-align: center;
  font-size: 14px;
  color: #6b6577;
}
.signup a {
  color: #4e9cd4;
  font-weight: 700;
}
/* ---------- landscape ---------- */
.landscape {
  display: block;
  width: 100%;
  margin-top: 56px;
}
/* ---------- footer ---------- */
footer {
  display: flex;
  flex-wrap: wrap;
  gap: 16px 32px;
  justify-content: space-between;
  padding: 20px 32px 32px;
  font-size: 14px;
  color: #6b6577;
}
</style>