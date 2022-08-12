<template>
  <div>
    <v-form ref="form" v-model="valid" lazy-validation>
      <p class="white--text">{{ error }}</p>

      <v-text-field
        v-model="todo.name"
        :counter="50"
        :rules="nameRules"
        label="Name"
        required
      ></v-text-field>

      <v-text-field
        v-model="todo.description"
        label="Desc"
        required
      ></v-text-field>
      <v-btn
        :disabled="!valid"
        color="success"
        class="mr-4"
        @click="createTodo()"
      >
        SUBMIT
      </v-btn>

      <v-btn color="error" class="mr-4" @click="reset"> Reset Form </v-btn>
    </v-form>

    <v-data-table
      :headers="headers"
      :items="todos"
      :items-per-page="10"
      class="elevation-1 mt-3"
    >
      <template v-slot:item.created="{ item }">
        {{ setDate(item.created_at) }}
      </template>
      <template v-slot:item.actions="{ item }">
        <div class="d-inline-flex">
          <div class="text-center">
            <v-dialog v-model="dialog[item.id]" width="500">
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color="green"
                  dark
                  v-bind="attrs"
                  v-on="on"
                  class="mr-2"
                  @click="setData(item)"
                >
                  EDIT {{ item.id }}
                </v-btn>
              </template>

              <v-card>
                <v-card-title class="text-h5 grey lighten-2">
                  EDIT {{ item.id }}
                </v-card-title>

                <v-card-text>
                  <v-text-field
                    v-model="uptodo.name"
                    :counter="50"
                    label="Name"
                    required
                  ></v-text-field>

                  <v-text-field
                    v-model="uptodo.description"
                    label="Desc"
                    required
                  ></v-text-field>
                </v-card-text>

                <v-divider></v-divider>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="primary" text @click="updateTodo(item.id)"
                    >UPDATE
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </div>
          <v-btn color="error" class="mr-4" @click="deleteTodo(item.id)">
            DELETE</v-btn
          >
        </div>
      </template>
    </v-data-table>
  </div>
</template>

<script>
import axios from "axios";
import Table from "../components/Table.vue";

export default {
  components: { Table },
  data: () => ({
    error: "",
    todos: [],
    todo: {
      name: "",
      description: "",
    },
    dialog: [],
    uptodo: {
      name: "",
      description: "",
    },
    valid: true,
    description: "",
    name: "",
    nameRules: [
      (v) => !!v || "Name is required",
      (v) => (v && v.length <= 50) || "Name must be less than 10 characters",
    ],
    email: "",
    emailRules: [
      (v) => !!v || "E-mail is required",
      (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
    ],
    select: null,
    items: ["Item 1", "Item 2", "Item 3", "Item 4"],
    checkbox: false,
    headers: [
      {
        text: "Name",
        align: "start",
        sortable: false,
        value: "name",
      },
      { text: "Description", value: "description" },
      { text: "Status", value: "status" },
      { text: "Date", value: "created" },
      { text: "Actions:", value: "actions" },
    ],
  }),
  mounted() {
    this.getTodos();
  },
  methods: {
    validate() {
      this.$refs.form.validate();
    },
    reset() {
      this.$refs.form.reset();
    },
    async getTodos() {
      const res = await axios.get("http://poppymeal.test/api/todo");
      if (res.status === 200) {
        this.todos = res.data.todos;
      }
    },
    async createTodo() {
      try {
        const res = await axios.post(
          "http://poppymeal.test/api/todo",
          this.todo
        );
        if (res.status === 201) {
          this.getTodos();
          this.reset();
        }
      } catch (err) {
        if (err.response.status === 422) {
          this.error = err.response.data.message;
        }
      }
    },

    async deleteTodo(id) {
      try {
        const res = await axios.delete(`http://poppymeal.test/api/todo/${id}`);
        if (res.status === 200) {
          this.getTodos();
          this.reset();
        }
      } catch (err) {
        if (err.response.status === 422) {
          this.error = err.response.data.message;
        }
      }
    },
    setDate(date) {
      return new Date(new Date(date) - new Date().getTimezoneOffset() * 60000)
        .toISOString()
        .substr(0, 10);
    },
    setData(item) {
      this.uptodo.name = item.name;
      this.uptodo.description = item.description;
    },
    async updateTodo(id) {
      try {
        const res = await axios.put(
          `http://poppymeal.test/api/todo/${id}`,
          this.uptodo
        );
        if (res.status === 200) {
          this.getTodos();
          this.dialog[id] = false;
        }
      } catch (err) {}
    },
  },
};
</script>

