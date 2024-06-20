<template>
    <div class="reservation-list">
        <h2 class="reservation-list-heading">Reservation List</h2>
        <table>
            <thead>
                <tr>
                    <th>User ID</th>
                    <th>Start Date</th>
                    <th>End Date</th>
                    <th>Status</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(reservation, index) in lastTenReservations" :key="index">
                    <td>{{ reservation.user_id }}</td>
                    <td>{{ formatDate(reservation.start_time) }}</td>
                    <td>{{ formatDate(reservation.end_time) }}</td>
                    <td>{{ reservation.status }}</td>
                    <td>
                        <button @click="confirmCancellation(reservation.id)" :disabled="disableCancelButton(reservation.status)">Cancel</button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        props: {
            reservations: Array
        },
        computed: {
            lastTenReservations() {
                return this.reservations.slice(-10); // Ensure it's the last 10
            }
        },
        methods: {
            updateStatus(reservationId, status) {
                fetch(`http://10.10.1.247:5010/api/reservations/${reservationId}/status`, {
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
            },
            confirmCancellation(reservationId) {
                if (confirm('Are you sure you want to cancel?')) {
                    this.updateStatus(reservationId, 'cancelled');
                }
            },
            formatDate(dateTime) {
                const date = new Date(dateTime);
                const options = {
                    year: 'numeric',
                    month: 'long',
                    day: 'numeric'
                };
                return new Intl.DateTimeFormat('en-ID', options).format(date);
            },
            disableCancelButton(status) {
                return status !== 'scheduled';
            }
        }
    }
</script>

<style>
    .reservation-list {
        margin-top: 20px;
        margin-bottom: 20px;
    }

    table {
        width: 100%;
        border-collapse: collapse;
    }

    th, td {
        padding: 8px;
        text-align: left;
        border-bottom: 1px solid #ddd;
    }

    th {
        background-color: coral;
    }

    .reservation-list-heading {
        text-align: center;
    }
</style>
