<template>
  <v-data-table
    :headers="headers"
    :items="events"
    color="white"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat color="dark">
        <v-toolbar-title >Events Management</v-toolbar-title>

       <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>

       <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">

          <template v-slot:activator="{ on }">
            <v-btn color="#38d39f" dark class="mb-2" v-on="on">New Event</v-btn>
          </template>

        <v-card>
        <v-card-title>
          <span class="headline">{{formTitle}}</span>
        </v-card-title>

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                <v-text-field v-model="editedEvent.nom" label="Event Name*" required></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="8">
                <v-text-field
                  v-model="editedEvent.organisateur"
                  label="Organiser*"
                  hint="GDG,CACE,CSE...*"
                  persistent-hint
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12">
                <v-text-field v-model="editedEvent.description" label="Description*" type="text" required></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field v-model="editedEvent.adresse" label="Adress*" required></v-text-field>
              </v-col> 
              <v-col cols="12" sm="6" md="6">
                    <v-text-field v-model="editedEvent.nbr_interesse" type="number" label="Intreseted Peaple"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="6">
                    <v-text-field v-model="editedEvent.nbr_participe" type="number" label="Participate"></v-text-field>
                  </v-col>  
            </v-row>
          </v-container>
          <small>*indicates required field</small>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="close">Close</v-btn>
          <v-btn color="blue darken-1" text @click="save">Save</v-btn>
        </v-card-actions>
        
      </v-card>
        </v-dialog> 

      </v-toolbar>
    </template>


    <template v-slot:item.action="{ item }">
      <v-icon
        color="#38d39f"
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>

      <v-icon
        small
        color="red lighten-1"
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="asyncData">Reset</v-btn> 
    </template>

  </v-data-table>
</template>

<script>

import axios from 'axios'

export default {
  data: () => ({
    lists: [] ,
    dialog: false,
     headers : [
       {text:'Name',align :'center',value:'nom'},
       {text:'Description',align :'center', sortable: false,value:'description'},
       {text:'Intrested Peaple',align :'center',value:'nbr_interesse'},
       {text:'Partipate',align :'center',sortable: false,value:'nbr_participe'},
       {text:'Organiser',align :'center',value:'organisateur'},
       {text:'Adress',align :'center', sortable: false,value:'adresse'},
       {text: 'Actions', value: 'action', sortable: false}
     ] ,
      editedIndex: -1,
      editedEvent: {
         nom :"",  
         description:"",
         nbr_interesse:0,  
         nbr_participe:0,
         organisateur:"",
         adresse:"",
         date_debut:null,
         duree:null
      },
      defaultEvent: { 
         nom :"",  
         description:"",
         nbr_interesse:0,  
         nbr_participe:0,
         organisateur:"",
         adresse:"",
         date_debut:null,
         duree:null
      }, 
      events:[],
      
  }),
  async asyncData ({ params }) {
    const { data }  = await axios.get('http://127.0.0.1:8001/api/events')
    return { events: data.data }
  } ,
  computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Event' : 'Edit Event'
      },
    },
  methods: { 
    close () {
        this.dialog = false
        setTimeout(() => {
          this.editedEvent = Object.assign({}, this.defaultEvent)
          this.editedIndex = -1
        }, 300)
      },
     save () {   
       if(this.editedIndex>-1){
        var id = this.editedEvent.id
        console.log(this.editedEvent)
        axios.patch(`http://127.0.0.1:8001/api/events/${id}`,this.editedEvent)
        .then(function(response){
          console.log(response)
        }).catch(error => {console.log(error)})
       }  
       else{
       axios.post('http://127.0.0.1:8001/api/events',this.editedEvent)
        .then(function(response){
          console.log(response)
        }).catch(error => {console.log(error)})
       } 
       this.close()
      setTimeout(() => {
          window.location.reload(true);
        }, 300)
      },
      editItem(item){
        this.editedIndex = this.events.indexOf(item)
        this.editedEvent = Object.assign({}, item)
        this.dialog = true
      } ,
      deleteItem (item) {
        var id = this.events.indexOf(item)

       axios.delete(`http://127.0.0.1:8001/api/events/${item.id}`)
        .then(function(response){
          console.log(response)
        }).catch(error => {console.log(error)})
          setTimeout(() => {
          this.editedEvent = Object.assign({}, this.defaultEvent)
          this.editedIndex = -1
        }, 300)
      },
  }  


}
</script>