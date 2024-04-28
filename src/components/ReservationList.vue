<template>
  <ul>
    <li v-for="reservation in reservations" :key="reservation.id">
      {{ reservation.user_id }} - {{ reservation.resource_id }} - {{ reservation.start_time }} - {{ reservation.end_time }} - Status: {{ reservation.status }}
      <button @click="updateStatus(reservation.id, 'completed')">Complete</button>
      <button @click="updateStatus(reservation.id, 'cancelled')">Cancel</button>
    </li>
  </ul>
</template>

<script>
export default {
  props: {
    reservations: Array
  },
  methods: {
    updateStatus(reservationId, status) {
      console.log(status)
      fetch(`http://localhost:5010/api/reservations/${reservationId}/status`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ status })
      })
      .then(response => response.json())
      .then(() => {
        this.$emit('status-updated');  // Emit an event to refresh the list
        alert('Status Updated');
      })
      .catch(error => console.error('Error:', error));
    }
  }
}
</script>

