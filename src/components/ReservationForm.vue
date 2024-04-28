<template>
    <form @submit.prevent="submitForm">
      <label for="user_id">User ID:</label>
      <input type="text" id="user_id" v-model="reservation.user_id" required>
  
      <label for="resource_id">Resource ID:</label>
      <input type="text" id="resource_id" v-model="reservation.resource_id" required>
  
      <label for="start_time">Start Time:</label>
      <input type="datetime-local" id="start_time" v-model="reservation.start_time" required>
  
      <label for="end_time">End Time:</label>
      <input type="datetime-local" id="end_time" v-model="reservation.end_time" required>
  
      <button type="submit">Create Reservation</button>
    </form>
  </template>
  
  <script>
  export default {
    data() {
      return {
        reservation: {
          user_id: '',
          resource_id: '',
          start_time: '',
          end_time: ''
        }
      };
    },
    methods: {
      submitForm() {
        fetch('http://localhost:5010/api/reservations', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(this.reservation)
        })
        .then(response => response.json())
        .then(data => {
          this.$emit('reservation-created');
          alert('Reservation Created');
          console.log(data)
        })
        .catch(error => console.error('Error:', error));
      }
    }
  }
  </script>
  