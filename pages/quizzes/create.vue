<template>
  <h3>Добавление квиза</h3>
  <Suspense>
    <template #default>
      <quiz-create />
    </template>
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>

<script lang="ts" setup>
</script>
