<template>
  <div class="text-center">
    <v-dialog v-model="dialog[todo.id]" width="500">
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          color="green"
          dark
          v-bind="attrs"
          v-on="on"
          class="mr-2"
          @click="setData()"
        >
          EDIT {{ todo.id }}
        </v-btn>
      </template>

      <v-card>
        <v-card-title class="text-h5 grey lighten-2"> EDIT </v-card-title>

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
          <v-btn color="primary" text @click="updateTodo(todo.id)"
            >UPDATE
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  props: ["todo"],
  data() {
    return {
      dialog: [],
      uptodo: {
        name: "",
        description: "",
      },
    };
  },
  methods: {
    setData() {
      this.uptodo.name = this.todo.name;
      this.uptodo.description = this.todo.description;
    },
    async updateTodo(id) {
      try {
      const res = await axios.put(`http://poppymeal.test/api/todo/${id}`, this.uptodo);
        if (res.status === 200) {
            this.$router.push('/project')
        }
      } catch (err) {
        
      }
    },
  },
};
</script>