<template>
  <Suspense>
    <template #default>
      <users-table />
    </template>
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>

<script lang="ts" setup>
</script>
