<template>
  <v-form v-model="valid" @submit.prevent>
    <v-container class="bg-white">
      <v-row>
        <v-col cols="12">
          <v-text-field
            v-model="name"
            :counter="10"
            :rules="nameRules"
            label="ФИО"
            required
            variant="outlined"
          />
        </v-col>
      </v-row>

      <v-row>
        <v-col cols="4">
          <v-text-field
            v-model="email"
            :rules="emailRules"
            label="E-mail"
            required
            variant="outlined"
          />
        </v-col>

        <v-col cols="4">
          <v-text-field
            type="tel"
            v-model="phone"
            :rules="[(v) => !!v || 'Телефон обязателен']"
            label="Телефон"
            required
            variant="outlined"
          />
        </v-col>

        <v-col>
          <v-select
            v-model="status"
            label="Select"
            :items="[{title: 'Активный', value: 'ACTIVE'}, {title: 'Заблокирован', value: 'BLOCKED'}]"
            variant="outlined"
          />
        </v-col>
      </v-row>
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
  interface User {
    email: string;
    full_name: string;
    id: number;
    phone: string;
    status: 'ACTIVE' | 'BLOCKED';
  }

  const valid = ref(false)
  const isLoading = ref(false)

  const { params } = useRoute()
  const { back } = useRouter()
  const { data, pending, error, refresh } = await useFetch(`http://127.0.0.1:8000/api/users/${params?.id}/`, {
    immediate: false
  })

  const name = ref('')
  const nameRules = [
    value => {
      if (value) return true

      return 'Name is required.'
    },
    value => {
      if (value?.length >= 10) return true

      return 'Полное имя должно быть длиннее 10 букв.'
    }
  ]

  const email = ref('')
  const emailRules = [
    value => {
      if (value) return true

      return 'E-mail обязателен.'
    },
    value => {
      if (/.+@.+\..+/.test(value)) return true

      return 'неправильный E-mail.'
    }
  ]

  const phone = ref('')

  const status = ref('ACTIVE')

  if(params?.id) {
    await refresh()
    name.value = data.value.full_name
    email.value = data.value.email
    phone.value = data.value.phone
    status.value = data.value.status
  }
  
  const submit = async () => {
    isLoading.value = true
    try {
      const response = await $fetch(`http://127.0.0.1:8000/api/users${!params?.id ? '' : `/${params?.id}`}/`, {
        method: !params?.id ? 'POST' : 'PUT',
        body: {
          status: status.value,
          full_name: name.value,
          email: email.value,
          phone: phone.value
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
