<template>
  <h3>Подробности пользователя</h3>
  <Suspense>
    <template #default>
      <v-container class="bg-white">
        <v-row>
          <v-col cols="3">
            <!-- I am user with id: {{ $route.params.id }} -->
            avatar
          </v-col>
          <v-col cols="6">
            <v-row>
              <v-col cols="5">
                ФИО
              </v-col>
              <v-spacer></v-spacer>
              <v-col cols="6">
                Жмышенко Валерий Альбертович
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="5">
                Email
              </v-col>
              <v-spacer></v-spacer>
              <v-col cols="6">
                Жмышенко Валерий Альбертович
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="5">
                Телефон
              </v-col>
              <v-spacer></v-spacer>
              <v-col cols="6">
                +7 666 228 1488
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="5">
                Статус
              </v-col>
              <v-spacer></v-spacer>
              <v-col cols="6">
                BOP B ZAKOHE
              </v-col>
            </v-row>
          </v-col>
          <v-spacer></v-spacer>
          <v-col cols="1">
            menu
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
