<template>
  <div class="flex flex-col h-screen max-h-screen">
   <div class="flex z-20 justify-center relative bg-hero-pattern bg-cover px-4 pt-8 pb-32 ">
    <div class="w-full max-w-screen-sm">
      <h1 class="text-white text-center text-3xl pb-4">IP Address Tracker</h1>
      <div class="flex">
        <input
         class="flex-1 py-3 px-2 rounded-tl-md rounded-bl-md focus:outline-none" 
        type="text"
         placeholder="Search for any IP address or leave empty to get your ip info">
         <i @click="getIpInfo" class="cursor-pointer bg-black text-white px-4 rounded-tr-md rounded-br-md  flex items-center fas fa-chevron-right"></i>
      </div>
    </div>
    <IPInfo :ipInfo="ipInfo" v-if="ipInfo"/>
   </div>
    <div id="map" class="h-full z-10"></div>
  </div>
</template>

<script>
import IPInfo from '../components/IPInfo.vue';
import leaflet from 'leaflet'
import axios from 'axios'
import { onMounted, ref } from 'vue';
export default {
  name: 'Home',
  components: {
   IPInfo
  },
  setup(){
    let mymap;

    const queryIp = ref('');
    const ipInfo = ref(null);

    onMounted(() => {
       mymap = leaflet.map('map').setView([51.505, -0.09], 9);

       leaflet
  .tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png?access_token =pk.eyJ1Ijoic2hvaHJ1aDEyMyIsImEiOiJjbDZxd2tmcngwNTRoM2RxdGU2aDVrMHRoIn0.HUNZjiEWNVy1qYQhUFVOaA', {
    maxZoom: 19,
    attribution: '© OpenStreetMap',
            id: "mapbox/streets-v11",
            tileSize: 512,
            zoomOffset: -1,
            accessToken:
              "pk.eyJ1Ijoic2hvaHJ1aDEyMyIsImEiOiJjbDZxd2tmcngwNTRoM2RxdGU2aDVrMHRoIn0.HUNZjiEWNVy1qYQhUFVOaA",
}).addTo(mymap);
    })
    const getIpInfo = async () => {
      try {
       const data = await axios.get(`https://geo.ipify.org/api/v2/country?apiKey=at_CgVMysJqqJlIovuQKMFb5Cr7RsotL&ipAddress=${queryIp.value}`)
      const result = data.data;
        ipInfo.value = {
          address: result.ip,
          state: result.location.region,
          timezone: result.location.timezone,
          isp: result.isp,
          lat: result.location.lat,
          lng: result.location.lng,
        };
        leaflet.marker([ipInfo.value.lat, ipInfo.value.lng]).addTo(mymap);
        mymap.setView([ipInfo.value.lAT, ipInfo.value.lng], 13);
      } catch (err) {
        alert(err.message);
      }
    }
    return {
      queryIp,
      ipInfo,
      getIpInfo
    }
  }
}
</script>
