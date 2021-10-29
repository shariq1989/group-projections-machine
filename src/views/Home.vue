<template>
  <div class="home">
    <GroupMachine />
  </div>
</template>

<script>
// @ is an alias to /src
import GroupMachine from "@/components/GroupMachine.vue";

export default {
  name: "Home",
  components: {
    GroupMachine,
  },
};
</script>
