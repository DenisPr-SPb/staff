<template>
  <v-container class="v-col-4 ml-5" style="background: white">
    <v-container style="min-width: 100%">
      <v-dialog
          v-model="dialog"
          persistent
          width="1024"
      >
        <template v-slot:activator="{ props }">
          <v-btn
              color="blue"
              v-bind="props"
              style="min-width: 100%"
          >
            +Добавить нового сотрудника
          </v-btn>
        </template>
        <v-card>
          <v-card-title>
            <span class="text-h5">Новый сотрудник</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-text-field
                      clearable
                      label="Фамилия"
                      v-model="newEmployee.lastName"
                      variant="outlined"/>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-text-field
                      clearable
                      label="Имя"
                      v-model="newEmployee.firstName"
                      variant="outlined"/>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-text-field
                      clearable
                      label="Отчество"
                      v-model="newEmployee.surname"
                      variant="outlined"/>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-text-field
                      clearable
                      label="Дата рождения DD.MM.YYYY"
                      v-model="newEmployee.date_birth"
                      variant="outlined"/>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-select
                      clearable
                      label="Пол"
                      :items="['женский', 'мужской']"
                      v-model="newEmployee.gender"
                      variant="outlined"
                  ></v-select>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-select
                      clearable
                      label="Гражданство"
                      :items="['Россия', 'Таджикистан', 'Узбекистан']"
                      v-model="newEmployee.county"
                      variant="outlined"
                  ></v-select>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-text-field
                      clearable
                      label="Город проживания"
                      v-model="newEmployee.address"
                      variant="outlined"/>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-select
                      clearable
                      label="Должность"
                      :items="['Промышленный альпинист', 'Токарь', 'Пекарь']"
                      v-model="newEmployee.position"
                      variant="outlined"
                  ></v-select>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-select
                      clearable
                      label="Тип договора"
                      :items="['ТД', 'ГПХ', 'СМЗ', 'Кандидат']"
                      v-model="newEmployee.contract"
                      variant="outlined"
                  ></v-select>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-text-field
                      clearable
                      label="ИНН"
                      v-model="newEmployee.inn"
                      variant="outlined"/>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-select
                      clearable
                      label="Статус"
                      :items="['Проблемный', 'Критический', 'Есть замечания', 'Выполнено']"
                      v-model="newEmployee.description_tag"
                      variant="outlined"
                  ></v-select>
                </v-col>
                <v-col
                    cols="12"
                    sm="6"
                    md="4"
                >
                  <v-text-field
                      clearable
                      label="Описание статуса"
                      v-model="newEmployee.description"
                      variant="outlined"/>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
                color="blue-darken-1"
                variant="text"
                @click="dialog = false"
            >
              Close
            </v-btn>
            <v-btn
                color="blue-darken-1"
                variant="text"
                @click="addEmployee"
            >
              Save
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-container>
        <h2 class="mb-5">Фильтр</h2>
        <v-row class="flex-wrap">
          <div class="flex-grow-0 mr-2" style="min-width: 49%">
            <div class="mb-2">Гражданство</div>
            <v-select style="min-width: 100%"
                      label="Все страны"
                      :items="['Россия', 'Таджикистан', 'Узбекистан']"
                      v-model="filter.country"
                      variant="solo"
            ></v-select>
          </div>
          <div class="flex-grow-0" style="min-width: 49%">
            <div class="mb-2">Пол</div>
            <v-select style="min-width: 100%"
                      label="Без разнницы"
                      :items="['Женский', 'Мужской']"
                      v-model="filter.gender"
                      variant="solo"
            ></v-select>
          </div>
          <div class="flex-grow-1" style="min-width: 100%">
            <div class="mb-2">Должность</div>
            <v-select style="min-width: 100%"
                      label="Все должности"
                      :items="['Промышленный альпинист', 'Токарь', 'Пекарь']"
                      v-model="filter.position"
                      variant="solo"
            ></v-select>
          </div>
        </v-row>
        <v-container>
          <div>Тип договора</div>
          <v-checkbox class="pa-0 ma-0"
              v-for="contract in contractValue"
              :key="contract"
              v-model="filter.selected"
              :label="contract"
              :value="contract"
          ></v-checkbox>
        </v-container>
      </v-container>
      <v-container>
        <v-row class="justify-center">
          <div class="mr-2" style="min-width: 48%">
            <v-btn
                style="min-width: 100%; background: #00AE5B; color: white"
                @click="addNewFilter"
            >
              Применить</v-btn>
          </div>
          <div style="min-width: 48%">
            <v-btn
              style="min-width: 100%; background: #84909B; color: white"
              @click="deleteFilter"
            >Отменить</v-btn>
          </div>
        </v-row>
      </v-container>
    </v-container>
  </v-container>
</template>

<script>

export default {
  name: 'staff-filter',
  props: [
    'getEmployeeInfo',
    'getFilterInfo'
  ],
  data() {
    return {
      contractValue:['ТД', 'ГПХ', 'СМЗ', 'Кандидат'],
      dialog: false,
      newEmployee: {
        id: '',
        gender: '',
        county: '',
        position: '',
        firstName: '',
        lastName: '',
        surname: '',
        contract: '',
        inn: '',
        address: '',
        date_birth: '',
        description_tag: '',
        description: '',
      },
      filter: {
        country: '',
        gender: '',
        position: '',
        selected: [],
      },
    }
  },
  methods: {
    addEmployee() {
      this.newEmployee.id = Date.now()

      this.getEmployeeInfo({
        newEmployee: this.newEmployee
      })

      this.clearDialogWindow()
      this.dialog = false
    },

    clearDialogWindow() {
      this.newEmployee = {
        id: '',
        gender: '',
        county: '',
        position: '',
        firstName: '',
        lastName: '',
        surname: '',
        contract: '',
        inn: '',
        address: '',
        date_birth: ''
      }
    },

    addNewFilter(){
      console.log('#DEBUG filter ', this.filter)
      if (this.filter.country || this.filter.position || this.filter.gender || this.filter.selected.length){
        this.getFilterInfo({
          newFilter: this.filter
        })
        this.cleanFilter()
      }
    },

    deleteFilter(){
        this.cleanFilter()

        this.getFilterInfo({
          newFilter: this.filter
        })
    },

    cleanFilter() {
      this.filter = {
        country: '',
        gender: '',
        position: '',
        selected: [],
      }
    }
  }
}
</script>