<template>
  <h3>Подробности пользователя</h3>
  <Suspense>
    <template #default>
      <v-container class="bg-white">
        <v-row>
          <v-col>
            I am user with id: {{ $route.params.id }}
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
</script>
