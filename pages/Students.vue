<template>
  <v-data-table
    :headers="headers"
    :items="etudiants"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat color="dark">
        <v-toolbar-title >Students Management</v-toolbar-title>

       <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
       <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">

          <template v-slot:activator="{ on }">
            <v-btn color="primary" dark class="mb-2" v-on="on">New Student</v-btn>
          </template>

        <v-card>
        <v-card-title>
          <span class="headline">{{formTitle}}</span>
        </v-card-title>

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                <v-text-field v-model="editedStudent.prenom" label="first name*" required></v-text-field>
              </v-col>

              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedStudent.nom"
                  label="last name*"
                  hint="Chergou,Dib...*"
                  persistent-hint
                  required
                ></v-text-field>
              </v-col>

              <v-col  cols="12" sm="4">
                <v-select v-model="editedStudent.niveau"
                  :items="['1CP', '2CP', '1CS', '2CS','3CS']"
                  label="Level*"
                  required
                ></v-select>
              </v-col>
              <v-col cols="12">
                <v-text-field v-model="editedStudent.email" label="Email*" type="email" required></v-text-field>
              </v-col>
            <!--  <v-col cols="12">
                <v-text-field v-model="editedStudent.password" label="Password*" type="password" required></v-text-field>
              </v-col> -->
              <v-col cols="12">
                <v-text-field v-model="editedStudent.adresse" label="Adress*" required></v-text-field>
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
        small
        color="light-blue"
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
       {text:'LastName',align :'left',value:'nom'},
       {text:'FirstName',align :'left' ,value:'prenom'},
       {text:'Email',align :'left',sortable:'false',value:'email'},
       {text:'Adress',align :'left',sortable:'false',value:'adresse'},
       {text:'Date of Birth',align :'left',value:'date_naissance'},
       {text: 'Actions', value: 'action', sortable: false}
     ] ,
      editedIndex: -1,
      editedStudent: {
        email: '',
        password :"Esi@2018",
        nom: '',
        prenom: '',
        niveau: "1CP",
        section :null,
        groupe :null,
        specialite:null,
        date_naissance:'1999-07-13',
        adresse:'',
        indicateur_promo:"h",  
      },
      defaultStudent: { 
        email:"",
        password:"Esi@2018",
        nom:"",
        prenom:"",
        niveau:"1CP",
        section:null,
        groupe:null,
        specialite:null,
        date_naissance:"1999-07-13",
        adresse:"",
        indicateur_promo:"h" 
      }, 
      etudiants:[],
      
  }),
  async asyncData ({ params }) {
    const { data }  = await axios.get('http://127.0.0.1:8000/api/etudiants')
    return { etudiants: data.data }
  } ,
  computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Student' : 'Edit Student'
      },
    },
  methods: { 
    close () {
        this.dialog = false
        setTimeout(() => {
          this.editedStudent = Object.assign({}, this.defaultStudent)
          this.editedIndex = -1
        }, 300)
      },
     save () {   
       if(this.editedIndex>-1){
        var id = this.editedStudent.id
        console.log(this.editedStudent)
        axios.patch(`http://127.0.0.1:8000/api/etudiants/${id}`,this.editedStudent)
        .then(function(response){
          console.log(response)
        }).catch(error => {console.log(error)})
       }  
       else{      
       axios.post('http://127.0.0.1:8000/api/etudiants',this.editedStudent)
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
        this.editedIndex = this.etudiants.indexOf(item)
        this.editedStudent = Object.assign({}, item)
        this.dialog = true
      } ,
      deleteItem (item) {
        var id = this.etudiants.indexOf(item)

       axios.delete(`http://127.0.0.1:8000/api/etudiants/${item.id}`)
        .then(function(response){
          console.log(response)
        }).catch(error => {console.log(error)})
      setTimeout(() => {
          this.editedStudent = Object.assign({}, this.defaultStudent)
          this.editedIndex = -1
        }, 300)
      },
  }  


}
</script>