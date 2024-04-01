<template>
  <Suspense>
    <template #default>
      <v-row>
        <v-col>
          <v-container class="bg-white rounded-lg">
            <v-row>
              <v-col>
                <div class="quiz-title">{{ title }}</div>
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
                      <v-list-item-title @click="$router.push(`/quizzes/${id}/edit/`)">
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
                              Вы хотите удалить опрос? Отменить действие будет невозможно.
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
              </v-col>
            </v-row>
            <template v-if="questions?.length">
              <v-row class="mt-0">
                <v-col>
                  <v-divider></v-divider>
                </v-col>
              </v-row>
              <template v-for="(question, questionIndex) in questionsToShow" :key="question.id">
                <v-row :class="{'mb-3': question.type !== 'Single'}">
                  <v-col class="py-0">
                    <v-row>
                      <v-col>
                        <div class="question-name">
                          {{ currentQuestionIndex + 1}}. {{ question.text }}
                        </div>
                      </v-col>
                    </v-row>
                    <v-row>
                      <v-col>
                        <v-radio-group v-if="question.type === 'Single'">
                          <v-radio
                            v-for="(answer, answerIndex) in question.answers"
                            :key="`question-${questionIndex}-answer-${answerIndex}-key`"
                            :label="answer.text"
                            :value="answer.text"
                          />
                        </v-radio-group>
                        <v-text-field v-else hide-details variant="outlined" density="compact" v-model="freeTypedAnswer" />
                      </v-col>
                    </v-row>
                  </v-col>
                </v-row>
                <v-row
                  v-if="questionIndex < (questionsToShow.length - 1)"
                  class="mt-0"
                >
                  <v-col>
                    <v-divider></v-divider>
                  </v-col>
                </v-row>
              </template>
            </template>
          </v-container>
          <v-row class="mt-3" v-if="logic !== 'Sequential'">
            <v-col>
              <v-btn color="#2196F3">
                Отправить
              </v-btn>
            </v-col>
          </v-row>
          <v-row class="mt-3" v-else>
            <v-col v-if="currentQuestionIndex > 0" cols="2">
              <v-btn color="#2196F3" variant="outlined" @click="() => { currentQuestionIndex -- }">
                Назад
              </v-btn>
            </v-col>
            <v-col cols="2">
              <v-btn color="#2196F3" v-if="currentQuestionIndex < questions.length - 1" @click="() => { currentQuestionIndex ++ }">
                Далее
              </v-btn>
              <v-btn color="#2196F3" v-else>
                Отправить
              </v-btn>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </template>
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>

<script lang="ts" setup>
const { params } = useRoute()
const { back } = useRouter()
const { data, refresh } = await useFetch(`http://127.0.0.1:8000/api/quizzes/${params.id}/`)
const { id, title, questions, logic } = data.value

const isLoading = ref(false)

const freeTypedAnswer = ref('')

const currentQuestionIndex = ref(0)
const questionsToShow = computed(() => logic !== 'Sequential' ? questions : [questions[currentQuestionIndex.value]])

const onDeleteClick = async () => {
  isLoading.value = true
  try {
    const response = await $fetch(`http://127.0.0.1:8000/api/quizzes/${id}/`, {
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
.quiz-title {
  font-size: 20px !important;
  font-weight: 500 !important;
  line-height: 32px !important;
  letter-spacing: 0.25px !important;
}

.question-name {
  font-size: 16px;
  font-weight: 500;
  line-height: 24px;
  letter-spacing: 0.5px;
}
</style>
