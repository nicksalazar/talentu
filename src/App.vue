<template>
  <v-app>
    <div id="app">
      <v-card>
        <v-card-text>
          <v-layout row wrap>
            <v-flex sm6 md6 xs12>
              <v-card>
                <v-card-title> Nuevo Usuario </v-card-title>
                <v-card-text>
                  <v-form ref="form" v-model="valid" lazy-validation>
                    <v-text-field
                      v-model="name"
                      :rules="nameRules"
                      label="Nombre(s)"
                      required
                    ></v-text-field>

                    <v-text-field
                      v-model="surname"
                      :rules="surnameRules"
                      label="Apellido(s)"
                      required
                    ></v-text-field>

                    <v-text-field
                      v-model="email"
                      :rules="emailRules"
                      label="E-mail"
                      required
                    ></v-text-field>

                    <v-text-field
                      v-model="birthday"
                      :rules="birthdayRules"
                      label="Fecha de nacimiento"
                      type="date"
                      required
                    ></v-text-field>

                    <v-btn :disabled="!valid" color="success" @click="newUser">
                      guardar
                    </v-btn>
                  </v-form>
                </v-card-text>
              </v-card>
            </v-flex>
            <v-flex sm6 md6 xs12>
              <v-data-table
                :headers="headers"
                hide-actions
                :items="items"
                class="elevation-1"
              >
                <template v-slot:items="props">
                  <td>{{ props.item.id }}</td>
                  <td class="text-xs-right">
                    {{ props.item.first_name }} {{ props.item.last_name }}
                  </td>
                  <td class="text-xs-right">
                    {{ props.item.birthday || "-" }}
                  </td>
                  <td class="text-xs-right">{{ props.item.email }}</td>
                </template>
              </v-data-table>
            </v-flex>
          </v-layout>
        </v-card-text>
      </v-card>
    </div>
  </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data: () => ({
    headers: [
      {
        text: "Id",
        value: "id",
      },
      { text: "Nombre", value: "name" },
      { text: "Edad", value: "birthday" },
      { text: "Email", value: "email" },
    ],
    items: [],
    valid: true,
    name: "",
    surname: "",
    nameRules: [(v) => !!v || "Nombre es requerido"],
    surnameRules: [(v) => !!v || "Apellido es requerido"],
    email: "",
    birthday: "",
    birthdayRules: [(v) => !!v || "Fecha de nacimiento es requerido"],
    emailRules: [
      (v) => !!v || "Email es requerido",
      (v) => /.+@.+/.test(v) || "Email no valido",
    ],
  }),
  methods: {
    getData() {
      axios.get("https://reqres.in/api/users?page=1").then((res) => {
        const data = res.data.data || [];
        const localData = localStorage.getItem("data");
        if (localData) {
          this.items = JSON.parse(localData);
        } else {
          localStorage.setItem("data", JSON.stringify(data));
          this.items = data;
        }
      });
    },
    newUser() {
      if (this.$refs.form.validate()) {
        const randomID = Math.floor(Math.random() * 100);
        this.items.push({
          id: randomID,
          email: this.email,
          first_name: this.name,
          last_name: this.surname,
          birthday: this.birthday,
        });
        localStorage.setItem("data", JSON.stringify(this.items));
        this.reset();
      }
    },
    reset() {
      this.$refs.form.reset();
    },
  },
  created() {
    this.getData();
  },
};
</script>

<style>
</style>
