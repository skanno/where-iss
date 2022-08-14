<template>
  <div style='text-align: left;'>
    <p>最終更新: {{data.lastupdate}}</p>
    <div id="map"></div>
  </div>
</template>

<script>
import axios from 'axios';
import { reactive, onMounted } from 'vue';

export default {
  name: 'Map',

  setup(props) {
    const API_ENDPOINT = 'http://api.open-notify.org/iss-now.json';
    var map;
    const data = reactive({
      lastupdate: 'test'
    });

    const initMap = () => {
      var options = {
        mapTypeId: google.maps.MapTypeId.SATELLITE,
        disableDefaultUI: true,
        backgroundColor: '#000000',
        keyboardShortcuts: false,
        zoom: 1,
        center: new google.maps.LatLng(35.709984, 139.810703)
      };
      map = new google.maps.Map(document.getElementById('map'), options);

      setMark();
    };

    const setMark = async () => {
      const result = await axios.get(API_ENDPOINT);
      var marker = new google.maps.Marker({
          position: {
            lat: parseFloat(result.data.iss_position.latitude),
            lng: parseFloat(result.data.iss_position.longitude)
          },
          map: map,
          title: 'Here!'
      });
      var date = new Date(result.data.timestamp * 1000);
      data.lastupdate = `${date.toLocaleDateString('ja-JP')} ${date.toLocaleTimeString('ja-JP')}`;
    };

    onMounted(() => {
      initMap();
      setInterval(() => {
        setMark();
      }, 1000);
    });

    return {data};
  }
}
</script>

<style>
#map {
  width:620px;
  height:400px;
}
</style>