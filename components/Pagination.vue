<template>
<v-row class="ma-0 bg-white cusom-row">
  <v-col>
    <v-row>
      <v-col cols="6" align-self="center">
        Номер страницы
      </v-col>
      <v-col align-self="center">
        <v-select
          hide-details
          variant="plain"
          density="compact"
          :items="[{ title: '1', value: 1 }]"
          class="custom-select"
        />
      </v-col>
      <v-col cols="4" align-self="center">
        10 из 17
      </v-col>
    </v-row>
  </v-col>
  <v-spacer></v-spacer>
  <v-col cols="2">
    <v-row>
      <v-col align-self="center">
        <v-btn
          flat
          slim
          icon="mdi-chevron-left"
          size="small"
        />
      </v-col>
      <v-spacer></v-spacer>
      <v-col align-self="center">
        <v-btn
          flat
          slim
          icon="mdi-chevron-right"
          size="small"
        />
      </v-col>
    </v-row>
  </v-col>
</v-row>
</template>

<script setup lang="ts">
  
</script>

<style scoped lang="scss">
.cusom-row {
  border: 1px solid #e0e0e0;
  border-radius: 0 0 8px 8px;
}
.custom-select {
  border-bottom: 1px solid #0000006B!important;
}
</style>
