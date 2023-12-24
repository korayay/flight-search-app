<!-- flightlist.vue -->
<template>
  <div>
    <h2>Uçuş Listesi</h2>
    <p>Uçuş detayı öğrenmek için tabloda o uçuş için herhangi bir yerine tıklayabilirsiniz!</p>
    <!-- Loading animasyonu -->
    <v-container v-if="loading">
      <v-row align="center" justify="center">
        <v-progress-circular indeterminate size="64"></v-progress-circular>
      </v-row>
    </v-container>

    <!-- Uçuş listesi tablosu -->
    <v-data-table
      v-else
      :items="filteredFlights"
      :headers="headers"
      :search="search"
      class="elevation-1"
      :sort-by="sortBy"
      :sort-desc="sortDesc"
      @click:header="sortFlights"
    >
      <template v-slot:item="{ item }">
        <tr @click="showFlightDetails(item)" style="cursor: pointer;">
          <td>{{ item.departureAirport }}</td>
          <td>{{ item.arrivalAirport }}</td>
          <td>{{ item.duration }}</td>
          <td>{{ item.price }}</td>
        </tr>
      </template>
    </v-data-table>

    <!-- Uçuş detayları dialog -->
    <v-dialog v-model="detailsDialog" max-width="600">
      <v-card>
        <v-card-title>Uçuş Detayları</v-card-title>
        <v-card-text>
          Havayolu: {{ selectedFlight.airline }}<br />
          Kalkış Havaalanı: {{ selectedFlight.departureAirport }}<br />
          Varış Havaalanı: {{ selectedFlight.arrivalAirport }}<br />
          Kalkış Zamanı: {{ formatDate(selectedFlight.departureTime) }}<br />
          Varış Zamanı: {{ formatDate(selectedFlight.arrivalTime) }}<br />
          Fiyat: {{ selectedFlight.price }}<br />
          Uçuş Süresi: {{ selectedFlight.duration }}<br />
        </v-card-text>
        <v-card-actions>
          <v-btn @click="detailsDialog = false">Kapat</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import flightService from "@/services/FlightService.js";

export default {
  data() {
    return {
      flights: [],
      headers: [
        {
          title: "Kalkış Havaalanı",
          value: "departureAirport",
          sortable: true,
        },
        { title: "Varış Havaalanı", value: "arrivalAirport", sortable: true },
        { title: "Uçuş Süresi", value: "duration", sortable: true },
        { title: "Fiyat", value: "price", sortable: true },
      ],

      search: {
        departureAirport: "",
        arrivalAirport: "",
      },
      sortBy: ["departureAirport"],
      sortDesc: [false],
      detailsDialog: false,
      selectedFlight: {},
      loading: true,
    };
  },
  computed: {
    filteredFlights() {
      const departureAirportLowerCase =
        this.search.departureAirport.toLowerCase();
      const arrivalAirportLowerCase = this.search.arrivalAirport.toLowerCase();

      return this.flights.filter(
        (flight) =>
          flight.departureAirport
            .toLowerCase()
            .includes(departureAirportLowerCase) &&
          flight.arrivalAirport.toLowerCase().includes(arrivalAirportLowerCase)
      );
    },
  },
  mounted() {
    this.fetchFlights();
  },
  methods: {
    async fetchFlights() {
      try {
        this.loading = true;

        const response = await flightService.getFlights();
        this.flights = response.data;

        this.loading = false;
      } catch (error) {
        console.error("API isteği başarısız:", error);
        this.loading = false;
      }
    },
    sortFlights(sortDetails) {
      this.sortBy = [sortDetails.field];
      this.sortDesc = [sortDetails.sortDesc];

      this.flights.sort((a, b) => {
        const modifier = this.sortDesc[0] ? -1 : 1;
        if (a[this.sortBy[0]] < b[this.sortBy[0]]) return -1 * modifier;
        if (a[this.sortBy[0]] > b[this.sortBy[0]]) return 1 * modifier;
        return 0;
      });
    },
    showFlightDetails(flight) {
      this.selectedFlight = flight;
      this.detailsDialog = true;
    },
    formatDate(dateTimeString) {
      const options = {
        year: "numeric",
        month: "long",
        day: "numeric",
        hour: "numeric",
        minute: "numeric",
      };
      return new Date(dateTimeString).toLocaleDateString(undefined, options);
    },
  },
};
</script>
