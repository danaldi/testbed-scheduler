<template>
    <div class="reservation-form">
        <h2>Create Reservation</h2>
        <form @submit.prevent="submitForm">
            <div class="form-group">
                <label for="user_id">User ID:</label>
                <input type="text" id="user_id" v-model="reservation.user_id" required class="form-input">
            </div>
            <div class="form-group">
                <label for="email">Email:</label>
                <input type="email" id="email" v-model="reservation.email" required class="form-input">
            </div>
            <div class="form-group">
                <label for="start_date">Start Date:</label>
                <input type="date" id="start_date" v-model="reservation.start_date" required class="form-input">
            </div>
            <div class="form-group">
                <label for="end_date">End Date:</label>
                <input type="date" id="end_date" v-model="reservation.end_date" required class="form-input">
            </div>
            <button type="submit" class="form-button" :disabled="isLoading">Create Reservation</button>
            <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
        </form>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                reservation: {
                    user_id: '',
                    email: '',
                    start_date: '',
                    end_date: ''
                },
                errorMessage: '',
                isLoading: false,  // New loading state property
            };
        },
        methods: {
            generateRandomPassword() {
                const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
                let result = '';
                const charactersLength = characters.length;
                for (let i = 0; i < 5; i++) {
                    result += characters.charAt(Math.floor(Math.random() * charactersLength));
                }
                return result;
            },
            submitForm() {
                const startDate = new Date(this.reservation.start_date);
                startDate.setHours(0, 0, 0, 0);  // Normalize the start date to the beginning of the day

                const endDate = new Date(this.reservation.end_date);
                endDate.setHours(0, 0, 0, 0);  // Normalize the end date to the beginning of the day

                const today = new Date();
                today.setHours(0, 0, 0, 0);  // Normalize today's date to the beginning of the day

                // Check if start date is today or in the future
                if (startDate < today) {
                    this.errorMessage = 'Please select a start date that is today or in the future';
                    return;
                }

                // Check if end date is in the past
                if (endDate < today) {
                    this.errorMessage = 'Please select an end date in the future';
                    return;
                }

                // Calculate the duration in milliseconds
                const duration = endDate.getTime() - startDate.getTime();

                // Convert milliseconds to days
                const durationInDays = duration / (1000 * 60 * 60 * 24);

                if (durationInDays > 3) {
                    this.errorMessage = 'Maximum reservation duration is 3 days';
                    return;
                }

                const password = this.generateRandomPassword();

                const payload = {
                    user_id: this.reservation.user_id,
                    email: this.reservation.email,
                    start_time: this.reservation.start_date,
                    end_time: this.reservation.end_date,
                    password: password
                };

                this.isLoading = true;  // Disable the button

                fetch('https://backend.testbed-itb.my.id/api/reservations', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(payload)
                })
                    .then(response => response.json())
                    .then(data => {
                        this.isLoading = false;  // Enable the button

                        if (data.error) {
                            this.errorMessage = data.error;
                        } else {
                            this.errorMessage = '';
                            alert('Reservation Created Successfully');
                            this.$emit('reservation-created', data);
                        }
                    })
                    .catch(error => {
                        console.error('Error:', error);
                        this.errorMessage = 'Failed to connect to the service';
                        this.isLoading = false;  // Enable the button in case of error
                    });
            }
        }
    }
</script>

<style scoped>
    .reservation-form {
        max-width: 400px;
        margin: 0 auto;
        padding: 20px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    .form-group {
        margin-bottom: 20px;
    }

    label {
        display: block;
        margin-bottom: 5px;
    }

    .form-input, .form-button {
        width: 100%;
        padding: 8px;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
    }

    .form-button {
        background-color: coral;
        border: solid 0.5px;
        color: black;
        cursor: pointer;
        margin-top: 10px;
    }

    .form-button:hover {
        background-color: lightcoral;
    }

    .form-button:disabled {
        background-color: #ddd;
        cursor: not-allowed;
    }

    .error-message {
        color: red;
        margin-top: 10px;
    }
</style>
