<template>
  <v-row>
    <v-col cols="12">
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
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="item in users"
            :key="item.name"
          >
            <td>{{ item.full_name }}</td>
            <td>{{ item.email }}</td>
            <td>{{ item.phone }}</td>
            <td>{{ item.status }}</td>
          </tr>
        </tbody>
      </v-table>
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

  const { data: users }: { data: User[] } = await useFetch('http://127.0.0.1:8000/api/users/', {
    lazy: true,
    server: false
  })
</script>
