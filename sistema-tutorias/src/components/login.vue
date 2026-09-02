<template>
  <div class="login_input">
    <input type="text" placeholder="Usuario" class="inpStyle" />
    <input type="password" placeholder="Contraseña" class="inpStyle" />
    <button class="accBtn">Iniciar Sesión</button>
  </div>
</template>

<style scoped>
.login_input {
  display: flex;
  flex-direction: column;
  gap: 1rem;

  width: 100%;
  max-width: 360px;
  margin: 0 auto;
  padding: 1rem;
  box-sizing: border-box;
}

.inpStyle {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid var(--color-border);
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 1rem;
}

.accBtn {
  width: 100%;
  padding: 0.5rem;
  border: none;
  border-radius: 4px;
  background-color: #4CAF50;
  color: var(--color-background);
  cursor: pointer;
  font-size: 1rem;
}

.accBtn:hover {
  opacity: 0.9;
}

/* Ajustes opcionales para pantallas grandes */
@media (min-width: 1024px) {
  .login_input {
    max-width: 400px;
  }
}
</style>