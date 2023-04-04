<template>
  <v-row>
    <v-col cols="12">
      <v-card-text class="pa-md-4 pa-1 pb-0 pb-md-0">
        <p class="purple--text text--darken-2 font-weight-bold ma-0">
          Пользователь №{{ user.id }}
        </p>
      </v-card-text>
    </v-col>
    <v-col cols="10">
      <v-card-text class="pa-md-4 pa-1 pt-0 pt-md-0">
        <p class="user-info__text">
          <span class="text-body-1 font-weight-thin">ФИО:</span>
          <span class="black--text">{{ user.fullName }}</span>
        </p>
        <p class="user-info__text">
          <span class="text-body-1 font-weight-thin">СНИЛС:</span>
          <span class="black--text">{{ user.snils }}</span>
        </p>
        <p class="user-info__text">
          <span class="text-body-1 font-weight-thin">Номер телефона:</span>
          <span class="black--text">{{ user.phone }}</span>
        </p>
        <p class="user-info__text">
          <span class="text-body-1 font-weight-thin">ИНН:</span>
          <span class="black--text">{{ user.inn }}</span>
        </p>
        <p class="user-info__text">
          <span class="text-body-1 font-weight-thin">Дата рождения:</span>
          <span class="black--text">{{ dateOfBirthdayFormatted }}</span>
        </p>
        <p class="user-info__text">
          <span class="text-body-1 font-weight-thin">Серия и номер паспорта:</span>
          <span class="black--text">{{ user.passportNumber }}</span>
        </p>
      </v-card-text>
    </v-col>

    <v-col cols="2">
      <v-card-actions class="align-start justify-end fill-height pt-0">
        <v-btn
          depressed
          density
          class="edit-button font-weight-thin mr-md-2"
          @click="$emit('update:editableUsersIds', user.id)"
        >
          <v-icon>
            {{ editIcon }}
          </v-icon>
        </v-btn>
      </v-card-actions>
    </v-col>
  </v-row>
</template>

<script>
import { mdiPencil } from '@mdi/js'

export default {
  name: "UserListItem",
  props: {
    user: {
      type: Object,
      required: true
    }
  },
  data: () => ({
    editIcon: mdiPencil,
    editableUsersIds: [],
    dateOfBirthdayFormatted: ''
  }),
  watch: {
    'user.dateOfBirthday': {
      handler(val) {
        this.dateOfBirthdayFormatted = this.formatDate(val)
      },
      immediate: true
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

<style scoped lang="scss">
.user-info {

  &__text {
    display: flex;
    flex-direction: row;
    gap: 8px;

    @media (max-width: 600px) {
      flex-direction: column;
    }
  }
}

.edit-button {
  min-width: 36px !important;
  color: rgba(0, 0, 0, 0.26);
}
</style>