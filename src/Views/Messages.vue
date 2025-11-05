<template>
  <div class="page-container">
    <!-- Navbar -->
    <nav class="navbar">
      <div class="title">SOCIAL</div>

      <!-- Liens -->
      <div class="nav-links">
        <router-link to="/home" class="nav-item">Accueil</router-link>
        <router-link to="/profil" class="nav-item">Profil</router-link>
        <router-link to="/messages" class="nav-item">Messages</router-link>
      </div>

      <!-- Bouton logout -->
      <button class="logout-btn" @click="handleLogout" id="logoutBtn">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="white">
          <path d="M10 17v-3H3v-4h7V7l5 5-5 5zM20 3h-8v2h8v14h-8v2h8c1.1 0 2-0.9 2-2V5c0-1.1-0.9-2-2-2z"/>
        </svg>
      </button>
    </nav>

    <!-- Contenu -->
    <div class="content">
      <h1>Messages</h1>
      <p>Bienvenue sur vos messages</p>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";

export default {
  name: "Home",
  setup() {
    const router = useRouter();
    const userPseudo = ref("");

    onMounted(() => {
      const pseudo = localStorage.getItem("userPseudo");
      if (pseudo) userPseudo.value = pseudo;
    });

    const handleLogout = () => {
      localStorage.removeItem("userPseudo");
      localStorage.removeItem("userEmail");
      router.push("/login"); // redirection vers Login.vue
    };

    return { userPseudo, handleLogout };
  },
};
</script>

<style scoped>
/* Conteneur général */
.page-container {
  display: flex;
}

.navbar {
  width: 200px;
  height: 100vh;
  background-color: #000;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px 0;
  box-shadow: 2px 0 5px rgba(0,0,0,0.5);
  position: fixed;
  top: 0;
  left: 0;
  font-family: 'Roboto', sans-serif;
}

.title {
  font-size: 1.5rem; /* même que Home.vue */
  color: #fff;
  margin-bottom: 40px;
}

.nav-links {
  display: flex;
  flex-direction: column;
  gap: 20px;
  width: 100%;
  align-items: center;
}

.nav-item {
  color: #fff;
  font-family: 'Roboto', sans-serif;
  font-size: 1.5rem; /* même que Home.vue */
  width: 100%;
  text-align: center;
  padding: 5px 0;
  text-decoration: none;
}

.nav-item:hover {
  background-color: transparent;
  text-decoration: none;
}

.logout-btn {
  background: none;
  border: none;
  margin-top: 600px;
  cursor: pointer;
  padding: 15px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.logout-btn svg {
  fill: white;
  width: 30px;
  height: 30px;
}

.logout-btn:hover svg {
  transform: scale(1.2);
  transition: transform 0.2s;
}


/* === CONTENU PRINCIPAL === */
.content {
  margin-left: 200px; /* espace pour la navbar */
  padding: 40px;
  color: #222;
}

.content h1 {
  font-size: 2rem;
  margin-bottom: 10px;
}

.content p {
  font-size: 1.3rem;
}
</style>