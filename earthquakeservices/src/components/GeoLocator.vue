<template>
    <v-container style="padding-top:30px;">     
            <v-row  no-gutters>
                <v-col>
                    <v-text-field  
                    v-model="locationName" 
                    placeholder="Select a City..."
                    :errorMessages="this.errorMsg"
                    
                    >
                    </v-text-field>
                </v-col>
                <v-col md="3" style="padding-top:10px" align="center" justify="center" >
                    <v-btn @click.prevent="getCoordinates" >Search</v-btn>
                </v-col>
            </v-row>
           
    </v-container>
</template>

<script>

import axios from "axios"
const config = require("../config")


export default {
    name: "GeoLocator",
    data(){
        return {
            locationName: "",
            locationGeometry: {},
            errorMsg:"",
            isError: false,
       
        }
    },
    mounted(){
        
    },
    methods:{
        //GetCoordinates
        //La función utiliza axios para hacer un html request a la  API Geocode de Google para obtener las coordenadas de
        //la localización dada por el usuario, posteriormente envia las coordenadas a la vista padre
        getCoordinates(){
            this.isError = false
            this.checkField()
            if(this.isError){
                return;
            }
            const URL = `https://maps.googleapis.com/maps/api/geocode/json?address=${this.locationName}&key=${config.GOOGLEAPI}`
            axios.get(URL)
            .then((response)=>{
                if(!response.data.results.length){
                    this.errorMsg = "Location not found"
                    return;
                }
                let locationData = response.data.results[0]
                let locationCoord = locationData.geometry   
                this.locationGeometry = locationCoord
                this.sendCoordinatesToParent()
            }).catch((error)=>{
                window.alert(error)
            })
        },
        //SendCoordinatesToParent
        //Envía un mensaje a la vista padre en el cual vienen las coordenadas de la ciudad buscada por el usuario
        sendCoordinatesToParent(){
            this.$emit("receiveLocationCoord",this.locationGeometry)
        },
        //CheckField
        //Verifica que el campo de "Ciudad" no esté vacío
        checkField(){
            if(!this.locationName){
                this.isError = true
                this.errorMsg="Required field"
                
            }
            else{
                this.errorMsg = ""
            }
        }
    }
}
</script>

<style>
.v-messages__message{
  color: rgb(255, 0, 0) !important;
  caret-color: rgb(255, 0, 0) !important;
}
</style>