<template>
  <div class="flex flex-col h-screen max-h-screen">
    <!-- Search / Results -->
    <div
      class="z-20 flex justify-center relative bg-hero-pattern bg-cover px-4 pt-8 pb-32"
    >
      <!-- Search Input -->
      <div class="w-full max-w-screen-sm">
        <h1 class="text-white text-center text-3xl pb-4">IP Address Tracker</h1>
        <div class="flex">
          <input
            v-model="queryIp"
            class="flex-1 py-3 px-2 rounded-tl-md rounded-bl-md focus:outline-none"
            type="text"
            placeholder="Search for any IP address or leave empty to get your ip info"
          /> 
         <i
         @click="getIpInfo"
            class="cursor-pointer
          bg-black
          text-white
          px-4
          rounded-tr-md
          rounded-br-md
          flex
          items-center
          fas fa-chevron-right"
          ></i>
        </div>
      </div>
      <!-- IP Info -->
      <IpInfo v-if="IpInfo" :ipInfo="IpInfo"/>
    </div>

  <!-- Map -->
    <div id="mapid" class="h-full z-10"></div>

  </div>
</template>

<script>
// @ is an alias to /src
import IpInfo from '../components/IpInfo.vue'
import leaflet from 'leaflet'
import axios from 'axios'
import {onMounted, ref} from 'vue'


export default {
  name: 'Home',
  components: {
    IpInfo
  },
  setup(){
    let mymap;
    const queryIp = ref("")
    const IpInfo = ref(null)
    onMounted(()=> {
      mymap = leaflet.map('mapid').setView([51.505, -0.09], 13);

    leaflet.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoiNXAzNHIiLCJhIjoiY2ttN2U0YjBiMGF4bDJ3bnVhZGNkMmwzdyJ9.zs9L5yBQMd5LoyBPPVkoag', {
    attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
    maxZoom: 18,
    id: 'mapbox/streets-v11',
    tileSize: 512,
    zoomOffset: -1,
    accessToken: 'pk.eyJ1IjoiNXAzNHIiLCJhIjoiY2ttN2U0YjBiMGF4bDJ3bnVhZGNkMmwzdyJ9.zs9L5yBQMd5LoyBPPVkoag'
}).addTo(mymap);
    })
    const getIpInfo = async () => {
      try {
          const data = await axios.get(`https://geo.ipify.org/api/v2/country,city?apiKey=at_DCyjo9jc25bUIUxI2e4dGX1FruiaU&ipAddress=${queryIp.value}`)
          const result = data.data;
          // console.log(result)
          IpInfo.value = {
          address: result.ip,
          state: result.location.region,
          timezone: result.location.timezone,
          isp: result.isp,
          lat: result.location.lat,
          lng: result.location.lng,
        };
        leaflet.marker([IpInfo.value.lat, IpInfo.value.lng]).addTo(mymap);
        mymap.setView([IpInfo.value.lat, IpInfo.value.lng], 9);
      }
      catch(error) {
        alert(error.message)
      }
    }
    
    return {queryIp, IpInfo, getIpInfo}
  },
}
</script>
