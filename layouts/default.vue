<template>
  <v-app>
    <v-main class="bg-app-background">
      <v-container class="fill-height">
        <v-responsive
          class="align-start fill-height mx-auto"
          max-width="900"
        >
          <v-row justify="space-between">
            <v-col cols="auto">
              <v-tabs slider-color="blue" class="custom-tabs" height="50px">
                <v-tab to="/quizzes" width="240" bg-color="white" class="custom-tabs__tab" rounded="lg">
                  Мои опросы
                </v-tab>
                <v-tab to="/users" width="240" bg-color="white" class="custom-tabs__tab" rounded="lg">
                  Пользователи
                </v-tab>
              </v-tabs>
            </v-col>
            <v-spacer></v-spacer>
            <v-col cols="auto">
              <v-btn
                v-if="!$route.href.includes('create') && !$route.params?.id"
                :to="$route.href.includes('quizzes') ? '/quizzes/create/' : '/users/create/'"
                rounded="lg"
                flat
                height="48px"
                color="blue"
              >
                Создать
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12">
              <NuxtPage />
            </v-col>
          </v-row>
        </v-responsive>
      </v-container>
    </v-main>
  </v-app>
</template>

<script lang="ts" setup>
  //
</script>

<style lang="scss" scoped>
.custom-tabs {
  width: 488px;

  :deep(.v-slide-group__content) {
    justify-content: space-between;
  }

  &__tab {
    background-color: white;
    border: 1px solid #E0E0E0;
  }
}
</style>
