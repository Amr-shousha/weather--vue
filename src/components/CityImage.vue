<template>
  <div>
    <div class="search-bar">
      <!-- <input
        v-model="searchQuery"
        @keyup.enter="fetchImage"
        placeholder="Search for a photo (e.g., mountains)"
      /> -->
      <!-- <button @click="fetchImage">Search</button> -->
    </div>
    <div v-if="image?.urls?.small" class="photo">
      <img
        :src="image.urls.small"
        :alt="image.alt_description || 'City Image'"
      />
    </div>
    {{ searchQuery }}
  </div>
</template>

<script setup>
import { onMounted, ref, watch, defineEmits } from "vue";

const props = defineProps({
  search: {
    type: String,
  },
});

const searchQuery = ref(); // Store the search keyword
const image = ref();
// Store the fetched image
const loading = ref(false); // Loading state
const emit = defineEmits(["imgLink"]);

watch(
  () => props.search,
  (newSearch) => {
    searchQuery.value = `${newSearch} City`; // Sync props.search with searchQuery
  }
);
// Watch for changes in searchQuery and trigger fetchImage

watch(searchQuery.value, async (newQuery, oldQuery) => {
  console.log("Search query changed from:", oldQuery, "to:", newQuery);

  if (newQuery) {
    try {
      await fetchImage(); // Call the async fetchImage function
      // Optionally reset searchQuery if needed:

      // Example of emitting the image URL if needed:
      // if (image.value.urls?.small) {
      //   emit("imgLink", image.value.urls.small);
      // }
    } catch (error) {
      console.error("Error fetching image:", error);
    }
  }
});

const accessKey = "ZWVkEFBgg7hxIxiajoHay61lTRO08SoeHdhdlKnSgqM";

// Function to fetch a single image
const fetchImage = async () => {
  if (!searchQuery.value.trim()) {
    // alert("Please enter a keyword to search.");
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
</script>

<style lang="scss" scoped></style>
// Watch for changes in searchQuery and trigger fetchImage watch(searchQuery,
async (newQuery, oldQuery) => { console.log("Search query changed from:",
oldQuery, "to:", newQuery); if (newQuery) { fetchImage(); // Fetch new image if
searchQuery changes //searchQuery.value = null; // if (image.value.urls?.small)
{ // emit("imgLink", image.value.urls.small); // Emit the image URL // } } });
