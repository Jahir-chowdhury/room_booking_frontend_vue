<template>
  <div id="app">
    <booking-room></booking-room>
  </div>
</template>


<script>
import BookingRoom from './components/BookingRoom.vue';

export default {
  components: {
    BookingRoom
  }
};
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
