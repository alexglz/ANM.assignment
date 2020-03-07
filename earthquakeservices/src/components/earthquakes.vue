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
        //CalculateBoundBox
        //Parámetros: 1-Latitude: Latitud del punto de búsqueda de terremotos, 2-Longitude: Longitudo del punto de búsqueda de terremotos
        //3- size: Tamaño en grados en el que se expanderá el area de búsqueda de terremotos a partir del punto inicial
        calculateBoundBox(latitude,longitude,size){
            window.console.log(latitude,longitude)
            this.north = latitude + size;
            if(this.north>90) this.north=90
            this.south = latitude - size;
            if(this.south<-90) this.south=-90
            this.east = longitude + size;
            if(this.east>180) this.east=180
            this.west = longitude - size;
            if(this.west<-180) this.west=-180
            window.console.log(this.north,this.south,this.east,this.west)
            this.getEarthQuakes()
        },
        //getEarthQuakes
        //Esta función utiliza la librería axios para hacer un HTML request y obtener la información de los terremotos según el conjunto de coordendadas
        //calculado previamente en la función calculateBoundBox
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
    
}
</script>
