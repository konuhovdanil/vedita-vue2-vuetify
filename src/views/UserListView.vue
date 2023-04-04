<template>
  <v-container fluid class="user-list-view">
    <v-row justify="center">
      <v-col md="8">
        <user-list/>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>

import UserList from "@/components/UserList";

export default {
  name: "UserListView",
  components: {
    UserList
  }
}
</script>

<style scoped lang="scss">
.user-list-view {
  background: #F5F6FB;
  min-height: 100vh;
  min-width: 100vw;
}
</style>