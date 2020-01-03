<template>
  <v-layout
    column
    justify-center
    align-center
  >
   
  </v-layout>
</template>

<script>
import axios from 'axios'

export default {
  async asyncData ({ params }) {
    const { data } = await axios.get('http://127.0.0.1:8000/api/etudiants')
    return { etudiants: data.data }
  } ,

}
</script>
