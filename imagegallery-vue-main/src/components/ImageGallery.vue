<template>
  <div class="gallery">
    <h1 class="gallery-heading">My Image Gallery</h1>

    <!-- Folder Navigation with Delete Option -->
    <div class="folder-navigation">
      <div
        v-for="(folder, index) in folders"
        :key="index"
        class="folder-item"
      >
        <button @click="selectFolder(folder)" :class="{ active: selectedFolder === folder }">
          {{ folder.name }}
        </button>
           <button class="delete-folder" @click.stop="deleteFolder(index)">🗑</button>
      </div>
      <button @click="createNewFolder">+ New Folder</button>
    </div>

    <!-- Input Container for Adding Images -->
    <div class="input-container">
      <input v-model="newImageUrl" type="text" placeholder="Enter image URL" />
      <input v-model="newImageName" type="text" placeholder="Enter image name" @keyup.enter="addImage" />
      <button @click="addImage">Add Image</button>
    </div>

    <!-- Gallery Grid -->
    <div class="gallery-grid" v-if="selectedFolder">
      <ImageItem
        v-for="(image, index) in selectedFolder.images"
        :key="index"
        :image="image"
        :index="index"
        @select="handleSelect"
        @delete="deleteImage"
      />
    </div>

    <!-- Lightbox for Image Preview -->
    <div v-if="selectedImage" class="lightbox" @click="closeLightbox">
      <img :src="selectedImage.src" :alt="selectedImage.alt" />
    </div>
  </div>
</template>

<script>
import ImageItem from './ImageItem.vue';

export default {
  components: { ImageItem },
  data() {
    return {
      folders: [{ name: 'Default', images: [] }],
      selectedFolder: null,
      selectedImage: null,
      newImageUrl: '',
      newImageName: '',
    };
  },
  methods: {
    selectFolder(folder) {
      this.selectedFolder = folder;
    },
    createNewFolder() {
      const folderName = prompt('Enter the new folder name:');
      if (folderName) {
        this.folders.push({ name: folderName, images: [] });
      }
    },
    deleteFolder(index) {
      this.folders.splice(index, 1);
      this.selectedFolder = this.folders[0] || null;
    },
    addImage() {
      if (this.newImageUrl.trim() !== '' && this.newImageName.trim() !== '') {
        this.selectedFolder.images.push({
          src: this.newImageUrl.trim(),
          alt: this.newImageName.trim(),
        });
        this.newImageUrl = '';
        this.newImageName = '';
      }
    },
    handleSelect(image) {
      this.selectedImage = image;
    },
    closeLightbox() {
      this.selectedImage = null;
    },
    deleteImage(index) {
      this.selectedFolder.images.splice(index, 1);
    },
  },
  created() {
    this.selectedFolder = this.folders[0]; // Default to the first folder
  },
};
</script>

<style scoped>
/* Same as before with some updates */
.gallery {
  max-width: 1200px;
  margin: auto;
  padding: 20px;
  background-color: #f8f8f8;
}

.gallery-heading {
  text-align: center;
  color: #333;
  margin-bottom: 20px;
  font-size: 2em;
  font-weight: bold;
}

.folder-navigation {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.folder-item {
  display: flex;
  align-items: center;
}

.folder-item button {
  margin: 0 5px;
  padding: 10px 20px;
  font-size: 16px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.folder-item button.active {
  background-color: #0056b3;
}

.delete-folder {
  background-color: transparent;
  color: red;
  border: none;
  font-size: 18px;
  cursor: pointer;
}

.delete-folder:hover {
  color: darkred;
}

.input-container {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.input-container input {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-right: 10px;
  width: 40%;
}

.input-container button {
  padding: 10px 20px;
  font-size: 16px;
  border: none;
  border-radius: 5px;
  background-color: #28a745;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.input-container button:hover {
  background-color: #218838;
}

.gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 15px;
}

.lightbox {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
}

.lightbox img {
  max-width: 90%;
  max-height: 80%;
  border-radius: 10px;
}
</style>
