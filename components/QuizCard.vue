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
      <v-menu v-model="isMenuOpen">
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
            <v-list-item-title @click="$router.push(`/quizzes/${id}/edit/`)">
              Редактировать
            </v-list-item-title>
          </v-list-item>
          <v-list-item>
            <v-list-item-title @click="$router.push(`/quizzes/${id}/`)">
              Просмотр
            </v-list-item-title>
          </v-list-item>
          <v-list-item>
            <v-dialog v-model="isDeleteDialogVisible" max-width="500">
              <template v-slot:activator="{ props: activatorProps }">
                <v-list-item-title v-bind="activatorProps">Удалить</v-list-item-title>
              </template>

              <template v-slot:default="{ isActive }">
                <v-card title="Подтверждение">
                  <v-card-text>
                    Вы хотите удалить просмотр? Отменить действие будет невозможно.
                  </v-card-text>

                  <v-card-actions>
                    <v-spacer></v-spacer>

                    <v-btn
                      color="blue"
                      text="Удалить"
                      @click="onDeleteClick(id)"
                      :loading="isLoading"
                    />
                    <v-btn
                      text="Отменить"
                      @click="isActive.value = false"
                    />
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
    id: number
  }>()

  const emit = defineEmits(['deleted'])

  const isLoading = ref(false)
  const isDeleteDialogVisible = ref(false)
  const isMenuOpen = ref(false)

  const onDeleteClick = async (id: number) => {
    isLoading.value = true
    try {
      const response = await $fetch(`http://127.0.0.1:8000/api/quizzes/${id}/`, {
        method: 'DELETE'
      })
    } catch(error) {
      console.error(error)
    } finally {
      isLoading.value = false
      isDeleteDialogVisible.value = false
      isMenuOpen.value = false
      emit('deleted')
    }
  }
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
