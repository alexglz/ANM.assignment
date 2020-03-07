<template>
  <v-container id="app">
  <v-card style="padding:25px" >
      <h1>Recent Earthquakes Finder</h1>
      <GeoLocator v-on:receiveLocationCoord="sendCoordinates2"/>
      <Maps ref="mapper" :coordinates="coordinates"/>
      <earthquakes  ref="form" v-on:coordinates="mapCoordinates"/>
    
  </v-card>
  </v-container>

</template>

<script>
import Maps from './components/Maps.vue'
import earthquakes from './components/earthquakes.vue'
import GeoLocator from './components/GeoLocator.vue'

export default {
  name: 'App',
  data() {
    return{
      latitude: 0,
      longitude: 0,
      coordinates: [],
      locationGeometry: {}
    }
  },
  methods:{
    sendCoordinates(){
      this.$refs.form.calculateBoundBox()
    },
    sendCoordinates2(coordinates){
      this.locationGeometry = coordinates
      this.coordinates = []
      this.latitude = coordinates.location.lat
      this.longitude = coordinates.location.lng
      window.console.log(this.latitude,this.longitude)
      this.$refs.form.calculateBoundBox(this.latitude,this.longitude,3)
    },
    mapCoordinates(coordinates){ 
      this.coordinates = coordinates
      this.$refs.mapper.createMarkers(this.locationGeometry,coordinates)
    }
  },
  components: {
    Maps,
    earthquakes,
    GeoLocator
  },
  

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  
  color: #2c3e50;
  margin-top: 20px;
}

body{
  background-color: #2c3e50
}
</style>
