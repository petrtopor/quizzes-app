<template>
  <v-form v-model="valid" @submit.prevent>
    <v-stepper v-model="step" alt-labels flat class="custom-stepper">
      <v-stepper-header class="custom-stepper__header">
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
          <v-row>
            <v-col cols="9">
              <v-label>Заголовок опроса</v-label>
              <v-text-field hide-details variant="outlined" density="compact" v-model="form.title" />
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="4">
              <v-label>Время на выполнение опроса</v-label>
              <v-select hide-details variant="outlined" density="compact" v-model="form.execution_time" :items="execution_timeOptionsItems" />
            </v-col>
          </v-row>

        </v-stepper-window-item>
        <v-stepper-window-item :value="2">
          <template v-for="(question, questionIndex) in form.questions" :key="`question-key-${question.id}`">
            <v-row>
              <v-col cols="4">
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
              <v-col cols="9">
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
              <v-row class="mt-0">
                <v-col>
                  <v-btn @click="addAnswer(questionIndex)" flat class="add-answer-button">
                    + Добавить ответ
                  </v-btn>
                </v-col>
              </v-row>
            </template>
          </template>
          <v-row class="add-question-button">
            <v-col cols="12">
              <v-btn @click="addQuestionToForm" flat variant="outlined" color="blue" width="100%">
                + Добавить вопрос
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
            <v-btn @click="setFormLogic('Sequential')" :variant="form.logic.type === 'Dependent' ? 'outlined' : 'tonal'">По порядку</v-btn>
            <v-btn @click="setFormLogic('Dependent')" :variant="form.logic.type === 'Dependent' ? 'tonal' : 'outlined'">Логический</v-btn>
          </v-container>
          <template v-if="form.logic.type === 'Dependent'">
            <v-row v-for="(questionAssociated, questionAssociatedIndex) in questionsAssociated" :key="`question-associated-key-${questionAssociatedIndex}`">
              <v-col cols="9">
                <v-row>
                  <v-col>
                    <v-select
                      label="Выберите вопрос"
                      :items="form.questions.map(item => ({ title: item.text, value: item.id }))"
                      v-model="questionsAssociated[questionAssociatedIndex]"
                      density="compact"
                      variant="outlined"
                      append-icon="mdi-delete-outline"
                      @click:append="onQuestionAssociatedDeleteClick(questionAssociatedIndex)"
                    />
                  </v-col>
                </v-row>
                <template v-if="questionAssociated !== null">
                  <v-row
                    v-for="(answer, answerIndex) in form.questions.find(question => question.id === questionAssociated).answers"
                    :key="`answer-qai-${questionAssociatedIndex}-key-${answerIndex}`"
                  >
                    <v-col cols="4">
                      <v-text-field
                        hide-details
                        variant="outlined"
                        density="compact"
                        :value="answer.text"
                      />
                    </v-col>
                    <v-col cols="8">
                      <v-select
                        density="compact"
                        variant="outlined"
                        label="То переводить на"
                        placeholder="Выбрать вопрос"
                        :items="form.questions.map(item => ({ title: item.text, value: item.id }))"
                        v-model="form.questions[form.questions.findIndex(el => el.id === questionAssociated)].answers[answerIndex].next_question_id"
                      />
                    </v-col>
                  </v-row>
                </template>
                <v-row v-if="questionAssociated !== null">
                  <v-col>
                    <v-btn @click="addQuestionAssociated">
                      + Добавить вопрос
                    </v-btn>
                  </v-col>
                </v-row>
              </v-col>
            </v-row>
          </template>
          
        </v-stepper-window-item>
        <v-stepper-window-item :value="4">
          <v-row>
            <v-col>
              <div class="text-body-2">
                Прохождение опроса доступно:
              </div>
            </v-col>
          </v-row>
          <v-container class="pl-0">
            <v-btn @click="setConditionsAccess('Always')" :variant="form.conditions.access === 'Limited' ? 'outlined' : 'tonal'">Всегда</v-btn>
            <v-btn @click="setConditionsAccess('Limited')" :variant="form.conditions.access === 'Limited' ? 'tonal' : 'outlined'">Ограниченно</v-btn>
            <v-row v-if="form.conditions.access === 'Limited'">
              <v-col>
                <v-menu
                  v-model="startDateMenu"
                  :close-on-content-click="false"
                  transition="scale-transition"
                  offset-y
                  nudge-bottom="8"
                  min-width="auto"
                >
                  <template #activator="{ props }">
                    <v-text-field
                      density="compact"
                      variant="outlined"
                      :model-value="formatDate(startDate)"
                      label="Дата начала"
                      placeholder="Выберите дату"
                      append-inner-icon="mdi-chevron-down"
                      readonly
                      v-bind="props"
                    />
                  </template>
                  <v-date-picker
                    v-model="startDate"
                    @update:modelValue="startDateMenu = false"
                  />
                </v-menu>
              </v-col>
              <v-col>
                <v-menu
                  v-model="expirationDateMenu"
                  :close-on-content-click="false"
                  transition="scale-transition"
                  offset-y
                  nudge-bottom="8"
                  min-width="auto"
                >
                  <template #activator="{ props }">
                    <v-text-field
                      density="compact"
                      variant="outlined"
                      :model-value="formatDate(form.conditions.expiration_date)"
                      label="Дата окончания"
                      placeholder="Выберите дату"
                      append-inner-icon="mdi-chevron-down"
                      readonly
                      v-bind="props"
                    />
                  </template>
                  <v-date-picker
                    v-model="form.conditions.expiration_date"
                    @update:modelValue="expirationDateMenu = false"
                  />
                </v-menu>
              </v-col>
            </v-row>
          </v-container>
        </v-stepper-window-item>
        <v-stepper-window-item :value="5">
          <v-container>
            <v-row>
              <v-col>
                <div class="text-body-2">
                  Выберете участников, кому будет доступен опрос.
                </div>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-autocomplete
                  :loading="isUsersLoading"
                  :items="users.map(user => ({ title: user.full_name, value: user.id }))"
                  variant="outlined"
                  density="compact"
                  multiple
                  :model-balue="form.assignment"
                  @update:modelValue="onAssignmentUpdate"
                />
              </v-col>
            </v-row>
          </v-container>
        </v-stepper-window-item>
      </v-stepper-window>

      <v-container class="custom-container">
        <v-row>
          <v-col cols="4">
            <v-row>
              <v-col cols="5">
                <v-btn
                  @click="onClickPrev"
                  variant="outlined"
                  color="#2196F3"
                  :disabled="step < 2"
                >
                  Назад
                </v-btn>
              </v-col>
              <v-spacer></v-spacer>
              <v-col cols="5">
                <v-btn v-if="step < 5" type="submit" color="#2196F3" @click="onClickNext" :disabled="!isNextStepAvailable">
                  Далее
                </v-btn>
                <v-btn v-else type="submit" color="#2196F3" @click="submit">
                  {{ !$route.href.includes('create') && !$route.params?.id ? 'Coxpaнить' : 'Создать' }}
                </v-btn>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-container>
    </v-stepper>
    
  </v-form>
</template>

<script lang="ts" setup>
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
      value: 2147483646
    },
    {
      title: '24 часа',
      value: 2147483645
    },
    {
      title: '48 часов',
      value: 2147483644
    },
    {
      title: '72 часа',
      value: 2147483643
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

  const startDateMenu = ref(false)
  const expirationDateMenu = ref(false)
  const startDate = ref(null)

  const formatDate = (date) => {
    if (!date) return '';
    const options = { day: '2-digit', month: '2-digit', year: 'numeric' }
    return new Date(date).toLocaleDateString('ru-RU', options)
  }

  const form = reactive({
    title: '',
    execution_time: null,
    questions: [
      {
        id: 1,
        text: 'Вопрос 1',
        type: 'Single',
        answers: [
          {
            text: 'Вопрос 1 - Вариант 1',
            next_question_id: null
          }
        ]
      }
    ],
    logic: {
      type: 'Sequential'
    },
    conditions: {
      access: 'Always',
      expiration_date: null
    },
    assignment: [
      {
        id: null
      }
    ]
  })

  const setConditionsAccess = (conditionsAccess: string) => {
    form.conditions.access = conditionsAccess
  }

  const questionsAssociated = reactive([null])
  const addQuestionAssociated = () => {
    questionsAssociated.push(null)
  }
  const onQuestionAssociatedDeleteClick = (indexToRemove: number) => {
    questionsAssociated.splice(indexToRemove, 1)
  }

  const setFormLogic = (logicValue: string) => {
    form.logic.type = logicValue
  }

  const addAnswer = (questionIndex: number) => {
    form.questions[questionIndex].answers.push({
      text: `${form.questions[questionIndex].text} - Вариант ${form.questions[questionIndex].answers.length + 1}`,
      next_question_id: 0
    })
  }

  const addQuestionToForm = () => {
    const previousQuestion = form.questions[form.questions.length - 1]
    form.questions.push({
      id: previousQuestion.id + 1,
      text: `Вопрос ${previousQuestion.id + 1}`,
      type: 'Single',
      answers: [
        {
          text: `Вопрос ${previousQuestion.id + 1} - Вариант 1`,
          next_question_id: 0
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

  const { data: users, refresh: refreshUsers, pending: isUsersLoading } = await useFetch('http://127.0.0.1:8000/api/users/', {
    lazy: true,
    server: false
  })

  if(params?.id) {
    await refresh()
  }

  const onAssignmentUpdate = (items: number[]) => {
    form.assignment = items.map(item => ({ id: item }))
  }
  
  const submit = async () => {
    isLoading.value = true
    try {
      const response = await $fetch(`http://127.0.0.1:8000/api/quizzes${!params?.id ? '' : `/${params?.id}`}/`, {
        method: !params?.id ? 'POST' : 'PUT',
        body: form/* form.logic.type !== 'Sequential' ?
          form :
          {
            ...form,
            questions: form.questions.map(question => ({
              ...question,
              answers: question.answers.map(answer => ({
                text: answer.text
              }))
            }))
          } */
      })
    } catch(error) {
      console.error(error)
    } finally {
      isLoading.value = false
      back()
    }
  }

  const isNextStepAvailable = computed(() => {
    switch(step.value) {
      case 1: return true
      default: return true
    }
  })
</script>

<style lang="scss" scoped>
.radio-answer-option {
  :deep(.v-label) {
    width: 100%;
  }
}

.custom-stepper {
  background-color: #eff3f5;

  &__header {
    border: 1px solid #E0E0E0;
    border-radius: 8px;
    box-shadow: none;
    background-color: white;
  }

  :deep(.v-stepper-window) {
    border: solid 1px #E0E0E0;
    margin: 0;
    padding: 24px;
    border-radius: 8px;
    box-shadow: none;
    margin-top: 16px;
    background-color: white;
  }
}

.custom-container {
  background-color: #eff3f5;
}

.add-answer-button {
  color: #2196F3;
}

.add-question-button {
  // background-color: #eff3f5;
}
</style>