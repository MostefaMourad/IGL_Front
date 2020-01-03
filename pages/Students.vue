<template>
  <v-layout child-flex >
    <v-card>
    
  <v-simple-table
      :dense="false"
      :fixed-header="true"
      width="1000"
    >
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">Name</th>
            <th class="text-left">FirstName</th>
            <th class="text-left">Date of Birth</th>
            <th class="text-left">Adress</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="etudiant in etudiants" :key="etudiant.id">
            <td>{{ etudiant.nom }}</td>
            <td>{{ etudiant.prenom }}</td>
            <td>{{ etudiant.date_naissance }}</td>
            <td>{{ etudiant.adresse }}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
    </v-card>
  </v-layout>
</template>

<script>

import axios from 'axios'

export default {
  async asyncData ({ params }) {
    const { data } = await axios.get('http://127.0.0.1:8000/api/etudiants')
    return { etudiants: data.data }
  }
}

</script>