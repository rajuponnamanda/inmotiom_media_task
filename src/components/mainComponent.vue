<template>
  <div>
    <!-- Navbar Section -->
    <nav class="navbar">
      <div class="navbar-title">My App</div>
      <input type="text" v-model="searchQuery" placeholder="Search..." class="navbar-search">
    </nav>

    <!-- Main Content -->
    <div class="container">
      <div class="card-details" v-if="selectedImage">
        <h3>{{ selectedImage.label }}</h3>
        <img :src="selectedImage.url" class="displayed-image" alt="">
      </div>
      <div v-if="loading" class="loading-message">Loading...</div>
      <div v-else>
        <div v-if="filteredItems.length === 0" class="no-results-message">Item not found</div>
        <div class="row" v-else>
          <div
            v-for="(item, index) in filteredItems"
            :key="index"
            class="col-md-3"
            @click="showDetails(item)"
          >
            <div class="grid-item">
              <img :src="item.url" class="grid-image" />
              <p class="grid-label">{{ item.label }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref, computed } from 'vue'
import axios from 'axios'

const items = ref([])
const loading = ref(true)
const selectedImage = ref(null)
const searchQuery = ref('')

const fetchData = async () => {
  try {
    const response = await axios.get(
      'https://s3.eu-west-2.amazonaws.com/assets-test.fast-thinking.co.uk/recruitment/data.json'
    )
    items.value = response.data
    loading.value = false
  } catch (error) {
    console.error('Error fetching data:', error)
    loading.value = false
  }
}

const showDetails = (item) => {
  selectedImage.value = item
}

const filteredItems = computed(() => {
  if (!searchQuery.value) {
    return items.value
  }
  return items.value.filter(item => item.label.toLowerCase().includes(searchQuery.value.toLowerCase()))
})

onMounted(() => {
  fetchData()
})
</script>

<style>
/* Navbar styles */
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 10px;
  background-color: #9ac536;
  color: white;
  position: fixed;
  width: 100%;
  top: 0;
  z-index: 1000;
}

.navbar-title {
  font-size: 1.2em;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.navbar-search {
  padding: 5px 10px;
  font-size: 0.9em;
  border-radius: 20px;
  border: 1px solid #ddd;
  width: 200px;
  transition: width 0.3s ease-in-out;
}

.navbar-search:focus {
  width: 250px;
  border-color: #9ac536;
  outline: none;
}

/* Main container styles */
.container {
  margin: 60px auto 20px; /* Reduced top margin to account for fixed navbar */
  max-width: 1200px;
  padding: 0 20px;
}

/* Center the card details */
.card-details {
  text-align: center;
  margin-bottom: 20px;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #f9f9f9;
}

.card-details h3 {
  margin-bottom: 20px;
}

.displayed-image {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
}

/* Grid styles */
.row {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
}

.col-md-3 {
  flex: 1 1 calc(25% - 20px);
  max-width: calc(25% - 20px);
  box-sizing: border-box;
}

.grid-item {
  text-align: center;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
  transition: transform 0.3s, box-shadow 0.3s;
  background-color: #fff;
}

.grid-item:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.grid-image {
  max-width: 100%;
  height: 120px; /* Reduced image size */
  object-fit: cover;
  border-radius: 8px;
  transition: transform 0.3s;
}

.grid-image:hover {
  transform: scale(1.1);
}

.grid-label {
  margin-top: 10px;
  font-weight: bold;
}

/* Loading message styles */
.loading-message {
  text-align: center;
  font-size: 1.2em;
  margin-top: 20px;
}

/* No results message styles */
.no-results-message {
  text-align: center;
  font-size: 1.6em;
  margin-top: 200px;
  color: #f20e0e;
  font-weight: bolder;
}

/* Media queries for responsiveness */
@media (max-width: 1200px) {
  .col-md-3 {
    flex: 1 1 calc(33.33% - 20px);
    max-width: calc(33.33% - 20px);
  }
}

@media (max-width: 992px) {
  .col-md-3 {
    flex: 1 1 calc(50% - 20px);
    max-width: calc(50% - 20px);
  }
}

@media (max-width: 768px) {
  .navbar {
    flex-direction: column;
    align-items: flex-start;
  }
  
  .navbar-search {
    margin-top: 10px;
    width: 100%;
  }
  
  .col-md-3 {
    flex: 1 1 calc(100% - 20px);
    max-width: calc(100% - 20px);
  }
}
</style>
