<template>
    <div></div>
</template>

<script>
//La librería axios permite hacer HTML requests al API
import axios from 'axios'
const config = require("../config")

export default {
    props:['latitude',"longitude"],
    data(){
        return{
            north: 44.1,
            south:-9.9,
            east: -22.4,
            west: 55.2,
            coordinates: []
        }
    },
    methods:{
        submit(){
            window.console.log(this.item)
        },
        calculateBoundBox(latitude,longitude,size){
            window.console.log(latitude,longitude)
            size = 3
            this.north = latitude + size;
            this.south = latitude - size;
            this.east = longitude + size;
            this.west = longitude - size;
            window.console.log(this.north,this.south,this.east,this.west)
            this.getEarthQuakes()
        },
        getEarthQuakes(){
            let URL = `https://secure.geonames.org/earthquakesJSON?north=${this.north}&south=${this.south}&east=${this.east}&west=${this.west}&username=${config.GEOUSERNAME}`
            axios.get(URL)
                .then((response)=>{
                    window.console.log(response.data)
                    response.data.earthquakes.forEach((earthquake)=>{
                        this.coordinates.push({lat:earthquake.lat,lng:earthquake.lgn})                       
                    })
                    
                    this.coordinates = response.data.earthquakes
                    //window.console.log(this.coordinates)
                    //Enviar el arreglo de coordenadas a la vista Padre/Principal
                    this.$emit("coordinates",this.coordinates)
                })
                .catch((error)=>{
                    window.console.log(error)
                })
        }
    },
    mounted(){
        //this.getEarthQuakes()
        
    },
    
}
</script>
