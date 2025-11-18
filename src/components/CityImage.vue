<template>
  <div>
    <div v-if="loading">Loading...</div>

    <div v-if="image?.urls?.small" class="photo">
      <img
        :src="image.urls.small"
        :alt="image.alt_description || 'City Image'"
      />
    </div>

    <p>{{ searchQuery }}</p>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";

const props = defineProps({
  search: String,
});

const image = ref(null);
const loading = ref(false);
const accessKey = "ZWVkEFBgg7hxIxiajoHay61lTRO08SoeHdhdlKnSgqM";

const fetchImage = async () => {
  if (!props.search) return;

  loading.value = true;
  try {
    const res = await fetch(
      `https://api.unsplash.com/search/photos?query=${encodeURIComponent(
        props.search + " City"
      )}&client_id=${accessKey}`
    );
    const data = await res.json();
    image.value = data.results[0] || null;
  } catch (err) {
    console.error(err);
  } finally {
    loading.value = false;
  }
};

// Watch prop search فقط، بدون أي input آخر
watch(
  () => props.search,
  (newCity) => {
    if (newCity) {
      fetchImage();
    }
  },
  { immediate: true } // fetch أول مرة فور وجود اسم المدينة
);
</script>

<style lang="scss" scoped>
.search-bar {
  margin-bottom: 1rem;
  input {
    padding: 0.5rem;
    margin-right: 0.5rem;
  }
}
.photo img {
  max-width: 100%;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
}
</style>
