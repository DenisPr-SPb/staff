<template>
  <div>
    <v-row>
      <staff-list
        :newEmployeeInfo="newEmployeeInfo"
        :newFilterInfo="newFilterInfo"
      />
      <staff-filter
        :getEmployeeInfo="getEmployeeInfo"
        :getFilterInfo="getFilterInfo"
      />
    </v-row>
  </div>
</template>

<script>

import StaffList from "@/components/StaffList.vue";
import StaffFilter from "@/components/StaffFilter.vue";

export default {
  name: 'staff-page',
  components: {StaffFilter, StaffList},
  props: [
    'newEmployee', 'newFilter'
  ],
  data() {
    return{
      newEmployeeInfo: '',
      newFilterInfo: ''
    }
  },
  methods: {
    getEmployeeInfo(data) {
      this.newEmployeeInfo = data.newEmployee
    },
    getFilterInfo(data) {
      this.newFilterInfo = data.newFilter
    }
  }
}
</script>
