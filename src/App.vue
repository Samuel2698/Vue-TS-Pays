<script setup lang="ts">
import { onMounted, ref, watch } from 'vue'
import { Country } from './models/country.model'
import Header from './components/Header.vue'
import CountryList from './components/CountryList.vue'
import axiosClient from './utils/axios'

const countries = ref<Country[]>([])
const search = ref('')
const filteredCountries = ref<Country[]>([])
const page = ref(1)
const itemsPerPage = ref(12)
const paginatedCountries = ref<Country[]>([])
const totalItems = ref(0)

const fetchCountries = async () => {
  try {
    const { data } = await axiosClient.get('/all')
    countries.value = data
    totalItems.value = countries.value.length
  } catch (err) {
    console.log(err)
  }
}

const filterCountries = () => {
  filteredCountries.value = countries.value.filter((country) =>
    country.name.common.toLowerCase().includes(search.value.toLowerCase())
  )
}

const sliceCountries = (currentCountries: Country[]) => {
  const start = (page.value - 1) * itemsPerPage.value
  const end = page.value * itemsPerPage.value
  paginatedCountries.value = currentCountries.slice(start, end)
}

const changePage = (newPage: number) => {
  page.value = newPage
}

const preventSpaceKey = (event: KeyboardEvent) => {
  if (event.key === ' ') {
    event.preventDefault()
  }
}

onMounted(() => {
  fetchCountries()
  sliceCountries(countries.value)
})

watch([countries, page, filteredCountries], () => {
  sliceCountries(
    filteredCountries.value.length <= 0 && search.value === ''
      ? countries.value
      : filteredCountries.value
  )
})
</script>

<template>
  <Header />
  <div class="container max-w-screen-lg mx-auto px-6">
    <div class="mb-8 flex justify-center items-center">
      <input
        type="text"
        placeholder="France, China, Brazil.."
        class="border border-gray-300 rounded w-[50%] p-1"
        v-model="search"
        @input="filterCountries"
        @keydown.prevent.space="preventSpaceKey"
      />
    </div>
    <div class="mb-8 flex justify-center space-x-6">
      <button
        :disabled="page <= 1"
        :class="{ 'opacity-50': page <= 1 }"
        @click="() => changePage(page - 1)"
        class="cursor-pointer border border-gray-300 rounded px-2 py-0.5 hover:bg-gray-200"
      >
        Previous
      </button>
      <button
        :disabled="page >= totalItems / itemsPerPage"
        :class="{ 'opacity-50': page >= totalItems / itemsPerPage }"
        @click="() => changePage(page + 1)"
        class="border border-gray-300 rounded px-2 py-0.5 hover:bg-gray-200"
      >
        Next
      </button>
    </div>

    <CountryList
      v-if="filteredCountries.length > 0 || search === ''"
      :countries="paginatedCountries"
    ></CountryList>

    <div v-if="filteredCountries.length <= 0 && search !== ''">
      <p class="text-center text-xl">Il n'y a pas de résultats.</p>
    </div>
  </div>
</template>
