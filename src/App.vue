<template>
  <v-app>
    <v-main>
      <user-list-view/>
    </v-main>
  </v-app>
</template>

<script>
import UserListView from "@/views/UserListView";

export default {
  name: 'App',
  components: {
    UserListView,
  },
  data: () => ({}),
};
</script>
