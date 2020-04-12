<template>
  <div id="app">
    <h1 v-if="value">{{value.location.name}}</h1>
    <div class="grid">
    <img v-if="value" v-bind:src="value.current.weather_icons" >
    <h2 v-if="value">{{value.current.temperature}} Degrees</h2>
    </div>
      <div v-if="errorStr">
    Sorry, but the following error
    occurred: {{errorStr}}
  </div>
  
  <div v-if="gettingLocation">
    <i>Getting your location...</i>
  </div>
  
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'App',
  components: {
  },
  data(){
    return{
      value : "",
          location:null,
    gettingLocation: false,
    errorStr:null
    }
  },
  mounted() {
    //do we support geolocation
    if(!("geolocation" in navigator)) {
      this.errorStr = 'Geolocation is not available.';
      return;
    }

    this.gettingLocation = true;
    // get position
    navigator.geolocation.getCurrentPosition(pos => {
      this.gettingLocation = false;
      this.location = pos;
      axios.get(`http://api.weatherstack.com/current?access_key=c1fc6820a69e9e0cb258257e136e45e8&query=${pos.coords.latitude},${pos.coords.longitude}&units=f`)
    .then(response => {
      // JSON responses are automatically parsed.
      this.value = response.data
    })

    }, err => {
      this.gettingLocation = false;
      this.errorStr = err.message;
    })
  }
}
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
.grid{
  display: flex;
  width: max-content;
  margin: 0 auto;
  align-items:center;
}
img{
  height: 50px;
  border-radius: 5px;
  margin-right: 10px;
}
</style>
