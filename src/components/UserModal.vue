<template>
  <b-modal id="modal-multi-1" size="lg" title="User" ok-only no-stacking>
    <p class="my-2">{{ user.name }}</p>
  </b-modal>
</template>

<script>
export default {
  props: ["userId"],
  watch: {
    userId() {
      this.$fetch();
    },
  },
  data() {
    return {
      user: {},
    };
  },
  async fetch() {
    this.user = await this.$axios.$get(`/users/${this.userId}`);
  },
  mounted() {},
};
</script>

<style></style>
