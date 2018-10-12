<template>
 <v-app>
   <v-container class="container_form">
     <h1 style="text-align: center">World Capitals and flags</h1>
     <v-form ref="form" >
    <v-select v-model="select"  @change=" capitalsRefill" :items="countries" :rules="[v => !!v || 'select one']" label="countries" required>
    </v-select>
  </v-form>
  <h1>Country: {{select}}</h1>
  <hr>
  <h1>Capital: {{capitals[0]}}
  </h1>
  <hr>
<img class="flags" :src="flags">
<hr>
<div id="map" ></div>
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
      lat: null,
      lng: null,
      error: null,
      showError: false,
      select: null,
      countries: [],
      API_DATA: [],
      capitals: [],
      flags: "../src/assets/download.png",
      elements: null,
      COUNTRIES_API: "https://restcountries.eu/rest/v2/all"
    };
  },
  async created() {
    try {
      await this.countriesRefill();
    } catch (e) {}
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
      }
    },
    async capitalsRefill() {
      try {
        this.capitals = [];
        let capitalsRefill = (await axios.get(
          `https://restcountries.eu/rest/v2/name/${this.select}?fullText=true`
        )).data;
        if (!capitalsRefill) {
          alert("no se ha encontrado una capital");
          return;
        }
        this.elements = capitalsRefill;
        this.citiesLoop();
        this.initMap();
      } catch (error) {
        alert(error.message);
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
        this.lat = countrySelected.latlng[0];
        this.lng = countrySelected.latlng[1];
      });
    },
    initMap() {
      var map = new google.maps.Map(document.getElementById("map"), {
        center: { lat: this.lat, lng: this.lng },
        zoom: 7
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
  width: 530px;
  height: 300px;
}
#map {
  height: 30%;
}
html,
body {
  height: 100%;
  margin: 0;
  padding: 0;
}
</style>

