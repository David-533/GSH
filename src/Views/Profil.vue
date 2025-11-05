<template>
  <div class="page-container">
    <!-- Navbar -->
    <nav class="navbar">
      <div class="title">SOCIAL</div>
      <div class="nav-links">
        <router-link to="/home" class="nav-item">Accueil</router-link>
        <router-link to="/profil" class="nav-item">Profile</router-link>
        <router-link to="/messages" class="nav-item">Messages</router-link>
      </div>
      <button class="logout-btn" @click="handleLogout">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="white">
          <path d="M10 17v-3H3v-4h7V7l5 5-5 5zM20 3h-8v2h8v14h-8v2h8c1.1 0 2-0.9 2-2V5c0-1.1-0.9-2-2-2z"/>
        </svg>
      </button>
    </nav>

    <!-- Contenu -->
    <div class="content">
      <div class="profile-header">
        <img class="profile-pic" :src="userPhoto || defaultPhoto" alt="">
        <div class="user-info">
          <h1>{{ userPseudo || "Pseudo" }}</h1>
          <div class="stats">
            <div><strong>{{ posts.length }}</strong> Publications</div>
          </div>
          <p>{{ userBio || 'Aucune bio renseignée pour le moment.' }}</p>

          <button class="edit-btn" @click="triggerFileInput">Modifier le profil</button>
          <input type="file" ref="fileInput" accept="image/*" @change="handleFileChange" style="display: none;">
        </div>
      </div>

      <!-- Galerie -->
      <div class="underline-wrapper">
        <div class="underline"></div>
        <div class="posts-gallery">
          <div v-for="(post, index) in posts" :key="index" class="post-container">
            <img :src="post" class="post-image" @click="openModal(index)" />
          </div>
        </div>
      </div>

      <input type="file" ref="postInput" accept="image/*" @change="handlePostFileChange" style="display:none;">
    </div>

    <div class="add-post-container">
      <button class="add-post-btn" @click="triggerPostInput">+</button>
    </div>

   <!-- Modal -->
<div v-if="modalOpen" class="modal-overlay" @click.self="closeModal">
  <div class="modal-content">
    <div class="modal-left">
      <img :src="posts[modalIndex]" class="modal-image" />
    </div>
    <div class="modal-right">
      <div class="comments-header">
        <h3>Commentaires</h3>
        <!-- Menu à trois points -->
        <div class="menu-container" @click.stop="toggleMenu">
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="black" viewBox="0 0 24 24">
            <circle cx="12" cy="5" r="2"/>
            <circle cx="12" cy="12" r="2"/>
            <circle cx="12" cy="19" r="2"/>
          </svg>
          <div v-if="menuOpen" class="dropdown-menu">
            <div class="dropdown-item" @click="deletePost(modalIndex)">Supprimer la photo</div>
          </div>
        </div>
      </div>

      <div class="comments-list">
        <div v-for="(comment, idx) in comments[modalIndex] || []" :key="idx" class="comment">
          <div class="comment-top">
            <img class="comment-avatar" :src="userPhoto || defaultPhoto" />
            <strong class="comment-pseudo">{{ userPseudo || 'Pseudo' }}</strong>
          </div>
          <div class="comment-text">{{ comment }}</div>
        </div>
      </div>

      <div class="add-comment">
        <input v-model="newComment" placeholder="Ajouter un commentaire..." />
        <button @click="addComment(modalIndex)" class="send-btn">
          <svg xmlns="http://www.w3.org/2000/svg" class="send-icon" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 10l9-9m0 0l9 9m-9-9v18"/>
          </svg>
        </button>
      </div>
    </div>
  </div>
</div>
</div>
</template>

<script>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";

export default {
  name: "Profil",
  setup() {
    const router = useRouter();

    const userPseudo = ref(localStorage.getItem("userPseudo") || "");
    const userPhoto = ref(localStorage.getItem("userPhoto") || null);
    
    const userBio = ref(localStorage.getItem("userBio") || "");
    const fileInput = ref(null);
    const postInput = ref(null);
    const defaultPhoto = "https://via.placeholder.com/150";

    const posts = ref([]);
    const comments = ref([]);
    const newComment = ref("");

    const modalOpen = ref(false);
    const modalIndex = ref(0);

    // Charger les publications depuis localStorage
    onMounted(() => {
      const savedPosts = JSON.parse(localStorage.getItem("userPosts") || "[]");
      posts.value = savedPosts;
    });

    // ⚡ Modification : ne supprime pas l'avatar
    const handleLogout = () => {
      ["userPseudo", "userBio"].forEach(key => localStorage.removeItem(key));
      router.push("/login");
    };

    const triggerFileInput = () => fileInput.value.click();
    const handleFileChange = (e) => {
      const file = e.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = () => {
        userPhoto.value = reader.result;
        localStorage.setItem("userPhoto", reader.result);
      };
      reader.readAsDataURL(file);
    };

    const triggerPostInput = () => postInput.value.click();
    const handlePostFileChange = (e) => {
      const file = e.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = () => {
        posts.value.push(reader.result);
        localStorage.setItem("userPosts", JSON.stringify(posts.value));
      };
      reader.readAsDataURL(file);
    };

    const openModal = (index) => {
      modalIndex.value = index;
      modalOpen.value = true;
    };
    const closeModal = () => modalOpen.value = false;

    const addComment = (index) => {
      if (!newComment.value.trim()) return;
      if (!comments.value[index]) comments.value[index] = [];
      comments.value[index].push(newComment.value);
      newComment.value = "";
    };
    const menuOpen = ref(false);

const toggleMenu = () => {
  menuOpen.value = !menuOpen.value;
};

const deletePost = (index) => {
  posts.value.splice(index, 1);
  localStorage.setItem("userPosts", JSON.stringify(posts.value));
  closeModal();
};


    return {
      userPseudo, userPhoto, userBio, defaultPhoto,
      fileInput, postInput, posts, comments, newComment,
      modalOpen, modalIndex,
      handleFileChange, handleLogout, triggerFileInput,
      triggerPostInput, handlePostFileChange, openModal,
      closeModal, addComment,
    };
  }
};
</script>


<style scoped>
.page-container {
  display: flex;
}

/* Navbar */
.navbar {
  width: 200px;
  height: 100vh;
  background-color: #000;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px 0;
  position: fixed;
  top: 0;
  left: 0;
  font-family: 'Roboto', sans-serif;
}

.title {
  font-size: 1.5rem;
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
  font-size: 1.5rem;
  width: 100%;
  text-align: center;
  padding: 5px 0;
  text-decoration: none;
}

.logout-btn {
  background: none;
  border: none;
  margin-top: 600px;
  cursor: pointer;
  padding: 15px;
}

.logout-btn svg {
  fill: white;
  width: 30px;
  height: 30px;
}

/* Content */
.content {
  margin-left: 200px;
  padding: 40px;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

/* Profile header */
.profile-header {
  display: flex;
  gap: 30px;
  width: 100%;
  max-width: 600px;
}

.profile-pic {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  object-fit: cover;
  border: solid black 2px;
}

.stats { margin: 10px 0; }

.edit-btn {
  padding: 8px 16px;
  border: 1px solid #ccc;
  background: #fff;
  border-radius: 6px;
}

/* Underline + posts */
.underline-wrapper {
  width: 100%;
  max-width: 800px;
  margin: 40px auto 0;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.underline {
  width: 100%;
  height: 2px;
  background-color: black;
  margin-bottom: 20px;
}

.posts-gallery {
  display: grid;
  grid-template-columns: repeat(3, 260px);
  gap: 10px;
}

.post-container {
  width: 260px;
}

.post-image {
  width: 100%;
  aspect-ratio: 1/1;
  object-fit: cover;
  border-radius: 6px;
}

/* + button */
.add-post-container {
  position: fixed;
  bottom: 80px;
  left: calc(50% + 100px);
  transform: translateX(-50%);
}

.add-post-btn {
  width: 70px;
  height: 70px;
  font-size: 40px;
  border-radius: 50%;
  border: 1px solid #ccc;
  background: #f0f0f0;
}

/* Modal */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0,0,0,0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  display: flex;
  width: 70vw;
  max-width: 1000px;
  height: 80vh;
  background-color: #fff;
  border-radius: 6px;
  overflow: hidden;
}

.modal-left {
  flex: 0 0 50%;
  background-color: black;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}

.modal-image {
  width: auto;
  height: 100%;       /* pleine hauteur du modal */
  object-fit: cover;   /* conserve le style actuel */
  image-rendering: auto; /* force le navigateur à ne pas faire de "smoothing" inutile */
  border-radius: 6px;
}

.modal-right {
  flex: 0 0 50%; /* commentaires prennent 50% */
  background-color: #fff;
  display: flex;
  flex-direction: column;
  padding: 15px;
}


.comments-header {
  border-bottom: 1px solid #ccc;
  padding-bottom: 5px;
  margin-bottom: 5px;
}

.comments-list {
  flex: 1;
  overflow-y: auto;
  margin-bottom: 10px; /* espace avant le champ de saisie */
}

.comment {
  padding: 8px 0;
  border-bottom: 1px solid #eee;
}

.comment-top {
  display: flex;
  align-items: center;
  gap: 8px;
}

.comment-avatar {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  object-fit: cover;
}

.comment-pseudo {
  font-size: 0.85rem;
}

.comment-text {
  margin-left: 32px; /* laisse de l'espace pour l'avatar */
  font-size: 0.9rem;
}

.add-comment {
  display: flex;
  align-items: center;
  justify-content: center; /* centre horizontalement */
  width: 100%;
  padding-top: 6px;
  box-sizing: border-box;
}

.add-comment input {
  flex: none;
  width: 85%; /* ajuste ici (80–90%) selon ton goût */
  padding: 5px 10px;
  border: 1px solid #ccc;
  border-radius: 20px;
  box-sizing: border-box;
  font-size: 0.8rem;
  outline: none;
}

.add-comment input:focus {
  border-color: #000;
}

.send-btn {
  background: none;
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-left: 6px; /* petit espace pour respirer */
  padding: 4px;
  border-radius: 50%;
  flex-shrink: 0;
}

.send-icon {
  width: 16px;
  height: 16px;
  color: #000;
}

.comments-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-bottom: 1px solid #ccc;
  padding-bottom: 5px;
  margin-bottom: 5px;
  position: relative;
}

.menu-container {
  cursor: pointer;
  position: relative;
}

.dropdown-menu {
  position: absolute;
  top: 25px;
  right: 0;
  background: white;
  border: 1px solid #ccc;
  border-radius: 6px;
  min-width: 150px;
  z-index: 10;
  box-shadow: 0px 2px 6px rgba(0,0,0,0.2);
}

.dropdown-item {
  padding: 8px 12px;
  cursor: pointer;
}

.dropdown-item:hover {
  background-color: #f0f0f0;
}


</style>
