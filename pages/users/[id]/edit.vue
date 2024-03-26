<template>
  <h3>Редактирование пользователя</h3>
  <Suspense>
    <template #default>
      <user-create />
    </template>
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>

<script lang="ts" setup>
</script>
