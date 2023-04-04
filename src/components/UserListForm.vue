<template>
  <v-form ref="userForm" class="pa-md-4">
    <v-row>
      <v-col cols="12" md="12" class="purple--text text--darken-2">Пользователь №{{ user.id }}</v-col>
      <v-col cols="12" md="4">
        <v-text-field
          v-model="editableUser.fullName"
          :rules="[rules.fullName]"
          label="ФИО"
          color="grey"
          hide-details="auto"
          outlined
          dense
          @keyup="validateForm"
        />
      </v-col>
      <v-col cols="12" md="4">
        <user-list-form-date-picker
          :user="user"
          :rules="rules"
          @update:userDateOfBirthday="updateUserDateOfBirthday"
        />
      </v-col>
      <v-col cols="12" md="4">
        <v-text-field
          v-model="editableUser.passportNumber"
          v-mask="'## ## ######'"
          :rules="[rules.passportNumber]"
          label="Серия и номер паспорта"
          color="grey"
          hide-details="auto"
          outlined
          dense
          @keyup="validateForm"
        />
      </v-col>
      <v-col cols="12" md="4">
        <v-text-field
          v-model="editableUser.snils"
          v-mask="'###-###-### ##'"
          :rules="[rules.snils]"
          label="СНИЛС"
          color="grey"
          hide-details="auto"
          outlined
          dense
          @keyup="validateForm"
        />
      </v-col>
      <v-col cols="12" md="4">
        <v-text-field
          v-model="editableUser.phone"
          v-mask="'+7 (###) ###-##-##'"
          :rules="[rules.phone]"
          label="Номер телефона"
          color="grey"
          hide-details="auto"
          outlined
          dense
          @keyup="validateForm"
        />
      </v-col>
      <v-col cols="12" md="4">
        <v-text-field
          v-model="editableUser.inn"
          v-mask="'############'"
          :rules="[rules.inn]"
          label="ИНН"
          color="grey"
          hide-details="auto"
          outlined
          dense
          @keyup="validateForm"
        />
      </v-col>
    </v-row>
  </v-form>
</template>

<script>
import { mdiPlus, mdiCalendar } from '@mdi/js'
import UserListFormDatePicker from "@/components/UserListFormDatePicker";

export default {
  name: "UserListForm",
  components: { UserListFormDatePicker },
  props: {
    usersLength: {
      type: Number,
      required: true,
      default: () => Number(Date.now())
    },
    user: {
      type: Object,
      default: null
    }
  },
  beforeMount() {
    this.editableUser = structuredClone(this.user)
  },
  mounted() {
    this.$nextTick(() => this.$emit('update:forms', this.user.id, this.$refs.userForm.validate()))
  },
  data: () => ({
    plusIcon: mdiPlus,
    calendarIcon: mdiCalendar,
    editableUser: null,
    showDatePicker: false,
    rules: {
      fullName: value => /[a-zA-Zа-яА-Я]{2,} [a-zA-Zа-яА-Я]{2,} [a-zA-Zа-яА-Я]{2,}/.test(value),
      passportNumber: value => /\d{2} \d{2} \d{6}/.test(value),
      snils: value => /\d{3}-\d{3}-\d{3} \d{2}/.test(value),
      inn: value => /\d{12}/.test(value),
      phone: value => /\+7 \(\d{3}\) \d{3}-\d{2}-\d{2}/.test(value),
      dateOfBirthday: value => /\d{2}-\d{2}-\d{4}/.test(value)
    },
    hasChanges: false
  }),
  methods: {
    addEmptyFields() {
      this.editableUsers = {
        id: this.usersLength + this.editableUsers.length,
        fullName: '',
        dateOfBirthday: "",
        passportNumber: '',
        snils: '',
        phone: '',
        inn: '',
      }
      this.$nextTick(() => this.$refs.userForm.validate())
    },
    validateForm() {
      this.showDatePicker = false
      this.hasChanges = true

      if (this.$refs.userForm.validate()) {
        this.$emit('update:editableUsers', this.editableUser, this.hasChanges)
      }

      this.$emit('update:forms', this.user.id, this.$refs.userForm.validate())
    },
    updateUserDateOfBirthday(date) {
      this.editableUser.dateOfBirthday = date
      this.$nextTick(() => this.validateForm())
    }
  }
}
</script>

<style scoped lang="scss">
.v-btn--outlined {
  border: 1px solid #7b1fa2;
}
</style>