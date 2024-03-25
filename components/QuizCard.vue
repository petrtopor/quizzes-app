<template>
  <v-card
    class="pa-6 bg-white quiz-card"
    rounded="lg"
    height="96px"
    flat
  >
    <template #title>
      <div class="quiz-card__caption">
        {{ title }}
      </div>
    </template>
    <template #append>
      <v-menu>
        <template v-slot:activator="{ props }">
          <v-btn
            v-bind="props"
            flat
            slim
            icon="mdi-dots-vertical"
            size="small"
          />
        </template>
        <v-list>
          <v-list-item>
            <v-list-item-title>Редактировать</v-list-item-title>
          </v-list-item>
          <v-list-item>
            <v-dialog max-width="500">
              <template v-slot:activator="{ props: activatorProps }">
                <v-list-item-title v-bind="activatorProps">Удалить</v-list-item-title>
              </template>

              <template v-slot:default="{ isActive }">
                <v-card title="Dialog">
                  <v-card-text>
                    Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
                  </v-card-text>

                  <v-card-actions>
                    <v-spacer></v-spacer>

                    <v-btn
                      text="Close Dialog"
                      @click="isActive.value = false"
                    ></v-btn>
                  </v-card-actions>
                </v-card>
              </template>
            </v-dialog>
          </v-list-item>
        </v-list>
      </v-menu>
    </template>
  </v-card>
</template>

<script setup lang="ts">
  defineProps<{
    title: string
  }>()
</script>

<style lang="scss" scoped>
.quiz-card {
  border: 1px solid #E0E0E0 !important;

  &__caption {
    font-family: Roboto;
    font-size: 16px;
    font-weight: 500;
    line-height: 24px;
    letter-spacing: 0.5px;
    color: #2196F3;
    white-space: normal;
  }

  :deep(.v-card-item) {
    padding: 0;
  }
}
</style>
