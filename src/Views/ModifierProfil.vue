<template>
  <div class="modifier-page">
    <h1>Modifier le profil</h1>

    <!-- Photo de profil -->
    <img :src="userPhoto || defaultPhoto" @click="triggerFileInput" alt="" />
    <input type="file" ref="fileInput" accept="image/*" @change="handleFileChange" style="display:none" />

    <!-- Pseudo -->
    <label>Pseudo</label>
    <input type="text" v-model="userPseudo" placeholder="Entrez votre pseudo" />

    <!-- Bio -->
    <label>Bio</label>
    <textarea v-model="userBio" placeholder="Entrez votre bio..."></textarea>

    <!-- Genre -->
    <label>Genre</label>
    <select v-model="gender">
      <option value="homme">Homme</option>
      <option value="femme">Femme</option>
      <option value="autre">Autre</option>
    </select>

    <!-- Bouton enregistrer -->
    <button @click="saveProfile">Enregistrer</button>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";

export default {
  name: "ModifierProfil",
  setup() {
    const router = useRouter();

    const userPseudo = ref("");
    const userPhoto = ref(null);
    const userBio = ref("");
    const gender = ref(""); // maintenant sauvegardé
    const fileInput = ref(null);
    const defaultPhoto = "https://via.placeholder.com/150";

    onMounted(() => {
      // Charger les infos depuis le localStorage
      userPseudo.value = localStorage.getItem("userPseudo") || "";
      userPhoto.value = localStorage.getItem("userPhoto") || null;
      userBio.value = localStorage.getItem("userBio") || "";
      gender.value = localStorage.getItem("userGender") || ""; // récupération du genre
    });

    const triggerFileInput = () => fileInput.value.click();

    const handleFileChange = (e) => {
      const file = e.target.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = () => {
        userPhoto.value = reader.result;
      };
      reader.readAsDataURL(file);
    };

    const saveProfile = () => {
      // Sauvegarde toutes les infos
      localStorage.setItem("userPseudo", userPseudo.value);
      localStorage.setItem("userPhoto", userPhoto.value);
      localStorage.setItem("userBio", userBio.value);
      localStorage.setItem("userGender", gender.value); // sauvegarde du genre
      router.push("/profil"); // retour vers la page profil
    };

    return {
      userPseudo, userPhoto, userBio, gender, defaultPhoto, fileInput,
      triggerFileInput, handleFileChange, saveProfile
    };
  }
};
</script>

<style scoped>
.modifier-page {
  max-width: 500px;
  margin: 50px auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
  display: flex;
  flex-direction: column;
  gap: 20px;
  font-family: 'Roboto', sans-serif;
}

.modifier-page h1 {
  text-align: center;
  font-size: 1.8rem;
  color: #333;
  margin-bottom: 20px;
}

.modifier-page img {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  object-fit: cover;
  margin: 0 auto;
  border: 2px solid #000;
  cursor: pointer;
}

.modifier-page label {
  font-weight: bold;
  color: #555;
}

.modifier-page input[type="text"],
.modifier-page textarea,
.modifier-page select {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 1rem;
  resize: none;
  font-family: 'Roboto', sans-serif;
  box-sizing: border-box;
}

.modifier-page textarea {
  min-height: 60px;
  max-height: 120px;
}

.modifier-page button {
  background-color: #0095f6;
  color: white;
  border: none;
  padding: 10px 0;
  border-radius: 6px;
  font-size: 1rem;
  cursor: pointer;
  transition: background 0.2s ease;
}

.modifier-page button:hover {
  background-color: #007ac1;
}

/* Responsive */
@media screen and (max-width: 600px) {
  .modifier-page {
    margin: 20px;
    padding: 15px;
  }

  .modifier-page img {
    width: 100px;
    height: 100px;
  }
}
</style>
