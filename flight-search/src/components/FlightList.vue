<template>
  <div>
    <h2>Uçuş Listesi</h2>
    <v-data-table
      :items="flights"
      :headers="headers"
      :search="search"
      class="elevation-1"
    >
      <template v-slot:items="props">
        <td>{{ props.item.departureAirport }}</td>
        <td>{{ props.item.arrivalAirport }}</td>
        <td>{{ props.item.duration }}</td>
        <td>{{ props.item.price }}</td>
      </template>
    </v-data-table>
  </div>
</template>

<script>
import flightService from "@/services/FlightService.js";

export default {
  data() {
    return {
      flights: [],
      headers: [
        { text: "Kalkış Havaalanı", value: "departureAirport" },
        { text: "Varış Havaalanı", value: "arrivalAirport" },
        { text: "Uçuş Süresi", value: "duration" },
        { text: "Fiyat", value: "price" },
      ],
      search: "",
    };
  },
  mounted() {
    this.fetchFlights();
  },
  methods: {
    async fetchFlights() {
      try {
        const response = await flightService.getFlights();
        this.flights = response.data;
      } catch (error) {
        console.error("API isteği başarısız:", error);
      }
    },
  },
};
</script>
