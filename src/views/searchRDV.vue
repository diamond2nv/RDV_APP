<template>
  <v-content class="ma-5 pa-4">
    <v-form ref="form" v-model="valid" lazy-validation>
      <v-text-field v-model="name" :counter="10" :rules="nameRules" label="Name" required></v-text-field>
      <v-text-field v-model="prenom" :counter="10" :rules="prenomRules" label="Prenom" required></v-text-field>

      <v-btn :disabled="!valid" color="success" class="mr-4" @click="validate">search</v-btn>

      <v-btn color="error" class="mr-4" @click="reset">Reset Form</v-btn>

      <v-btn color="warning" @click="resetValidation">Reset Validation</v-btn>
    </v-form>
    <!-- <v-data-table
      @click="overlayMenuOptions($event)"
      :headers="headers"
      :items="filteredData"
      :items-per-page="5"
      class="elevation-1"
    ></v-data-table>-->

    <v-container fluid>
      <v-data-iterator :items="filteredData">
        <template v-slot:default="props">
          <v-row>
            <v-col v-for="(item,index) in props.items" :key="index" cols="12" sm="6" md="6" lg="4">
              <v-card>
                <v-card-text>
                  <v-list-item v-for="(header,index2) in headers" :key="index2">
                    <v-list-item-content>{{header.text}}:</v-list-item-content>
                    <v-list-item-content class="align-end">{{ item[header.value] }}</v-list-item-content>
                  </v-list-item>
                </v-card-text>
                <v-divider></v-divider>
                <v-card-actions>
                  <v-btn @click="overlayMenuOptions(item)">
                    <v-icon>search</v-icon>Search
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-col>
          </v-row>
        </template>
      </v-data-iterator>
    </v-container>
    <v-overlay :value="overlayMenu">
      <v-btn text @click="overlayMenu = false">
        <v-icon>mdi-close</v-icon>Close
      </v-btn>
      <v-container>
        <v-card class="white--text ma-4 pa-4">
          <v-subheader>Modifier</v-subheader>
          <v-container v-for="(header,index) in headers" :key="index">
            <span style="color: white" class="white--text">{{header.text}}:</span>
            <!-- v-show="header.type == null ||  header.type!='Date'" -->
            <v-text-field v-model="selectedItem[header.value]" class="black"></v-text-field>
            <v-chip-group>
              <v-chip v-show="header.type=='Date'">{{selectedDate}}</v-chip>
              <v-chip v-show="header.type=='Date'">{{selectedTime}}</v-chip>
            </v-chip-group>
            <v-btn v-show="header.type=='Date'" class="primary">Modifier Date et Heure</v-btn>
          </v-container>
          <v-card-actions>
            <v-btn @click="modifier(selectedItem)" class="primary">
              <v-icon>done</v-icon>Modifier
            </v-btn>
          </v-card-actions>
          <v-divider></v-divider>
          <v-subheader>Supprimer</v-subheader>
          <v-card-actions>
            <v-btn @click="supprimer(selectedItem)" class="primary">
              <v-icon>undone</v-icon>Supprimer
            </v-btn>
          </v-card-actions>
          <v-divider></v-divider>
          <v-subheader>Imprimer</v-subheader>
          <v-card-actions>
            <v-btn @click="print(selectedItem)" class="primary">
              <v-icon>print</v-icon>Imprimer
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-container>
    </v-overlay>
  </v-content>
</template>
<script>
import { Patient, RENDEZVS } from "./../dao";

export default {
  props: ["headers", "filteredData", "peopleConcerned"],
  data: () => ({
    selectedItem: {},
    selectedDate: new Date().toISOString().substr(0, 10),
    selectedTime: new Date().toISOString().substr(11, 5),
    overlayMenu: false,
    valid: true,
    name: "foo",
    nameRules: [
      v => !!v || "Name is required",
      v => (v && v.length <= 10) || "Name must be less than 10 characters"
    ],
    prenom: "bar",
    prenomRules: [
      v => !!v || "Prenom is required",
      v => (v && v.length <= 10) || "Prenom must be less than 10 characters"
    ]
  }),

  methods: {
    validate() {
      if (this.$refs.form.validate())
        this.$emit("accessStorage", {
          type: "search",
          data: [new Patient(this.name, this.prenom)]
        });
    },
    reset() {
      this.$refs.form.reset();
    },
    resetValidation() {
      this.$refs.form.resetValidation();
    },
    overlayMenuOptions: function(event) {
      if (!!event) this.overlayMenu = true;
      this.selectedItem = event;
      this.overlayMenu;
      event;
    },
    modifier(item) {
      this.$emit("accessStorage", {
        type: "update",
        data: [item]
      });
      this.validate();
      this.overlayMenu = false;
    },
    supprimer(item) {
      this.$emit("accessStorage", {
        type: "delete",
        data: [item]
      });
      this.validate();
      this.overlayMenu = false;
    },

    print(item) {
      this.$emit("accessStorage", {
        type: "print",
        data: [item]
      });
      this.validate();
      this.overlayMenu = false;
    }
  }
};
</script>