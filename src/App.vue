<script setup>
import { computed, ref, watch } from 'vue'
import { useDebounceFn } from "@vueuse/core";

const searchTerm = ref('')
const products = ref([])
const loading = ref(false)
const noResults = ref(false)

const findProducts = useDebounceFn(async term => {
  noResults.value = false

  if (term.length) {
    try {
      loading.value = true
      const response = await fetch(`https://dummyjson.com/products/search?q=${term}`)
      const result = await response.json()
      products.value = result.products
      noResults.value = !products.value.length
      loading.value = false
    } catch (error) {
      loading.value = false
      alert('Whoops, something went wrong!')
      searchTerm.value = ''
    }
  } else {
    products.value = []
  }
}, 300)

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <p v-if="loading">Loading...</p>
    <ul v-else class="list-disc">
      <li v-for="product in products" :key="product.id">{{ product.title }}</li>
    </ul>
    <p v-if="noResults">No results found.</p>
  </div>
</template>
