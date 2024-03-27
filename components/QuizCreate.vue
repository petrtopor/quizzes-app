<template>
  <v-form v-model="valid" @submit.prevent>
    <v-container class="bg-white">
      ololo
    </v-container>
    <v-container>
      <v-row>
        <v-col cols="auto">
          <v-btn
            variant="outlined"
            color="#2196F3"
            @click="back()"
          >
            Отменить
          </v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn type="submit" @click="submit" :disabled="!valid" :loading="isLoading" color="#2196F3">
            {{ !$route.params?.id ? 'Добавить' : 'Сохранить' }}
          </v-btn>
        </v-col>
      </v-row>
    </v-container>
    
  </v-form>
</template>

<script lang="ts" setup>
  import { filter } from 'vue-input-facade'
  const valid = ref(true)
  const isLoading = ref(false)

  const { params } = useRoute()
  const { back } = useRouter()
  const { data, pending, error, refresh } = await useFetch(`http://127.0.0.1:8000/api/quizzes/${params?.id}/`, {
    immediate: false
  })

  if(params?.id) {
    await refresh()
    // 
  }
  
  const submit = async () => {
    isLoading.value = true
    try {
      const response = await $fetch(`http://127.0.0.1:8000/api/quizzes${!params?.id ? '' : `/${params?.id}`}/`, {
        method: !params?.id ? 'POST' : 'PUT',
        body: {
          "title": "string",
          "execution_time": 2147483647,
          "questions": [
            {
              "id": 0,
              "text": "string",
              "type": "Single",
              "answers": [
                {
                  "text": "string",
                  "next_question_id": 2147483647
                }
              ]
            }
          ],
          "logic": {
            "type": "Dependent"
          },
          "conditions": {
            "access": "Always",
            "expiration_date": "2024-03-27"
          },
          "assignment": [
            {
              "id": 3
            }
          ]
        }
      })
    } catch(error) {
      console.error(error)
    } finally {
      isLoading.value = false
      back()
    }
  }
</script>
