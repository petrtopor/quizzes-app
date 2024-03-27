<template>
  <Suspense>
    <template #default>
      <v-container class="bg-white rounded-lg">
        <v-row>
          <v-col cols="6">
            {{ id }}
          </v-col>
          <v-col cols="6">
            {{ title }}
          </v-col>
        </v-row>
      </v-container>
    </template>
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>

<script lang="ts" setup>
const { params } = useRoute()
const { back } = useRouter()
const { data, refresh } = await useFetch(`http://127.0.0.1:8000/api/quizzes/${params.id}/`)
const { id, title } = data.value

const isLoading = ref(false)

const onDeleteClick = async () => {
  isLoading.value = true
  try {
    const response = await $fetch(`http://127.0.0.1:8000/api/quizzes/${id}/`, {
      method: 'DELETE'
    })
  } catch(error) {
    console.error(error)
  } finally {
    isLoading.value = false
    back()
  }
}
</script>

<style lang="scss" scoped>
.custom-icon-style {
  background-color: #FFFFFF !important;
  color: #BBDEFB !important;
}
</style>
