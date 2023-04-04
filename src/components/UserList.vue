<template>
  <div>
    <v-alert
        v-if="!isValidForms"
        color="#FF5252"
        icon="mdi-alert"
        density="compact"
        class="white--text"
        dark
    >
      Ошибка
    </v-alert>

    <v-card class="pa-md-1 pa-0">
      <v-container>
        <v-row align="center">
          <v-col md="6" cols="10">
            <v-card-title class="text-h6 pa-1 pa-md-4 title">{{ title }}</v-card-title>
            <v-card-text class="text-body-2 font-weight-thin pa-md-4 pa-1 pt-0 pt-md-0">{{ subtitle }}</v-card-text>
          </v-col>
          <v-col md="6" cols="2" justify="center">
            <v-card-actions class="justify-end pa-md-4 pa-1">
              <v-btn v-if="mode === modes[0] && users.length === 0" class="purple darken-2 white--text header-button" @click="createNewField">
                <v-icon v-if="isMobileScreenSize">{{ plusIcon }}</v-icon>
                <span v-else>ДОБАВИТЬ</span>
              </v-btn>
              <v-btn v-else-if="mode !== modes[0]" class="purple darken-2 white--text header-button" :disabled="!isValidForms" @click="saveData">
                <v-icon v-if="isMobileScreenSize">{{ checkIcon }}</v-icon>
                <span v-else>СОХРАНИТЬ</span>
              </v-btn>
            </v-card-actions>
          </v-col>
        </v-row>
      </v-container>

      <v-container>
        <v-row v-for="user in users" :key="user.id"  class="user-info">
          <v-col md="12">
            <user-list-form
              v-if="editableUsersIds.length > 0 && editableUsersIds.includes(user.id)"
              :user="user"
              :users-length="users.length"
              @update:editableUsers="updateEditableUsers"
              @update:forms="updateForms"
            />
            <user-list-item v-else :user="user" @update:editableUsersIds="id => editableUsersIds.push(id)"/>
          </v-col>
        </v-row>

        <v-row v-for="user in newUsers" :key="user.id"  class="user-info">
          <v-col md="12">
            <user-list-form
              v-if="editableUsersIds.length > 0 && editableUsersIds.includes(user.id)"
              :user="user"
              :users-length="users.length"
              @update:editableUsers="updateEditableUsers"
              @update:forms="updateForms"
            />
            <user-list-item v-else :user="user" @update:editableUsersIds="id => editableUsersIds.push(id)"/>
          </v-col>
        </v-row>

        <v-row>
          <v-col v-if="users.length > 0 && editableUsersIds.length > 0" md="12" align="center">
            <v-btn
              :disabled="!isValidForms"
              color="purple"
              border="1px solid #7b1fa2"
              class="purple--text text--darken-2"
              prepend-icon
              outlined
              depressed
              @click="addEmptyFields"
            >
              <v-icon>
                {{ plusIcon }}
              </v-icon>
              ЕЩЕ
            </v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-card>
  </div>
</template>

<script>
import UserListForm from "@/components/UserListForm";
import json from '@/data/db.json'
import UserListItem from "@/components/UserListItem";
import { mdiCheck, mdiPlus } from "@mdi/js";

export default {
  name: "UserList",
  components: { UserListItem, UserListForm },
  mounted() {
    const users = JSON.parse(localStorage.getItem('users'))

    this.users = users?.length > 0 ? users : json.users
    localStorage.setItem('users', JSON.stringify(this.users))
  },
  data: () => ({
    users: [],
    mode: 'view',
    modes: ['view', 'add', 'edit'],
    editableUsersIds: [],
    editableUsers: [],
    newUsers: [],
    plusIcon: mdiPlus,
    checkIcon: mdiCheck,
    forms: [],
    isValidForms: true,
    changedUsersIds: []
  }),
  watch: {
    editableUsersIds: {
      handler(value) {
        if (value.length > 0) {
          this.mode = this.modes[2]
        }
      }
    }
  },
  computed: {
    title() {
      if (this.mode === this.modes[0]) return 'Список пользователей'

      return 'Добавление пользователей'
    },
    subtitle() {
      if (this.users.length === 0 && this.mode === this.modes[0]) return 'Не добавлены'

      if (this.users.length > 0 && this.mode === this.modes[0]) return 'Добавлено'

      return 'Заполните данные'
    },
    isMobileScreenSize() {
      return ['sm', 'xs'].includes(this.$vuetify.breakpoint.name)
    }
  },
  methods: {
    updateEditableUsers(user, hasChanges) {
      if (!this.editableUsers.some(item => item.id === user.id)) {
        this.editableUsers.push(user)
      }

      if (hasChanges && !this.changedUsersIds.includes(user.id)) {
        this.changedUsersIds.push(user.id)
      }

      if (!this.users.some(item => item.id === user.id) && !this.newUsers.some(item => item.id === user.id)) {
        this.newUsers.push(user)
      }
    },
    saveData() {
      if (this.editableUsers.length === 0) {
        this.clearEditableData()
        return
      }

      if (this.mode === this.modes[2]) {
        const filteredChangedUsers = this.editableUsers.filter(item => this.changedUsersIds.includes(item.id))

        filteredChangedUsers.forEach(item => {
          let oldUserId = this.users.findIndex(user => user.id === item.id)

          if (oldUserId !== -1) {
            this.users[oldUserId] = item;
          } else {
            this.users.push(item)
          }
        })

        console.log('Changed users:', filteredChangedUsers)
        localStorage.setItem('users', JSON.stringify(this.users))
        this.clearEditableData()
        return
      }

      this.users.push(...this.editableUsers)
      console.log('New users:', this.editableUsers)
      localStorage.setItem('users', JSON.stringify(this.users))
      this.clearEditableData()
    },
    clearEditableData() {
      this.editableUsers = []
      this.editableUsersIds = []
      this.changedUsersIds = []
      this.newUsers = []
      this.mode = this.modes[0]
    },
    addEmptyFields() {
      const id = this.users.length + this.editableUsersIds.length || 1

      this.newUsers.push({
        id,
        fullName: '',
        dateOfBirthday: "",
        passportNumber: '',
        snils: '',
        phone: '',
        inn: '',
      })

      this.changedUsersIds.push(id)
      this.editableUsersIds.push(id)
    },
    createNewField() {
      this.mode = this.modes[1]
      this.addEmptyFields()
    },
    updateForms(id, isValid) {
      const idx = this.forms.findIndex(item => item.id === id)
      const checkAllForms = () => this.forms.every(item => item.isValid)

      if (idx !== -1) {
        this.forms[idx] = {
          id,
          isValid
        }

        this.isValidForms = checkAllForms()
        return
      }

      this.forms.push({
        id,
        isValid
      })

      this.isValidForms = checkAllForms()
    }
  }
}
</script>

<style scoped lang="scss">
.user-info {
  position: relative;

  &:not(:first-child) {
    &::before {
      content: '';
      position: absolute;
      top: 0;
      left: 28px;
      right: 28px;
      height: 1px;
      background: #D2D2D2;
    }
  }
}

.header-button {
  min-width: 36px!important;
}

.title {
  word-break: keep-all;
}
</style>