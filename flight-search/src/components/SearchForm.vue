<template>
  <div>
    <h2>Amadeus ile Uçuş Ara</h2>
    <v-form @submit.prevent="searchFlights">
      <v-row>
        <v-col>
          <v-autocomplete
            v-model="departureAirport"
            :items="filteredDepartureAirports"
            label="Kalkış Havaalanı"
          ></v-autocomplete>
        </v-col>
        <v-col>
          <v-autocomplete
            v-model="arrivalAirport"
            :items="filteredArrivalAirports"
            label="Varış Havaalanı"
          ></v-autocomplete>
        </v-col>
        <v-col>
          <v-menu
            v-model="departureMenu"
            :close-on-content-click="false"
            :nudge-right="40"
            offset-y
            transition="scale-transition"
          >
            <template v-slot:activator="{ on }">
              <v-text-field
                v-model="departureDate"
                label="Kalkış Tarihi"
                readonly
                v-on="on"
              ></v-text-field>
            </template>
            <v-date-picker
              v-model="departureDate"
              @input="departureMenu = false"
            ></v-date-picker>
          </v-menu>
        </v-col>
        <v-col v-if="!oneWay">
          <v-menu
            v-model="returnMenu"
            :close-on-content-click="false"
            :nudge-right="40"
            offset-y
            transition="scale-transition"
          >
            <template v-slot:activator="{ on }">
              <v-text-field
                v-model="returnDate"
                label="Dönüş Tarihi"
                readonly
                v-on="on"
              ></v-text-field>
            </template>
            <v-date-picker
              v-model="returnDate"
              @input="returnMenu = false"
            ></v-date-picker>
          </v-menu>
        </v-col>
        <v-col>
          <v-checkbox v-model="oneWay" label="Tek Yönlü Uçuş"></v-checkbox>
        </v-col>
        <v-col>
          <v-btn type="submit" color="primary">Ara</v-btn>
        </v-col>
      </v-row>
    </v-form>
  </div>
</template>

<script>
import Axios from "axios";

export default {
  data() {
    return {
      departureAirport: null,
      arrivalAirport: null,
      departureDate: null,
      returnDate: null,
      oneWay: false,
      departureMenu: false,
      returnMenu: false,
      airportList: ["IST", "JFK", "LHR", "CDG"], // Örnek havaalanları
    };
  },
  computed: {
    filteredDepartureAirports() {
      const departureAirportLowerCase = this.departureAirport
        ? this.departureAirport.toLowerCase()
        : "";
      return this.airportList
        ? this.airportList.filter((airport) =>
            airport.toLowerCase().includes(departureAirportLowerCase)
          )
        : [];
    },
    filteredArrivalAirports() {
      const arrivalAirportLowerCase = this.arrivalAirport
        ? this.arrivalAirport.toLowerCase()
        : "";
      return this.airportList
        ? this.airportList.filter((airport) =>
            airport.toLowerCase().includes(arrivalAirportLowerCase)
          )
        : [];
    },
  },
  methods: {
    async searchFlights() {
      try {
        const response = await Axios.get("http://localhost:8080/flights", {
          params: {
            departureAirport: this.departureAirport,
            arrivalAirport: this.arrivalAirport,
            departureDate: this.departureDate,
            returnDate: this.returnDate,
            oneWay: this.oneWay,
          },
        });

        const flightsData = response.data;
        this.$emit("search", flightsData.flights);
      } catch (error) {
        console.error("API isteği başarısız:", error);
      }
    },
  },

  watch: {
    oneWay(newValue) {
      // Tek yönlü uçuş seçeneği değiştiğinde, dönüş tarihi alanını sıfırla
      if (newValue) {
        this.returnDate = null;
      }
    },
  },
};
</script>
