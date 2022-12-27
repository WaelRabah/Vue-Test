<template>
  <b-container fluid="sm" class="centered">
    <div v-if="todos.length > 0" style="width: 100%">
      <b-form-group
        label="Title filter"
        label-for="filter-input"
        label-cols-sm="1"
        label-align-sm="left"
        label-size="sm"
        class="mb-3 mt-3"
      >
        <b-input-group size="sm">
          <b-form-input
            id="filter-input"
            v-model="filter"
            type="search"
            placeholder="Type to Search"
          ></b-form-input>

          <b-input-group-append>
            <b-button
              :disabled="!filter"
              @click="
                filter = '';
                filterByTitle();
              "
              >Clear</b-button
            >
          </b-input-group-append>
        </b-input-group>
      </b-form-group>
      <b-table
        striped
        hover
        :items="filtered"
        :fields="fields"
        @row-clicked="clickRow"
        :tbody-tr-class="rowClass"
      >
        <template #cell(completed)="row">
          <b-form-checkbox
            v-model="row.item.completed"
            @change="markCompleted(row.item)"
          >
          </b-form-checkbox>
        </template>
      </b-table>
    </div>
    <p v-else>Loading ...</p>
    <UserModal v-bind:user-id="userId" />
  </b-container>
</template>
<script>
import UserModal from "@/components/UserModal.vue";
export default {
  components: { UserModal },
  data() {
    return {
      todos: [],
      fields: [
        { key: "title", label: "Title", class: "d-none d-lg-table-cell" },
        {
          key: "completed",
          label: "Completed",
          class: "d-none d-lg-table-cell",
        },
      ],
      filtered: [],
      filter: "",
      userId: "",
    };
  },
  watch:{
    filter(){
      this.filterByTitle()
    }
  },
  methods: {
    filterByTitle() {
      if (this.filter === "") {
        this.filtered = [...this.todos];
      }
      this.filtered = this.todos.filter((item) =>
        item.title.toLowerCase().includes(this.filter.toLowerCase())
      );
    },
    markCompleted(item) {
      let originalItem = this.todos.find((item_) => item_.id == item.id);
      originalItem.completed = item.completed;
    },
    clickRow(item, index, event) {
      this.userId = item.userId;
      this.$bvModal.show("modal-multi-1");
    },
  },
  computed: {
    rowClass() {
      return "rowCursorPointer";
    },
  },
  mounted() {
    this.filtered = [...this.todos];
  },
  async asyncData({ $axios }) {
    const todos = await $axios.$get(`/todos`);
    return {
      todos,
    };
  },
};
</script>

<style>
.centered {
  justify-content: center;
  display: flex;
}

.rowCursorPointer {
  cursor: pointer;
}
</style>
