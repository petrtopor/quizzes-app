<template>
  <v-row>
    <v-col cols="6" v-for="quiz in quizzes" :key="`title-key-${quiz.id}`">
      <quiz-card :title="quiz.title" :id="quiz.id" @deleted="refresh()" />
    </v-col>
  </v-row>
  <v-row>
    <v-col cols="12">
      <pagination />
    </v-col>
  </v-row>
</template>

<script lang="ts" setup>
  const { data: quizzes, refresh } = await useFetch('http://127.0.0.1:8000/api/quizzes/', {
    lazy: true,
    server: false
  })
</script>
