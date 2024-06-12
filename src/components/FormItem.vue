<script lang="ts">
import { defineComponent, ref } from 'vue'

interface ModelData {
  modelId: string
  tags: string[]
  co2_eq_emissions: number | string
  cardData?: {
    co2_eq_emissions: number
  }
}

export default defineComponent({
  name: 'FormItem',
  setup() {
    const modelName = ref<string>('')
    const modelData = ref<ModelData | null>(null)
    const errorMessage = ref<string>('')

    const fetchModelData = async () => {
      try {
        const response = await fetch(
          `https://huggingface.co/api/models?cardData=true&search=${modelName.value}`
        )
        if (!response.ok) {
          throw new Error('Network response was not ok')
        }
        const models = await response.json()
        if (models.length > 0) {
          modelData.value = {
            modelId: models[0].modelId,
            tags: models[0].tags,
            co2_eq_emissions: models[0].cardData?.co2_eq_emissions ?? 'Unknown'
          }
          errorMessage.value = ''
        } else {
          errorMessage.value = 'Model not found.'
          modelData.value = null
        }
      } catch (error) {
        errorMessage.value = 'An error occurred while fetching the model data.'
        modelData.value = null
      }
    }

    return {
      modelName,
      modelData,
      errorMessage,
      fetchModelData
    }
  }
})
</script>

<template>
  <h1 class="text-3xl font-bold">Hugging Face Model CO2 Emission Checker</h1>
  <form @submit.prevent="fetchModelData" class="flex flex-col">
    <label for="modelName" class="text-lg font-bold mt-4">Model Name:</label>
    <input
      type="text"
      v-model="modelName"
      id="modelName"
      required
      class="w-full border border-blue-500 rounded-sm p-2"
    />
    <button type="submit" class="border rounded-md mt-4 py-2 px-4 font-semibold hover:bg-slate-300">
      Check Emission
    </button>
  </form>
  <div v-if="modelData" class="mt-10">
    <h2 class="text-2xl font-bold text-green-500">Model Details</h2>
    <h3 class="text-lg font-bold mt-6">Name:</h3>
    <p class="ml-2">{{ modelData.modelId }}</p>
    <h3 class="text-lg font-bold mt-4">Tags:</h3>
    <ul class="list-disc ml-6">
      <li v-for="tag in modelData.tags" :key="tag">{{ tag }}</li>
    </ul>
    <h3 class="text-lg font-bold mt-4">CO2 Emissions:</h3>
    <p class="ml-2">{{ modelData.co2_eq_emissions }} g</p>
  </div>
  <div v-if="errorMessage" class="text-red-500 mt-10">{{ errorMessage }}</div>
</template>
