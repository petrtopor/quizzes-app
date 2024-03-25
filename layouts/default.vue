<template>
  <v-app>
    <h3>Default layout</h3>
    <v-main class="bg-app-background">
      <v-container class="fill-height">
        <v-responsive
          class="align-start fill-height mx-auto"
          max-width="900"
        >
          <v-row>
            <v-col cols="12">
              <v-tabs>
                <v-tab to="quizzes">
                  Quizzes
                </v-tab>
                <v-tab to="users">
                  Users
                </v-tab>
              </v-tabs>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12">
              <router-view />
            </v-col>
          </v-row>
        </v-responsive>
      </v-container>
    </v-main>

    <AppFooter />
  </v-app>
</template>

<script lang="ts" setup>
  //
</script>
