<script setup lang="ts">
import { defineProps } from 'vue'
import { Country } from '../models/country.model'

interface Props {
  country: Country
}

defineProps<Props>()
</script>

<template>
  <div class="h-56 sm:h-36">
    <img
      class="h-56 sm:h-36 w-full object-cover"
      :src="country.flags.png"
      :alt="country.flags.alt"
    />
  </div>
  <h3 class="mt-1 text-xl font-bold text-gray-500 truncate">
    {{ country.name.common }}
  </h3>
  <p class="mt-2 text-sm text-gray-500">
    Capital:
    <span v-for="capital in country.capital" class="font-bold"
      >{{ capital }}
      <template> </template>
    </span>
  </p>
  <p class="mt-1 text-sm text-gray-500">
    Population:
    <span class="font-bold">{{ country.population?.toLocaleString() }}</span>
  </p>
  <p class="mt-1 text-sm text-gray-500">
    Région:
    <span class="font-bold">{{ country.region }}</span>
  </p>
</template>
