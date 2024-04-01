<template>
  <Suspense>
    <template #default>
      <quizzes-table />
    </template>
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>

<script lang="ts" setup>
</script>
