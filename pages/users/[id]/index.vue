<template>
  <Suspense>
    <template #default>
      <v-container class="bg-white rounded-lg">
        <v-row>
          <v-col cols="3">
            <v-icon
              icon="mdi-account-circle"
              size="192"
              class="custom-icon-style"
            />
          </v-col>
          <v-col cols="6">
            <v-row align="center">
              <v-col cols="2" class="font-weight-bold">
                ФИО
              </v-col>
              <v-spacer></v-spacer>
              <v-col cols="9">
                {{ full_name }}
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="2" class="font-weight-bold">
                Email
              </v-col>
              <v-spacer></v-spacer>
              <v-col cols="9">
                {{ email }}
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="2" class="font-weight-bold">
                Телефон
              </v-col>
              <v-spacer></v-spacer>
              <v-col cols="9">
                {{ phone }}
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="2" class="font-weight-bold">
                Статус
              </v-col>
              <v-spacer></v-spacer>
              <v-col cols="9">
                <v-chip label :color="status !== 'ACTIVE' ? 'error' : 'success'">
                  {{ status !== 'ACTIVE' ? 'ЗАБЛОКИРОВАН' : 'АКТИВЕН' }}
                </v-chip>
              </v-col>
            </v-row>
          </v-col>
          <v-spacer></v-spacer>
          <v-col cols="1">
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
                  <v-list-item-title @click="$router.push(`/users/${id}/edit/`)">
                    Редактировать
                  </v-list-item-title>
                </v-list-item>
                <v-list-item>
                  <v-dialog max-width="500">
                    <template v-slot:activator="{ props: activatorProps }">
                      <v-list-item-title v-bind="activatorProps">Удалить</v-list-item-title>
                    </template>

                    <template v-slot:default="{ isActive }">
                      <v-card title="Подтверждение">
                        <v-card-text>
                          Вы хотите удалить пользователя? Отменить действие будет невозможно.
                        </v-card-text>

                        <v-card-actions>
                          <v-spacer></v-spacer>

                          <v-btn
                            color="blue"
                            text="Удалить"
                            @click="onDeleteClick()"
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
const { params } = useRoute()
const { back } = useRouter()
const { data, refresh } = await useFetch(`http://127.0.0.1:8000/api/users/${params.id}/`)
const { id, full_name, email, phone, status } = data.value

const isLoading = ref(false)

const onDeleteClick = async () => {
  isLoading.value = true
  try {
    const response = await $fetch(`http://127.0.0.1:8000/api/users/${id}/`, {
      method: 'DELETE'
    })
  } catch(error) {
    console.error(error)
  } finally {
    isLoading.value = false
    back()
  }
}
</script>

<style lang="scss" scoped>
.custom-icon-style {
  background-color: #FFFFFF !important;
  color: #BBDEFB !important;
}
</style>
