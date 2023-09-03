<template>
  <div class="booking-form">
    <h1 class="page-title">Book a Room</h1>
    <form @submit.prevent="bookRoom" class="form">
      <!-- Room Selection -->
      <div class="form-group">
        <label for="room" class="form-label">Select Room:</label>
        <select v-model="selectedRoom" class="form-control">
          <option v-for="room in rooms" :key="room.id" :value="room.id">{{ room.name }}</option>
        </select>
      </div>

      <!-- Time Selection -->
      <div class="form-group">
        <label for="start" class="form-label">Start Time:</label>
        <input type="datetime-local" v-model="startTime" class="form-control">
      </div>
      <div class="form-group">
        <label for="end" class="form-label">End Time:</label>
        <input type="datetime-local" v-model="endTime" class="form-control">
      </div>

      <!-- Book Button -->
      <button type="submit" class="btn btn-primary">Book</button>
    </form>

    <!-- Show Response Message -->
    <p v-if="responseMessage" class="response-message">{{ responseMessage }}</p>

    <h2 v-if="conflicts.length > 0" class="conflicts-title">Conflicts with below time:</h2>
    <ul v-if="conflicts.length > 0" class="conflicts-list">
      <li v-for="conflict in conflicts" :key="conflict.id" class="conflict-item">
        Start: {{ conflict.start_time }}, End: {{ conflict.end_time }}
      </li>
    </ul>

    <!-- Display Booked Rooms -->
    <div class="booked-rooms">
      <h2>Booked Rooms:</h2>
      <ul>
        <li v-for="booking in bookedRooms" :key="booking.id">
          <strong>Room Name:</strong> {{ booking.room.name }}<br>
          <strong>Start Time:</strong> {{ booking.start_time }}<br>
          <strong>End Time:</strong> {{ booking.end_time }}<br><br>
        </li>
      </ul>
    </div>
  </div>
</template>

<style>
.booking-form {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f7f7f7;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.page-title {
  font-size: 24px;
  margin-bottom: 20px;
}

.form {
  display: flex;
  flex-direction: column;
}

.form-group {
  margin-bottom: 10px;
}

.form-label {
  font-weight: bold;
}

.form-control {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
}

.btn {
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 10px 20px;
  cursor: pointer;
  font-size: 16px;
}

.response-message {
  margin-top: 10px;
  font-weight: bold;
}

.conflicts-title {
  margin-top: 20px;
  font-size: 20px;
}

.conflicts-list {
  list-style-type: none;
  padding: 0;
}

.conflict-item {
  margin-top: 10px;
}

.no-conflicts-message {
  font-weight: bold;
  margin-top: 10px;
}
</style>
<script>
import axios from "axios";

export default {
  data() {
    return {
      selectedRoom: "",
      startTime: "",
      endTime: "",
      rooms: [],
      conflicts: [],
      responseMessage: "", // Initialize the responseMessage property
    };
  },
  created() {
    this.fetchRooms();
    this.fetchBookedRooms();
  },
  methods: {
    async fetchRooms() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/rooms");
        this.rooms = response.data.data;
      } catch (error) {
        console.error("Error fetching rooms", error);
      }
    },
    async bookRoom() {
      try {
        const bookingData = {
          room_id: this.selectedRoom,
          start_time: this.startTime,
          end_time: this.endTime,
        };

        // Post booking data
        const response = await axios.post(
          "http://127.0.0.1:8000/api/bookings",
          bookingData
        );
        if (response.status === 201) {
          this.responseMessage = "Booking created successfully";
        } else {
          this.conflicts = response.data.data;
          this.responseMessage = "Booking conflicts with existing bookings";
        }
      } catch (error) {
        if (error.response && error.response.status === 409) {
          this.conflicts = [{ room: {}, start_time: "", end_time: "" }]; // Show conflicts message
        } else {
          console.error("Error booking room", error);
        }
      }
    },

    async fetchBookedRooms() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/bookings");
        this.bookedRooms = response.data.data;
      } catch (error) {
        console.error("Error fetching booked rooms", error);
      }
    },
  },
};
</script>