<template>
  <div :style="{ backgroundImage: backgroundUrl }">
    <h2 style="color: lightgrey; text-align: center;">Choose Album:</h2>
    <select v-model="selectedAlbum" @change="fetchPhotos" style="margin: 0 auto; display: block;">
      <option v-for="album in albums" :key="album.id" :value="album.id">{{ album.title }}</option>
    </select>
    <h2 style="color: lightgrey; text-align: center;">Photos in Album {{ selectedAlbum }}</h2>
    <table v-if="photos.length" style="margin: 0 auto; max-width: 80%; border-collapse: collapse;">
      <thead>
        <tr>
          <th style="border: 2px solid white; max-width: 500px; word-wrap: break-word;">Thumbnail</th>
          <th style="border: 2px solid white; max-width: 500px; word-wrap: break-word;">Title</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="photo in photos" :key="photo.id" style="text-align: center;">
          <td>
            <img :src="photo.thumbnailUrl" @click="showPhoto(photo.url)" style="width: 100px; height: 100px; object-fit: cover; border: 2px solid #7c08007e; border-radius: 5px; cursor: pointer; transition: border-color 0.3s;">
          </td>
          <td style="border: 2px solid white; max-width: 300px; word-wrap: break-word;">{{ photo.title }}</td>
        </tr>
      </tbody>
    </table>
    <div v-else style="text-align: center; margin-top: 20px;">
      <p>No photos available.</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const selectedAlbum = ref(route.params.id)
const albums = ref([])
const photos = ref([])

const fetchAlbums = async () => {
  try {
    const response = await fetch(`https://jsonplaceholder.typicode.com/albums`)
    albums.value = await response.json()
  } catch (error) {
    console.error('Error fetching albums:', error)
  }
}

const fetchPhotos = async () => {
  try {
    const response = await fetch(`https://jsonplaceholder.typicode.com/albums/${selectedAlbum.value}/photos`)
    photos.value = await response.json()
  } catch (error) {
    console.error('Error fetching photos:', error)
  }
}

const showPhoto = (url) => {
  window.open(url, '_blank')
}

onMounted(() => {
  fetchAlbums()
  fetchPhotos()
})
</script>

<style scoped>
.album-details {
  background-size: cover;
  background-position: center;
  color: white;
}
.text-lightgrey {
  color: lightgrey;
}
.text-center {
  text-align: center;
}
</style>