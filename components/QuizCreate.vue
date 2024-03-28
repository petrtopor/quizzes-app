<template>
  <v-form v-model="valid" @submit.prevent>
    <v-stepper v-model="step">
      <v-stepper-header>
        <v-stepper-item :value="1">
          Параметры
        </v-stepper-item>
        <v-divider />
        <v-stepper-item :value="2">
          Вопросы
        </v-stepper-item>
        <v-divider />
        <v-stepper-item :value="3">
          Логика
        </v-stepper-item>
        <v-divider />
        <v-stepper-item :value="4">
          Условия
        </v-stepper-item>
        <v-divider />
        <v-stepper-item :value="5">
          Участники
        </v-stepper-item>
      </v-stepper-header>

      <v-stepper-window v-model="step">
        <v-stepper-window-item :value="1">
          <v-label>Заголовок опроса</v-label>
          <v-text-field hide-details variant="outlined" density="compact" v-model="form.title" />

          <v-label>Время на выполнение опроса</v-label>
          <v-select hide-details variant="outlined" density="compact" v-model="form.execution_time" :items="execution_timeOptionsItems" />
        </v-stepper-window-item>
        <v-stepper-window-item :value="2">
          <template v-for="(question, questionIndex) in form.questions" :key="`question-key-${question.id}`">
            <v-row>
              <v-col>
                <v-select
                  hide-details
                  variant="outlined"
                  density="compact"
                  v-model="form.questions[questionIndex].type"
                  :items="questionTypeOptionsItems"
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field
                  hide-details
                  variant="outlined"
                  density="compact"
                  placeholder="Введите название вопроса"
                  :label="`Вопрос ${questionIndex + 1}`"
                  v-model="form.questions[questionIndex].text"
                />
              </v-col>
            </v-row>
            <template v-if="question.type === 'Single'">
              <v-row>
                <v-col>
                  <v-radio-group>
                    <v-radio
                      v-for="(answer, answerIndex) in question.answers"
                      :key="`question-${questionIndex}-answer-${answerIndex}-key`"
                      :value="answer.text"
                      class="radio-answer-option"
                    >
                      <template #label>
                        <v-row>
                          <v-col cols="9">
                            <v-text-field
                              v-if="answerEditingNumber === answerIndex && questionEditingNumber === questionIndex"
                              hide-details
                              variant="outlined"
                              density="compact"
                              v-model="form.questions[questionIndex].answers[answerIndex].text"
                              @blur="resetEditing"
                            />
                            <div v-else class="text-body-2" @click="onAnswerLabelClick(answerIndex, questionIndex)">
                              {{ answer.text }}
                            </div>
                          </v-col>
                        </v-row>
                      </template>
                    </v-radio>
                  </v-radio-group>
                </v-col>
              </v-row>
              <v-row>
                <v-col>
                  <v-btn @click="addAnswer(questionIndex)">
                    Добавить ответ
                  </v-btn>
                </v-col>
              </v-row>
            </template>
          </template>
          <v-row>
            <v-col>
              <v-btn @click="addQuestionToForm">
                Добавить вопрос
              </v-btn>
            </v-col>
          </v-row>
        </v-stepper-window-item>
        <v-stepper-window-item :value="3">
          <v-row>
            <v-col>
              <div class="text-body-2">
                Порядок расположения вопросов в опросе:
              </div>
            </v-col>
          </v-row>
          <v-container class="pl-0">
            <v-btn @click="setFormLogic('Sequential')" :variant="form.logic === 'Dependent' ? 'outlined' : 'tonal'">По порядку</v-btn>
            <v-btn @click="setFormLogic('Dependent')" :variant="form.logic === 'Dependent' ? 'tonal' : 'outlined'">Логический</v-btn>
          </v-container>
          <v-row v-if="form.logic === 'Dependent'">
            <v-col>
              <v-select
                hide-details
                label="Выберите вопрос"
                variant="outlined"
                density="compact"
                v-model="questionSelected"
                :items="questionSelectOptions"
                append-icon="mdi-delete-outline"
                @click:append="questionSelectedReset"
              />
            </v-col>
          </v-row>
          <template v-if="form.logic === 'Dependent' && questionSelected !== null">
            <v-row
              v-for="answer in form.questions.find(question => question.id === questionSelected).answers"
              :key="`question-selected-key-id-${answer.id}`"
            >
              <v-col cols="5">
                <v-text-field
                  disabled
                  hide-details
                  variant="outlined"
                  density="compact"
                  :value="answer.text"
                  @blur="resetEditing"
                />
              </v-col>
              <v-col>
                <v-select
                  hide-details
                  label="То переводить на"
                  placeholder="Выбрать вопрос"
                  variant="outlined"
                  density="compact"
                  v-model="qqqqqq"
                  :items="form.questions.map(question => ({ title: question.text, value: question.id }))"
                />
              </v-col>
            </v-row>
          </template>
        </v-stepper-window-item>
        <v-stepper-window-item :value="4">
          // trololo
        </v-stepper-window-item>
        <v-stepper-window-item :value="5">
          // foobar
        </v-stepper-window-item>
      </v-stepper-window>
      
      <v-stepper-actions class="bg-app-background">
        <template #prev>
          <v-btn
            @click="onClickPrev"
            variant="outlined"
            color="#2196F3"
          >
            Назад
          </v-btn>
        </template>
        <template #next>
          <v-btn type="submit" color="#2196F3" @click="onClickNext">
            Далее
          </v-btn>
        </template>
      </v-stepper-actions>
    </v-stepper>
    
  </v-form>
</template>

<script lang="ts" setup>
  import { filter } from 'vue-input-facade'
  const valid = ref(true)
  const isLoading = ref(false)

  const step = ref(1)

  const answerEditingNumber = ref(null)
  const questionEditingNumber = ref(null)
  const onAnswerLabelClick = (answerIndex: number, questionIndex: number) => {
    answerEditingNumber.value = answerIndex
    questionEditingNumber.value = questionIndex
  }
  const resetEditing = () => {
    answerEditingNumber.value = null
    questionEditingNumber.value = null
  }

  const execution_timeOptionsItems = [
    {
      title: '30 минут',
      value: 2147483647
    },
    {
      title: '1 час',
      value: 2147483647
    },
    {
      title: '24 часа',
      value: 2147483647
    },
    {
      title: '48 часов',
      value: 2147483647
    },
    {
      title: '72 часа',
      value: 2147483647
    },
  ]

  const questionTypeOptionsItems = [
    {
      title: 'Один из списка',
      value: 'Single'
    },
    {
      title: 'Свободный ввод',
      value: 'Free'
    }
  ]

  const form = reactive({
    title: '',
    execution_time: 2147483647,
    questions: [
      {
        id: 0,
        text: '',
        type: 'Single',
        answers: [
          {
            text: 'Вариант 1',
            next_question_id: 1
          }
        ]
      }
    ],
    logic: 'Sequential'
  })

  const qqqqqq = ref(null)

  const questionSelectOptions = computed(() => (form.questions.map((question, questionIndex) => ({
    title: `${questionIndex + 1}. ${question.text}`,
    value: question.id
  }))))
  const questionSelected = ref(null)
  const questionSelectedReset = () => {
    questionSelected.value = null
  }

  const setFormLogic = (logicValue: string) => {
    form.logic = logicValue
  }

  const addAnswer = (questionIndex: number) => {
    form.questions[questionIndex].answers.push({
      text: `Вариант ${form.questions[questionIndex].answers.length + 1}`,
      next_question_id: 1
    })
  }

  const addQuestionToForm = () => {
    const previousQuestion = form.questions[form.questions.length - 1]
    form.questions.push({
      id: previousQuestion.id + 1,
      text: '',
      type: 'Single',
      answers: [
        {
          text: '',
          next_question_id: previousQuestion.next_question_id + 1
        }
      ]
    })
  }
  
  const onClickNext = () => {
    step.value ++
  }
  const onClickPrev = () => {
    step.value --
  }

  const { params } = useRoute()
  const { back } = useRouter()
  const { data, pending, error, refresh } = await useFetch(`http://127.0.0.1:8000/api/quizzes/${params?.id}/`, {
    immediate: false
  })

  if(params?.id) {
    await refresh()
  }
  
  const submit = async () => {
    isLoading.value = true
    try {
      const response = await $fetch(`http://127.0.0.1:8000/api/quizzes${!params?.id ? '' : `/${params?.id}`}/`, {
        method: !params?.id ? 'POST' : 'PUT',
        body: {
          "title": "string",
          "execution_time": 2147483647,
          "questions": [
            {
              "id": 0,
              "text": "string",
              "type": "Single",
              "answers": [
                {
                  "text": "string",
                  "next_question_id": 2147483647
                }
              ]
            }
          ],
          "logic": {
            "type": "Dependent"
          },
          "conditions": {
            "access": "Always",
            "expiration_date": "2024-03-27"
          },
          "assignment": [
            {
              "id": 3
            }
          ]
        }
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
.radio-answer-option {
  :deep(.v-label) {
    width: 100%;
  }
}
</style>