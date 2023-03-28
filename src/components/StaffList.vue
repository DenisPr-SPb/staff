<template>
  <v-container class="v-col-7 mr-0" style="background: white">
    <v-text-field
        clearable
        label="Поиск сотрудника"
        variant="outlined"
        v-model="liveSearch"
        class="mt-3"
    ></v-text-field>
    <small class="text-grey">Например: Иванов Иван</small>

    <h2>Список сотрудников</h2>

    <v-container>
      <div class="mt-5">
        <v-sheet>
          <v-chip-group
              multiple
              selected-class="text-primary"
          >
            <v-chip
                v-for="item in tabItems"
                :key="item.id"
                :style="{'color': item.color}"
                v-model="selected"
                @click="filterBtn"
            >
              {{ item.name }}
            </v-chip>
          </v-chip-group>
        </v-sheet>
      </div>
    </v-container>

    <v-container>
      <div style="background: #E7F3FF"
                   class="mb-5"
                   v-for="staffItem in inputLiveSearch"
                   :key="staffItem.employee.inn">
        <p>
          <span class="text-blue font-weight-bold mr-3">{{ staffItem.employee.full_name }}</span>
          <span class="text-grey bg-white mr-3">ИНН {{ staffItem.employee.inn }}</span>
          <span class="bg-green mr-3" style="color: white">{{ staffItem.employee.type_contact }}</span>
          <span class="">{{ staffItem.employee.position }}</span>
        </p>
        <p>
          <img :src="staffItem.county.icon" class="mr-2" alt="icon">
          <span class="mr-3"> {{ staffItem.county.slug }} {{ staffItem.county.id }}</span>
          <span class="mr-3">{{ staffItem.employee.address }}</span>
          <span class="mr-3">Дата рождения: {{ staffItem.employee.date_birth }}</span>
          <span class="mr-3">Возраст: {{ staffItem.employee.age }}</span>
          <span class="mr-3">Пол: {{ staffItem.employee.gender }}</span>
        </p>
        <p>
          <span
              :style="{'background': staffItem.staff_tag.color}"
              style="color: white; border-radius: 3px; padding: 3px"
          >{{ staffItem.staff_tag.title }}</span>
        </p>
      </div>
      <div>
        <v-row class="align-center justify-center">
          <v-btn class="mt-5" style="max-width: 20%" @click="showMore">Показать еще</v-btn>
        </v-row>
      </div>
    </v-container>
  </v-container>
</template>

<script>

export default {
  name: 'staff-list',
  props: [
    'newEmployeeInfo',
    'newFilterInfo'
  ],
  data(){
    return {
      tabItems: [
        {id: 'all', name: 'Весь список', color: 'gray'},
        {id: 'problem', name: 'Проблемные', color: '#E2BD06'},
        {id: 'critical', name: 'Критические', color: '#E52E2E'},
        {id: 'error', name: 'Есть замечания', color: '#00B6ED'},
        {id: 'done', name: 'Выполнено', color: '#00AE5B'},
      ],
      staffList: [
        {
          staff_tag: {
            id: 0,
            title: 'qwe',
            slug: '',
            color: 'red'
          },
          county: {
            id: 111111,
            icon: require('../assets/rusFlag.svg'),
            title: 'Россия',
            slug: 'RU'
          },
          position: {
            id: 0,
            name: 'промышленный альпинист'
          },
          type_contact: {
            id: 0,
            title: 'Самозанятый',
            slug: 'СМЗ'
          },
          gender: {
            id: 0,
            title: 'мужской',
            slug: 'Муж.'
          },
          employee: {
            full_name: 'Name Name Name',
            inn: '1234567890',
            address: 'г.Санкт-Петербург',
            date_birth: '25.03.1990',
            age: 30,
            type_contact: 'СМЗ',
            type_contact_id: '',
            gender: 'мужской',
            gender_id: 0,
            country: 'Россия',
            country_id: 0,
            position: 'промышленный альпинист',
            position_id: 0,
            status: {
              tag_id: 0,
              tag: '',
              description: ''
            }
          }
        }
      ],
      filteredStaff: [],
      liveSearch: '',
      maxShow: 4,
      filter: false,
      selected: []

    }
  },
  watch: {
    newEmployeeInfo(newVal){
      this.addToStaffList(newVal)
    },
    newFilterInfo(newVal){
      this.onFilterObject(newVal)
    }
  },
  computed: {
    inputLiveSearch() {
      if (this.filter) {
        return this.filteredStaff.filter(item => item.employee.full_name.toLowerCase().startsWith(this.liveSearch.toLowerCase())).slice(0, this.maxShow)
      }

      if (this.liveSearch) {
        return this.staffList.filter(item => item.employee.full_name.toLowerCase().startsWith(this.liveSearch.toLowerCase())).slice(0, this.maxShow)
      }

      return this.staffList.slice(0, this.maxShow)
    },
  },
  methods: {
    addToStaffList(info) {
      try {
        if (info) {
          const employeeInfo = {
            employee: {
              status: {}
            },
            staff_tag: {},
            county: {},
            position: {},
            type_contact: {},
            gender: {}
          }

          const {
            gender,
            county,
            position,
            firstName,
            lastName,
            surname,
            contract,
            inn,
            address,
            date_birth,
            description_tag,
            description
          } = info


          employeeInfo.employee.full_name = firstName && lastName ?
              `${lastName} ${firstName} ${surname}` : ''
          employeeInfo.employee.inn = inn ? inn : ''
          employeeInfo.employee.address = address ? address : ''

          employeeInfo.employee.date_birth = date_birth ? date_birth : ''
          if (employeeInfo.employee.date_birth) {
            employeeInfo.employee.age = this.getCurrentAge(employeeInfo.employee.date_birth)
          }

          employeeInfo.employee.type_contact = contract ? contract : ''
          if (employeeInfo.employee.type_contact) {
            switch (employeeInfo.employee.type_contact) {
              case 'ТД':
                employeeInfo.employee.type_contact_id = '001'
                    break
              case 'СМЗ':
                employeeInfo.employee.type_contact_id = '002'
                break
              case 'ГПХ':
                employeeInfo.employee.type_contact_id = '003'
                break
              case 'Кандидат':
                employeeInfo.employee.type_contact_id = '004'
                break
            }
          }

          employeeInfo.employee.gender = gender ? gender : ''
          if (employeeInfo.employee.gender) {
            switch (employeeInfo.employee.gender.toLowerCase()) {
              case 'мужской':
                employeeInfo.employee.gender_id = 'xy'
                    break
              case 'женский':
                employeeInfo.employee.gender_id = 'xx'
                break
            }
          }

          employeeInfo.employee.country = county ? county : ''
          if (employeeInfo.employee.country) {
            switch (employeeInfo.employee.country.toLowerCase()) {
              case 'россия':
                employeeInfo.employee.country_id = '1'
                    break
              case 'таджикистан':
                employeeInfo.employee.country_id = '2'
                break
              case 'узбекистан':
                employeeInfo.employee.country_id = '3'
                break
            }
          }

          employeeInfo.employee.position = position ? position : ''
          if (employeeInfo.employee.position) {
            switch (employeeInfo.employee.position.toLowerCase()) {
              case 'промышленный альпинист':
                employeeInfo.employee.position_id = '1'
                    break
              case 'токарь':
                employeeInfo.employee.position_id = '2'
                break
              case 'пекарь':
                employeeInfo.employee.position_id = '3'
                break
            }
          }

          employeeInfo.employee.status.description = description_tag ? description_tag : ''

          if (employeeInfo.employee.status.description) {
            switch (employeeInfo.employee.status.description.toLowerCase()) {
              case 'проблемный':
                employeeInfo.employee.status.tag_id = 'problem'
                    break
              case 'критический':
                employeeInfo.employee.status.tag_id = 'critical'
                break
              case 'есть замечания':
                employeeInfo.employee.status.tag_id = 'error'
                break
              case 'выполнено':
                employeeInfo.employee.status.tag_id = 'done'
                break
            }
          }

          if (employeeInfo.employee) {
            employeeInfo.staff_tag.id = employeeInfo.employee.status.tag_id
            employeeInfo.staff_tag.title = description
            employeeInfo.staff_tag.slug = employeeInfo.employee.status.description
            if (employeeInfo.staff_tag.id) {
              switch (employeeInfo.staff_tag.id) {
                case 'problem':
                  employeeInfo.staff_tag.color = '#E2BD06'
                      break
                case 'critical':
                  employeeInfo.staff_tag.color = '#E52E2E'
                  break
                case 'error':
                  employeeInfo.staff_tag.color = '#00B6ED'
                  break
                case 'done':
                  employeeInfo.staff_tag.color = '#00AE5B'
                  break
              }
            }

            employeeInfo.county.id = employeeInfo.employee.country_id
            employeeInfo.county.title = county
            if (employeeInfo.county.title) {
              switch (employeeInfo.county.title.toLowerCase()) {
                case 'россия':
                  employeeInfo.county.slug = 'RU'
                  employeeInfo.county.icon = require('../assets/rusFlag.svg')
                  break
                case 'таджикистан':
                  employeeInfo.county.slug = 'TJ'
                  employeeInfo.county.icon = require('../assets/tjFlag.svg')
                  break
                case 'узбекистан':
                  employeeInfo.county.slug = 'UZ'
                  employeeInfo.county.icon = require('../assets/tjFlag.svg')
                  break
              }
            }

            employeeInfo.position.id = employeeInfo.employee.position_id
            employeeInfo.position.name = employeeInfo.employee.position

            employeeInfo.type_contact.id = employeeInfo.employee.type_contact_id
            employeeInfo.type_contact.slug = employeeInfo.employee.type_contact
            if (employeeInfo.type_contact.slug) {
              switch (employeeInfo.type_contact.slug) {
                case 'ТД':
                  employeeInfo.type_contact.title = 'Трудовой договор'
                      break
                case 'СМЗ':
                  employeeInfo.type_contact.title = 'Самозанятость'
                  break
                case 'ГПХ':
                  employeeInfo.type_contact.title = 'Гражданско-правовой характера'
                  break
                case 'Кандидат':
                  employeeInfo.type_contact.title = 'Кандидат на прием'
                  break
              }
            }

            employeeInfo.gender.id = employeeInfo.employee.gender_id
            employeeInfo.gender.title = employeeInfo.employee.gender
            if (employeeInfo.gender.title) {
              switch (employeeInfo.gender.title.toLowerCase()) {
                case 'мужской':
                  employeeInfo.gender.slug = 'муж'
                      break
                case 'женский':
                  employeeInfo.gender.slug = 'жен'
                  break
              }
            }
          }

          if (!employeeInfo.employee.full_name) {
            console.log('No data for add new employee', employeeInfo)
            return
          }

          this.staffList.unshift(employeeInfo)
        }
      } catch (e) {
        console.error('Problem with add to staff list ', e.message)
      }

      console.log('#DEBUG StaffList ', this.staffList)
    },

    getCurrentAge(date) {
      const d = date.split('.');
      if( typeof d[2] !== "undefined" ) {
        date = d[2]+'.'+d[1]+'.'+d[0];
        return ((new Date().getTime() - new Date(date)) / (24 * 3600 * 365.25 * 1000)) | 0;
      }
      return 0;
    },

    showMore(){
      return this.maxShow += 4
    },

    onFilterObject(obj) {
      this.filter = true
      const {country, gender, position, selected} = obj

      if (country || gender || position || selected.length) {
        const filterStaff = this.staffList.filter(item => {
          const employee = item.employee
          if (country && employee.country.toLowerCase() !== country.toLowerCase()) {
            return false
          }

          if (gender && employee.gender.toLowerCase() !== gender.toLowerCase()) {
            return false
          }

          if (position && employee.position.toLowerCase() !== position.toLowerCase()) {
            return false
          }

          if (selected.length && !selected.includes(employee.type_contact)) {
            return false
          }

          return true
        })

        return this.filteredStaff = filterStaff
      }

      this.filter = false
      return this.filteredStaff = []
    },

    filterBtn() {
      console.log(this.selected)
    }
  }
}
</script>