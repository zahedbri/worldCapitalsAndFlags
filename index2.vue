<template>
 <v-app>
   <v-container class="container_form">
     <h1 style="text-align: center">World Capitals and flags</h1>
     <v-form ref="form" >
    <v-select v-model="select" :items="countries" :rules="[v => !!v || 'select one']" label="countries" required>
    </v-select>
    <v-layout row wrap>
    <v-flex  xs6><v-btn @click=" capitalsRefill" >Generar capital</v-btn>
      </v-flex>
    </v-layout>
  </v-form>
  <h1>capital: {{capitals}}
  </h1>
<img class="flags" :src="flags">
  <v-alert class="alerts" :value="showError" type="error" transition="scale-transition"> {{error}}
  </v-alert>
   </v-container>
 </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: "app",
  data() {
    return {
      error: null,
      showError: false,
      select: null,
      countries: [],
      API_DATA: [],
      capitals: null,
      flags: "../src/assets/download.png",
      elements: null,
      COUNTRIES_API: "https://restcountries.eu/rest/v2/all",
      CITIES_API: `https://restcountries.eu/rest/v2/name/${
        this.select
      }?fullText=true`
    };
  },
  created() {
    this.countriesRefill()
      .then(console.log)
      .catch(console.log);
  },
  methods: {
    showErrors() {
      this.showError = !this.showError;
    },
    async countriesRefill() {
      try {
        let countriesData = (await axios.get(this.COUNTRIES_API)).data;
        if (!countriesData) {
          alert("No se han podido cargar los datos");
          return;
        }
        this.API_DATA = countriesData;
        this.countriesLoop();
      } catch (error) {
        alert(error.message);
        this.showError();
      }
    },
    async capitalsRefill() {
      try {
        let loader = this.$loading.show();
        this.capitals = "";
        let capitalsRefill = (await axios.get(this.CITIES_API)).data;
        if (!capitalsRefill) {
          alert("no se ha encontrado una capital");
          return;
        }
        this.elements = capitalsRefill;
        this.citiesLoop();
        loader.hide();
      } catch (error) {
        alert(error.message);
        this.showError();
      }
    },
    countriesLoop() {
      this.API_DATA.forEach(country => {
        this.countries.push(country.name);
      });
    },
    citiesLoop() {
      this.elements.forEach(countrySelected => {
        this.capitals.push(countrySelected.capital);
        this.flags = countrySelected.flag;
      });
    }
  }
};
</script>

<style>
.alerts {
  margin: 25px auto;
}
.container_form {
  max-width: 600px;
  width: 100%;
}
.banner {
  height: 300px;
  border: gray solid 1px;
}
.flags {
  width: 300px;
  height: 200px;
}
</style>
