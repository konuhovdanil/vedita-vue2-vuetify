<template>
<div>
  <v-menu
      v-model="showDatePicker"
      :close-on-content-click="true"
      :nudge-right="40"
      transition="scale-transition"
      offset-y
      min-width="290px"
  >
    <template v-slot:activator="{ on, attrs }">
      <v-text-field
          v-model="dateOfBirthdayFormatted"
          :rules="[rules.dateOfBirthday]"
          label="Дата рождения"
          color="grey"
          hide-details="auto"
          v-bind="attrs"
          v-on="on"
          readonly
          outlined
          dense
      >
        <template v-slot:prepend-inner>
          <v-icon>{{ calendarIcon }}</v-icon>
        </template>

        {{ user }}
      </v-text-field>
    </template>
    <v-date-picker
        v-model="date"
        header-color="purple darken-2"
        color="purple darken-2"
        locale="ru-ru"
        @input="$emit('update:userDateOfBirthday', date)"
    ></v-date-picker>
  </v-menu>
</div>

</template>

<script>
import { mdiCalendar } from '@mdi/js'

export default {
  name: "UserListFormDatePicker",
  props: {
    user: {
      type: Object,
      required: true,
      default: () => ({})
    },
    rules: Object
  },
  mounted() {
    this.date = this.user.dateOfBirthday
  },
  data: () => ({
    showDatePicker: false,
    calendarIcon: mdiCalendar,
    date: '',
    dateOfBirthdayFormatted: ''
  }),
  watch: {
    date: {
      handler(val) {
        this.dateOfBirthdayFormatted = this.formatDate(val)
      }
    }
  },
  methods: {
    formatDate (date) {
      if (!date) return ''

      const [year, month, day] = date.split('-')
      return `${day}-${month}-${year}`
    },
  }
}
</script>

<style scoped>

</style>