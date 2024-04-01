<template>
  <v-row>
    <v-col cols="12" class="custom-col">
      <v-table
        height="300px"
        fixed-header
      >
        <thead>
          <tr>
            <th class="text-left">
              ФИО
            </th>
            <th class="text-left">
              Email
            </th>
            <th class="text-left">
              Телефон
            </th>
            <th class="text-left">
              Статус
              <v-icon icon="mdi-filter" class="ml-3" />
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="item in users"
            :key="item.name"
            @click="$router.push(`/users/${item.id}`)"
          >
            <td>{{ item.full_name }}</td>
            <td>{{ item.email }}</td>
            <td>{{ item.phone }}</td>
            <td>
              <v-row align="center">
                <v-col cols="9">
                  <v-chip label :color="item.status !== 'ACTIVE' ? 'error' : 'success'">
                    {{ item.status !== 'ACTIVE' ? 'ЗАБЛОКИРОВАН' : 'АКТИВЕН' }}
                  </v-chip>
                </v-col>
                <v-spacer></v-spacer>
                <v-col>
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
                        <v-list-item-title @click="$router.push(`/users/${item.id}/edit/`)">
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
                                  @click="onDeleteClick(item.id)"
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
            </td>
          </tr>
        </tbody>
      </v-table>
      <pagination />
    </v-col>
  </v-row>
</template>

<script lang="ts" setup>
  interface User {
    email: string;
    full_name: string;
    id: number;
    phone: string;
    status: 'ACTIVE';
  }

  const { data: users, refresh } = await useFetch('http://127.0.0.1:8000/api/users/', {
    lazy: true,
    server: false
  })

  const isLoading = ref(false)

  const onDeleteClick = async (id: string) => {
    isLoading.value = true
    try {
      const response = await $fetch(`http://127.0.0.1:8000/api/users/${id}/`, {
        method: 'DELETE'
      })
    } catch(error) {
      console.error(error)
    } finally {
      isLoading.value = false
      refresh()
    }
  }
</script>

<style lang="scss" scoped>
.custom-col {
  border: 1px solid #e0e0e0;
  border-radius: 8px 8px 0 0;
}
</style>