<template>
 <v-app>
   <v-container class="container_form">
     <h1 style="text-align: center">World Capitals and flags</h1>
     <v-form ref="form" >
    <v-select
      v-model="select"
      :items="countries"
      :rules="[v => !!v || 'select one']"
      label="countries"
      required
    ></v-select>
      
    <v-layout row wrap>
    <v-flex  xs6>
          <v-btn @click=" capitalsRefill" >Generar capital</v-btn>
      </v-flex>
    </v-layout>
  </v-form>
  <h1>capital: {{capitals}}</h1>
<img class="flags" :src="flags">
   <v-alert
      class="alerts"
      :value="show"
      type="success"
      transition="scale-transition">
      El reporte fue generado correctamente.
    </v-alert>
  <v-alert class="alerts"
      :value="showError"
      type="error"
      transition="scale-transition">
      {{error}}
    </v-alert>
   </v-container>
 </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: 'app',
  data () {
    return {
      error:null,
      showError:false,
      select:null,
      countries:[],
      API_DATA:[],
      capitals:[],
      flags:[],
      elements: null

    }
  },
  created(){
    axios
    .get('https://restcountries.eu/rest/v2/all')
    .then(response => {
     this.API_DATA = (response.data)
     this.countriesLoop();
      }) 
    .catch(err => {
      console.log(err)
    })
    
  },
  methods:{
     showErrors(){
       this.showError = !this.showError
     },
   countriesLoop(){
   this.API_DATA.forEach(country => {
     this.countries.push(country.name)
   });
   },
   capitalsRefill(){
     this.capitals=[];
       let loader = this.$loading.show();
     axios
    .get(`https://restcountries.eu/rest/v2/name/${this.select}?fullText=true`)
    .then(response => {
     loader.hide(); 
    this.elements = response.data
     this.elements.forEach(countrySelected => {
     this.capitals.push(countrySelected.capital)
     this.flags = countrySelected.flag
   });
    })
    .catch(err =>{
      var errorMessage = err.response.data.message 
      this.error = errorMessage
      loader.hide();
     this.showErrors();
      setTimeout(()=>{
        this.showError = !this.showError
      },3000)
    })
  },
  }
  }
</script>

<style>
.alerts{
  margin: 25px auto;
}
.container_form {
    max-width: 600px;
    width: 100%;
}
.banner {
  height : 300px;
  border : gray solid 1px;
}
.flags{
  width : 150px;
  height: 100px;
}

</style>

