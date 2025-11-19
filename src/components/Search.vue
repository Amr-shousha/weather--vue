<template>
  <div></div>
</template>

<script setup>
import { ref } from "vue";
const searresults = ref([]);
const searchquery = ref("");

const search = async (query) => {
  const encodedquery = encodeURIComponent(query);
  const endpoint = `https://en.wikipedia.org/w/api.php?action=query&list=search&prop=info&inprop=url&utf8=&format=json&origin=*&srlimit=10&srsearch=${encodedQuery}`;
  try {
    const response = await fetch(endpoint);
    const data = await response.json();
    searresults.value = data.query.search;
  } catch (err) {
    alert(`Error: ${err}`);
  }
};

const submitsearch = () => {
  if (searchquery.value.trim() !== "") {
    search(searchquery.value);
  } else {
    searresults.value = [];
    //error.value = "Please enter a valid search term.";
  }
};
</script>

<style lang="scss" scoped></style>
