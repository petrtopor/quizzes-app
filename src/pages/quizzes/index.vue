<template>
  <!-- <HelloWorld /> -->
  <h3>Quizzes page</h3>
  <v-row>
    <v-col cols="6" v-for="(title, titleIndex) in titles" :key="`title-key-${titleIndex}`">
      <quiz-card :title="title" />
    </v-col>
  </v-row>
</template>

<script lang="ts" setup>
  const titles = ref([
    'Опрос пользователей мобильного приложения ...',
    'Опрос пользователей мобильного приложения ...',
    'Опрос пользователей мобильного приложения ...',
    'Опрос пользователей мобильного приложения ...',
    'Опрос пользователей мобильного приложения ...'
  ])
</script>
