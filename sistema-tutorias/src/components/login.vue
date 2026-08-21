<template>
  <div class="login_input">
    <input type="text" placeholder="Usuario" class="inpStyle"/>
    <input type="password" placeholder="Contraseña" class="inpStyle"/>
    <button class="accBtn">Iniciar Sesión</button>
  </div>
</template>

<style scoped>
.login_input {
  display: flex;
  flex-direction: column;
  gap: 1rem;

  display: flex;
  position: relative;
}

.details {
  flex: 1;
  margin-left: 1rem;
}

i {
  display: flex;
  place-items: center;
  place-content: center;
  width: 32px;
  height: 32px;

  color: var(--color-text);
}

h3 {
  font-size: 1.2rem;
  font-weight: 500;
  margin-bottom: 0.4rem;
  color: var(--color-heading);
}

@media (min-width: 1024px) {
  .item {
    margin-top: 0;
    padding: 0.4rem 0 1rem calc(var(--section-gap) / 2);
  }

  i {
    top: calc(50% - 25px);
    left: -26px;
    position: absolute;
    border: 1px solid var(--color-border);
    background: var(--color-background);
    border-radius: 8px;
    width: 50px;
    height: 50px;
  }

  .item:before {
    content: ' ';
    border-left: 1px solid var(--color-border);
    position: absolute;
    left: 0;
    bottom: calc(50% + 25px);
    height: calc(50% - 25px);
  }

  .item:after {
    content: ' ';
    border-left: 1px solid var(--color-border);
    position: absolute;
    left: 0;
    top: calc(50% + 25px);
    height: calc(50% - 25px);
  }

  .item:first-of-type:before {
    display: none;
  }

  .item:last-of-type:after {
    display: none;
  }
}

.inpStyle {
  padding: 0.5rem;
  border: 1px solid var(--color-border);
  border-radius: 4px;
}

.accBtn {
  padding: 0.5rem;
  border: none;
  border-radius: 4px;
  background-color: #4CAF50;
  color: var(--color-background);
  cursor: pointer;
}
</style>
