<template>
  <v-data-table
    :headers="headers"
    :items="transport"
    sort-by="name"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>User Transport</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <v-card>
            <v-card-title>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12" sm="6" md="12"
                  >
                    <v-text-field
                      v-model="editedItem.name"
                      label="Name"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12" sm="6" md="12"
                  >
                    <v-text-field
                      v-model="editedItem.email"
                      label="Email"
                    ></v-text-field>
                    
                  </v-col>
                  <v-col
                    cols="12" sm="6" md="12"
                  >
                    <v-text-field
                      v-model="editedItem.address"
                      label="Address"
                    ></v-text-field>
                    
                  </v-col>
                  <v-col
                    cols="12" sm="6" md="12"
                  >
                    <v-text-field
                      v-model="editedItem.phoneNumber"
                      label="Phone Number"
                    ></v-text-field>
                    
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:[`item.actions`]="{ item }" >
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="primary"
        @click="initialize"
      >
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>
<script>
import axios from 'axios'
  export default {
    data: () => ({
      dialog: false,
      dialogDelete: false,
      headers: [
        {
          text: 'Name',
          align: 'start',
          sortable: false,
          value: 'fullname',
        },
        { text: 'Email', value: 'email', sortable: false },
        { text: 'Address', value: 'address', sortable: false },
        { text: 'Phone Number', value: 'phoneNumber', sortable: false },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      transport: [],
      editedIndex: -1,
      editedItem: {
        fullname: '',
        email:'',
        phoneNumber:'',
        address:'',
      },
      defaultItem: {
        fullname: '',
        email:'',
        phoneNumber:'',
        address:'',
      },
    }),
    async mounted() {
        this.LoadItens()
    },
    computed: {
      
    },
    watch: {
      dialog (val) {
        val || this.close()
      },
      dialogDelete (val) {
        val || this.closeDelete()
      },
    },
    created () {
      this.initialize()
    },
    methods: {
      async LoadItens(){
        // API URL must be constant, should be in config.
        const API_URL = "http://localhost:5000/api/transport";
        // If the fetch fails,
        try {
          // Fetch with custom headers.
          const transport = await fetch(API_URL, { "method":"GET" })
            .then(response => response.json())
            .then(data => this.transport = data
            );
          // Then set the user object to data
          this.transport = transport;
        } catch (err) {
          console.error(err);
      }
      },
      initialize () {
      },
      editItem (item) {
        this.editedIndex = this.transport.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },
      deleteItem (item) {
        this.editedIndex = this.transport.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
        if(this.dialogDelete == true){
          axios.delete(`http://localhost:5000/api/transport/${item._id}`)
        }
      },
      deleteItemConfirm () {
        this.transport.splice(this.editedIndex, 1)
        this.closeDelete()
      },
      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },
      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },
      save () {
        Object.assign(this.transport[this.editedIndex], this.editedItem)
        axios.put(`http://localhost:5000/api/transport/${this.editedItem._id}/${this.editedItem.fullname}/${this.editedItem.email}/${this.editedItem.address}/${this.editedItem.phoneNumber}`)
        this.close()
      },
    },
  }
</script>