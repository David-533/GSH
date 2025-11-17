<template>
  <div class="home-container">
    <!-- Sidebar / Navbar -->
    <nav class="navbar">
      <div class="title">SOCIAL</div>

      <!-- Liens de navigation -->
      <div class="nav-links">
        <router-link to="/home" class="nav-item">Accueil</router-link>
        <router-link to="/profil" class="nav-item">Profile</router-link>
        <router-link to="/messages" class="nav-item">Messages</router-link>
      </div>

      <!-- Bouton logout -->
      <button class="logout-btn" @click="handleLogout" id="logoutBtn">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="white">
          <path d="M10 17v-3H3v-4h7V7l5 5-5 5zM20 3h-8v2h8v14h-8v2h8c1.1 0 2-0.9 2-2V5c0-1.1-0.9-2-2-2z"/>
        </svg>
      </button>
    </nav>

    <!-- Contenu principal -->
    <div class="home">

    </div>

    <!-- Bouton flottant -->
    <button class="floating-btn" @click="openPostModal">+</button>

    <!-- Modale de création de post -->
    <div v-if="isModalOpen" class="modal-overlay" @click.self="closePostModal">
      <div class="modal">
        <h2>Créer un post</h2>
        <textarea placeholder="Écris ton post..." rows="5"></textarea>
        <div class="modal-actions">
          <button @click="submitPost">Publier</button>
          <button @click="closePostModal">Annuler</button>
        </div>
      </div>
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
    const isModalOpen = ref(false);

    onMounted(() => {
      const pseudo = localStorage.getItem("userPseudo");
      if (pseudo) userPseudo.value = pseudo;
    });

    const handleLogout = () => {
      localStorage.removeItem("userPseudo");
      localStorage.removeItem("userEmail");
      router.push("/login");
    };

    const openPostModal = () => isModalOpen.value = true;
    const closePostModal = () => isModalOpen.value = false;
    const submitPost = () => {
      alert("Post publié !");
      closePostModal();
    };

    return { userPseudo, handleLogout, isModalOpen, openPostModal, closePostModal, submitPost };
  },
};
</script>


<style scoped>
.home-container {
  display: flex;
  flex-direction: row;
  height: 100vh;
}

/* Sidebar / Navbar */
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
  z-index: 1000;
}

.title {
  font-size: 1.5rem;
  color: #fff;
  font-family: 'Roboto', sans-serif;
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
  font-family: 'Roboto', sans-serif; /* même police que le titre */
  font-size: 1.5rem;                /* même taille que le titre */
  width: 100%;
  text-align: center;
  padding: 5px 0;
  text-decoration: none;             /* enlève le surlignement */
}

.nav-item:hover {
  background-color: transparent;     /* pas d’encadré au survol */
  text-decoration: none;             /* pas de soulignement */
}

.logout-btn {
  background: none;
  border: none;
  margin-top: 600px;  /* augmente la valeur pour descendre le bouton */
  cursor: pointer;
  padding: 15px;       /* espace autour de l'icône */
  display: flex;
  align-items: center;
  justify-content: center;
}

.logout-btn svg {
  fill: white;
  width: 30px;   /* taille plus grande pour être visible */
  height: 30px;
}

.logout-btn:hover svg {
  transform: scale(1.2); 
  transition: transform 0.2s;
}


.home {
  flex: 1;
  margin-left: 200px; /* espace pour la sidebar */
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: 2rem;
  text-align: center;
  font-family: 'Roboto', sans-serif;
}
/* Bouton flottant */
.floating-btn {
  position: fixed;
  bottom: 30px;
  right: 30px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: #000;
  color: white;
  font-size: 2rem;
  border: none;
  cursor: pointer;
  box-shadow: 0 4px 8px rgba(0,0,0,0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.2s;
  z-index: 1001;
}

.floating-btn:hover {
  transform: scale(1.1);
}

/* Modale */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0,0,0,0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1002;
}

.modal {
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
  width: 400px;
  max-width: 90%;
}

.modal textarea {
  width: 100%;
  margin: 10px 0;
  padding: 10px;
  font-size: 1rem;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}

.modal-actions button {
  padding: 8px 15px;
  cursor: pointer;
}
</style>
