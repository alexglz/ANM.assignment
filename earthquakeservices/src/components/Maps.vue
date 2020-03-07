<template>
        <div class="Map"/>
</template>

<script>
//import axios from 'axios'
import gmaps from "../libraries/googleMaps"

export default {
  name: 'App',
  props: ['coordinates'],
  data(){
    return{
      mapInstance:{},
      google: {},
      locations: [],
      geocoder:{}
    }
  },
  methods:{
    //FUNCION: pinSymbol
    //Parámetros: color, código exadecimal del color del marcador que se desea
    //Descripción: Esta función genera un marcador personalizado según el color que recibe como parámetros
     pinSymbol(color) {
        return {
            path: 'M 0,0 C -2,-20 -10,-22 -10,-30 A 10,10 0 1,1 10,-30 C 10,-22 2,-20 0,0 z',  //Coordenadas de la figura del marcador
            fillColor: color,
            fillOpacity: 1,
            strokeColor: '#000',
            strokeWeight: 2,
            scale: 1.4,
            labelOrigin: new this.google.maps.Point(0,-29),
            initialized: false,
            locationGeometry: {},
    
      };
    },
    async init(){
      try {
        const google = await gmaps();
        const geocoder = new google.maps.Geocoder();
        const map = new google.maps.Map(this.$el);
        this.geocoder= geocoder
        if(!this.initialized){
          geocoder.geocode({ address: 'Mexico' }, (results, status) => {
            if (status !== 'OK' || !results[0]) {
              throw new Error(status);
            }
            map.setCenter(results[0].geometry.location);
            map.fitBounds(results[0].geometry.viewport);
          });
          this.initialized = true
        }else{
          
          let viewport = this.locationGeometry.viewport
          let northeast = new google.maps.LatLng(viewport.northeast.lat,viewport.northeast.lng)      
          let southwest = new google.maps.LatLng(viewport.southwest.lat,viewport.southwest.lng)
          let bounds = new google.maps.LatLngBounds(southwest,northeast)
          map.setCenter(this.locationGeometry.location)
          map.fitBounds(bounds)
        }

        const markerClickEvent = (marker)=>{
          map.setZoom(10);
          map.setCenter(marker.getPosition());
        }

        this.mapInstance = map
        this.google = google
        //this.locations.map(x => new google.maps.Marker({ ...x, map }));
        this.locations.map((location)=>{
          const marker = new google.maps.Marker({...location,map})

          const popUpWindow = new google.maps.InfoWindow({
            content: location.infoWindowText
          })

          marker.addListener('click', function(){
            markerClickEvent(marker)
            popUpWindow.open(map,marker)
          })
          return marker;
        })
      }catch(error) {
        console.error(error);
      }
    },
    createMarkers(origin,coordinates){  
      this.locationGeometry = origin
      this.locations = []
      coordinates.forEach((earthquake)=>{
        //Contenido en HTML que se mostrará en la ventana de información de cada marcador
        let windowText =  `
          <h3><i>(latitude: ${earthquake.lat}, longitude: ${earthquake.lng})</i></h3>
          <ul>
            <li>Date: ${earthquake.datetime}</li>
            <li>Depth: ${earthquake.depth}</li>
            <li>Magnitude (Richter): ${earthquake.magnitude}</li>
            
          </ul>
        `

        this.locations.push({
          position:{
            lat:earthquake.lat,
            lng:earthquake.lng
          },
          icon: this.pinSymbol(this.getMagnitudeColor(earthquake.magnitude)),
          label: {text:earthquake.magnitude.toString(),fontFamily:"Calibri"},
          infoWindowText: windowText
        })
      })
      this.init()
    },
    //GetMagnitudeColor
    //Parámetros: magnitude: Recive un valor numérico que dentro de las escala de richter
    //Esta funcion regresa un código de color en hexadecimal que representa la intensidad de la escala de richter recibida como parámetros
    getMagnitudeColor(magnitude){
      if(magnitude<2){
        return "#73fab2"
      }else if(magnitude<3){
        return "#00af52"
      }else if(magnitude<4){
        return "#93D151"
      }else if(magnitude<5){
        return "#D8F78D"
      }else if(magnitude<6){
        return "#D6F690"
      }else if(magnitude<7){
        return "#FFFF02"
      }else if(magnitude<8){
        return "#FE8F3C"
      }else if(magnitude>=8){
        return "#FE0000"
      }
    },
    
  },
  async mounted(){
    this.init()
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .Map{
    height: 65vh;
  }
</style>
