<template>
  <div id="app">
    <h2>Global population: </h2>
    <p>Total Population: {{ totalPopulation }}</p>
    <label for="country_select">Select a Country:</label>
    <select id="country_select" v-model="selectedCountry">
      <option disabled value="">Select a country</option>
      <option v-for="country in countries" :value="country">{{country.name}}</option>
    </select>

    <!-- Country detail goes here -->
    <div v-if="selectedCountry!= null" class="">
      <p>{{ selectedCountry.name }} <img class="small-flag" v-bind:src="selectedCountry.flag"></img>
        <p>Bordering Countries:</p>
        <p v-for="country in selectedCountryNeighbours">{{ country.name }}   <img class="small-flag" v-bind:src="country.flag"></img></p>
        <p>Total Neighbouring Populations: {{ totalNeighbouringPopulation }}</p>
      </p>
    </div>

    <button v-on:click="addToFavourites">Add Country</button>

    <!-- Favourite Countries goes here -->
    <div class="">
      <ul v-for="country in favouriteCountries">
        <p>{{ country.name }}  <img class="small-flag" v-bind:src="country.flag"></img></p>
      </ul>

    </div>

</div>

</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      countries: [],
      selectedCountry: null,
      favouriteCountries: [],
      neighbourCountries: []
      // totalPopulation: null
    }
  },
    computed: {
      totalPopulation: function() {
        return this.countries.reduce((total, country) => {
          return total + country.population;
        }, 0)
      },
      selectedCountryNeighbours: function() {
        // return this.selectedCountry.borders;

        const result = this.countries.filter((country) => {
                return this.selectedCountry.borders.includes(country.alpha3Code);
          });
        return result;
      },
      totalNeighbouringPopulation: function() {
        return this.selectedCountryNeighbours.reduce((total, country) => {
          return total + country.population;
        }, 0)
      }


    },
    mounted(){
      this.fetchCountries();
    },
    methods: {
      fetchCountries: function() {
        fetch("https://restcountries.eu/rest/v2/all")
        .then(response =>  response.json())
        .then(data => this.countries = data)
      },
      addToFavourites: function() {
        const checkinList = this.favouriteCountries.filter((country) => {
          return country.name == this.selectedCountry.name;
        });
        console.log(checkinList);
        if (checkinList.length ==0){
          this.favouriteCountries.unshift(this.selectedCountry)
        }
      },


    }
}
</script>

<style>
.small-flag {
  height: 20px;
}



</style>
