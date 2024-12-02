<template>
  <div class="unsplash-gallery">
    <h1>Unsplash Photo Search</h1>

    <!-- Input for keyword search -->
    <div class="search-bar">
      <input
        v-model="searchQuery"
        @keyup.enter="fetchImage"
        placeholder="Search for a photo (e.g., mountains)"
      />
      <button @click="fetchImage">Search</button>
    </div>

    <!-- Loading indicator -->
    <div v-if="loading" class="loading">Loading...</div>

    <!-- Display the single photo -->
    <div v-if="image" class="photo">
      <img :src="image.urls.small" :alt="image.alt_description" />
      <p>
        Photo by
        <a
          :href="
            image.user.links.html +
            '?utm_source=vue_unsplash&utm_medium=referral'
          "
          target="_blank"
          rel="noopener noreferrer"
        >
          {{ image.user.name }}
        </a>
        on <a href="https://unsplash.com" target="_blank">Unsplash</a>
      </p>
    </div>

    <!-- No results -->
    <div v-if="!image && !loading">No results found.</div>
  </div>
</template>

<script>
import { ref } from "vue";

export default {
  name: "UnsplashGallery",
  setup() {
    const searchQuery = ref(""); // Store the search keyword
    const image = ref(null); // Store the fetched image
    const loading = ref(false); // Loading state

    // Replace this with your Unsplash Access Key
    const accessKey = "ZWVkEFBgg7hxIxiajoHay61lTRO08SoeHdhdlKnSgqM";

    // Function to fetch a single image
    const fetchImage = async () => {
      if (!searchQuery.value.trim()) {
        alert("Please enter a keyword to search.");
        return;
      }

      loading.value = true; // Set loading state to true
      try {
        const response = await fetch(
          `https://api.unsplash.com/search/photos?query=${encodeURIComponent(
            searchQuery.value
          )}&client_id=${accessKey}`
        );

        // Check if the response is OK
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();

        // Get the first image result
        image.value = data.results[0] || null;
      } catch (error) {
        console.error("Error fetching image:", error);
      } finally {
        loading.value = false; // Reset loading state
      }
    };

    return {
      searchQuery,
      image,
      loading,
      fetchImage,
    };
  },
};
</script>

<style>
.unsplash-gallery {
  font-family: Arial, sans-serif;
  padding: 20px;
  text-align: center;
}

.search-bar {
  margin-bottom: 20px;
}

.search-bar input {
  width: 300px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.search-bar button {
  padding: 10px 20px;
  background-color: #007aff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.search-bar button:hover {
  background-color: #005bb5;
}

.photo img {
  width: 100%;
  max-width: 500px;
  height: auto;
  border-radius: 8px;
}

.loading {
  font-size: 1.5em;
  color: gray;
}
</style>
