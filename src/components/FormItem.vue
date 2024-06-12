<script lang="ts">
import { defineComponent, ref } from 'vue'

interface ModelData {
  modelId: string
  pipeline_tag: string
  co2_eq_emissions: number | string
  safetensors: number
  downloads: number
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
        const response = await fetch(`https://huggingface.co/api/models/${modelName.value}`)
        if (!response.ok) {
          if (response.status == 401)
            throw new Error(
              'Incorrect request or model not found or access to the model is limited.'
            )
          throw new Error('An error occurred while fetching the model data.')
        }
        const model = await response.json()
        if (model) {
          modelData.value = {
            modelId: model.modelId,
            pipeline_tag: model?.pipeline_tag ?? 'Unknown',
            co2_eq_emissions:
              model.cardData?.co2_eq_emissions?.emissions ??
              model.cardData?.co2_eq_emissions ??
              'Unknown',
            safetensors: model?.safetensors?.total ?? 'Unknown',
            downloads: model?.downloads ?? 'Unknown'
          }
          errorMessage.value = ''
        }
      } catch (error) {
        errorMessage.value = error as string
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
    <h3 class="text-lg font-bold mt-4">Pipeline_tag:</h3>
    <p class="ml-2">{{ modelData.pipeline_tag }}</p>
    <h3 class="text-lg font-bold mt-4">CO2 Emissions:</h3>
    <p class="ml-2">{{ modelData.co2_eq_emissions }} g</p>
    <h3 class="text-lg font-bold mt-4">Safetensors:</h3>
    <p class="ml-2">{{ modelData.safetensors }}</p>
    <h3 class="text-lg font-bold mt-4">Downloads:</h3>
    <p class="ml-2">{{ modelData.downloads }}</p>
  </div>
  <div v-if="errorMessage" class="text-red-500 mt-10">{{ errorMessage }}</div>
</template>
