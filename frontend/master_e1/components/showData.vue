<template>
  <v-sheet class="fill-container" elevation="0">
    <v-toolbar flat color="transparent">
      <v-toolbar-title>สมาชิก</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn color="primary" @click="openAddDialog">เพิ่มสมาชิก</v-btn>
    </v-toolbar>

    <v-data-table
      class="full-table"
      :headers="headers"
      :items="books"
      :hide-default-footer="books.length < 11"
      dense
    >
      <template v-slot:item.actions="{ item }">
        <v-icon small color="blue" class="me-2" @click="openEditDialog(item)">mdi-pencil</v-icon>
        <v-icon small color="red" @click="remove(item.id)">mdi-delete</v-icon>
      </template>
    </v-data-table>

    <!-- Add/Edit Dialog -->
    <v-dialog v-model="dialog" max-width="500">
      <v-card>
        <v-card-title>
          <span class="text-h6">{{ isEdit ? "แก้ไขสมาชิก" : "เพิ่มสมาชิก" }}</span>
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="form.email" label="Email"></v-text-field>
          <v-text-field v-model="form.password" label="Password"></v-text-field>
          <v-text-field v-model="form.status" label="Status"></v-text-field>
          <v-text-field v-model="form.dep" label="Dep"></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog=false">ยกเลิก</v-btn>
          <v-btn color="blue darken-1" text @click="save">บันทึก</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-sheet>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      books: [],
      headers: [
        { text: "ID", value: "id" },
        { text: "Email", value: "email" },
        { text: "Password", value: "password" },
        { text: "Status", value: "status" },
        { text: "Dep", value: "dep" },
        { text: "Actions", value: "actions", sortable: false },
      ],
      dialog: false,
      isEdit: false,
      form: { id: null, email: "", password: "", status: "", dep: "" },
      token: localStorage.getItem("token") || "",
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      try {
        const res = await axios.get("http://localhost:7000/", {
          headers: { Authorization: `Bearer ${this.token}` }
        });
        this.books = res.data;
      } catch (err) { console.error(err); }
    },

    openAddDialog() {
      this.isEdit = false;
      this.form = { id: null, email: "", password: "", status: "", dep: "" };
      this.dialog = true;
    },

    openEditDialog(item) {
      this.isEdit = true;
      this.form = { ...item };
      this.dialog = true;
    },

    async save() {
      try {
        if (this.isEdit) {
          await axios.post(`http://localhost:7000/edit-user/${this.form.id}`, this.form, {
            headers: { Authorization: `Bearer ${this.token}` }
          });
        } else {
          await axios.post("http://localhost:7000/insert", this.form, {
            headers: { Authorization: `Bearer ${this.token}` }
          });
        }
        this.dialog = false;
        this.fetchData();
      } catch (err) { console.error(err); }
    },

    async remove(id) {
      if (!confirm("คุณแน่ใจว่าต้องการลบสมาชิกนี้?")) return;
      try {
        await axios.delete(`http://localhost:7000/delete-user/${id}`, {
          headers: { Authorization: `Bearer ${this.token}` }
        });
        this.fetchData();
      } catch (err) { console.error(err); }
    }
  }
};
</script>

<style scoped>
.fill-container {
  height: 100%;
  display: flex;
  flex-direction: column;
}
.full-table {
  flex: 1;
  overflow-y: auto;
  margin-top: 10px;
}
</style>
