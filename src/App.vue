<template>
    <div id="app" class="container">
        <div class="form-box">
            <reservation-form @reservation-created="fetchReservations"></reservation-form>
        </div>
        <div class="list-box">
            <reservation-list :reservations="reservations" @status-updated="fetchReservations"></reservation-list>
        </div>
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
                fetch('https://backend.testbed-itb.my.id/api/reservations')
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

<style>
    #app {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh; /* Set the height of the container to full viewport height */
    }

    .container {
        display: flex;
        width: 100%; /* Ensure the container takes up the full width */
        height: 100%; /* Set the height of the container to full viewport height */
    }

    .form-box,
    .list-box {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    .form-box {
        flex: 1; /* Covering 1/3 of the screen */
        height: 100%; /* Adjust the height of the form */
        width: 100%; /* Adjust the width of the form */
        border-radius: 10px; /* Add border-radius for rounded corners */
        padding: 20px; /* Add padding for better spacing */
    }


    .list-box {
        flex: 2; /* Covering 2/3 of the screen */
        overflow-y: auto; /* Add scroll if needed */
        height: 100%; /* Adjust the height of the list */
    }

    reservation-list {
        width: 100%; /* Ensure the list occupies full width */
    }
</style>
