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
          <LoadingSpinner v-if="loading" />
          <FlightList v-if="!loading" :flights="filteredFlights" />
        </v-col>
      </v-row>
    </v-container>
        </v-main>
    </v-app>
    
  </template>
  
  <script>
  import SearchForm from '@/components/SearchForm.vue';
  import FlightList from '@/components/FlightList.vue';
  import LoadingSpinner from '@/components/LoadingSpinner.vue';

  import Axios from 'axios';
  
  export default {
    components: {
      SearchForm,
      FlightList,
      LoadingSpinner,
    },
    data() {
      return {
        flights: [],
        filteredFlights: [],
        loading: false,
      };
    },
    methods: {
  async searchFlights() {
    try {
      this.loading = true; // İstek yapılırken loading durumunu true yap
      // Axios veya başka bir HTTP istemcisini kullanarak mock API'ye istek yapma
      const response = await Axios.get("http://localhost:8080/flights", {
        // Gerekli parametreleri buraya ekleyin
      });

      // Gelen verileri filteredFlights'e atama
      this.filteredFlights = response.data.flights;
    } catch (error) {
      // Hata durumunda işlemler
      console.error("API isteği başarısız:", error);
    } finally {
      this.loading = false; // İstek tamamlandığında loading durumunu false yap
    }
  },
},
  };
  </script>
  