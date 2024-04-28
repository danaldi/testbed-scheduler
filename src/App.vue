<template>
  <div id="app">
    <h1>Homelab Scheduler</h1>
    <reservation-form @reservation-created="fetchReservations"></reservation-form>
    <reservation-list :reservations="reservations" @status-updated="fetchReservations"></reservation-list>
  </div>
</template>

<script>
import ReservationForm from './components/ReservationForm.vue';
import ReservationList from './components/ReservationList.vue';

export default {
  components: {
    ReservationForm,
    ReservationList
  },
  data() {
    return {
      reservations: []
    };
  },
  methods: {
    fetchReservations() {
      fetch('http://localhost:5010/api/reservations')
        .then(response => response.json())
        .then(data => {
          this.reservations = data;
        })
        .catch(error => console.error('Error:', error));
    }
  },
  mounted() {
    this.fetchReservations();
  }
}
</script>
