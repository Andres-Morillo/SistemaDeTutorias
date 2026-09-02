<template>
  <aside class="sidebar">
    <div class="sidebar-header">
      <h2>Sistema De Tutorias</h2>
    </div>
    
    <nav class="sidebar-menu">
      <a href="#" class="menu-item">
        <span>🛖</span>
        <span>Inicio</span>
      </a>
      
      <a href="#" class="menu-item">
        <span>➕</span>
        <span>Nueva Citación</span>
      </a>
      
      <a href="#" class="menu-item">
        <span>📋</span>
        <span>Mis Citaciones</span>
      </a>

      <a href="#" class="menu-item">
        <span>📖</span>
        <span>Materias</span>
      </a>

      <a href="#" class="menu-item">
        <span>👥</span>
        <span>Estudiantes</span>
      </a>

      <a href="#" class="menu-item">
        <span>📅</span>
        <span>Horarios de Tutorias</span>
      </a>

      <a href="#" class="menu-item">
        <span>📊</span>
        <span>Reportes</span>
      </a>

      <a href="#" class="menu-item">
        <span>⚙️</span>
        <span>Configuración</span>
      </a>

    </nav>
     
    <div class="sidebar-footer">
      <button class="logout-btn">
        Cerrar sesión
      </button>
    </div>
  </aside>
</template>

<style scoped>
.sidebar {
  width: 250px;
  height: 100vh;
  background-color: #2c3e50;
  color: white;
  display: flex;
  flex-direction: column;
  position: fixed;
  left: 0;
  top: 0;
}

.sidebar-header {
  padding: 1.5rem;
  border-bottom: 1px solid #ffffff33;
}

.sidebar-header h2 {
  margin: 0;
}

.sidebar-menu {
  display: flex;
  flex-direction: column;
  padding: 1rem;
  gap: 0.5rem;
}

.menu-item {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  padding: 0.8rem;
  color: white;
  text-decoration: none;
  border-radius: 5px;
}

.menu-item:hover {
  background-color: #34495e;
}

.sidebar-footer {
  margin-top: auto;
  padding: 1rem;
}

.logout-btn {
  width: 100%;
  padding: 0.7rem;
  border: none;
  border-radius: 5px;
  background-color: #e74c3c;
  color: white;
  cursor: pointer;
}

.logout-btn:hover {
  background-color: #c0392b;
}
</style scoped>