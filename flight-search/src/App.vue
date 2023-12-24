<template>
    <v-app>
      <v-main>
        <v-container>
          <v-row>
            <v-col>
              <SearchForm @search="searchFlights" />
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <FlightList v-if="!loading" :flights="filteredFlights" />
            </v-col>
          </v-row>
        </v-container>
      </v-main>
    </v-app>
  </template>
  
  <script>
  import SearchForm from "@/components/SearchForm.vue";
  import FlightList from "@/components/FlightList.vue";
  
  export default {
    components: {
      SearchForm,
      FlightList,
    },
    data() {
      return {
        flights: [],
        filteredFlights: [],
        loading: false,
      };
    },
    methods: {
      async searchFlights(searchData) {
        try {
          this.loading = true;
  
          if (searchData.flights) {
            this.flights = searchData.flights;
          }
  
          this.filteredFlights = searchData.flights || [];
  
        } catch (error) {
          console.error("Uçuşları filtreleme hatası:", error);
        } finally {
          this.loading = false;
        }
      },
    },
  };
  </script>
  