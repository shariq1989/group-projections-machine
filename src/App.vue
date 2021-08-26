<template>
  <v-app>
    <v-main>
      <GroupMachine />
    </v-main>
  </v-app>
</template>

<script>
import GroupMachine from "./components/GroupMachine";

export default {
  name: "App",

  components: {
    GroupMachine,
  },

  data: () => ({
    //
  }),
};
</script>
