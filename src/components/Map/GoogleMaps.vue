<template>
  <div>
    <div id="map" style="margin-bottom: 2%;"></div>
    <div style="color: rgb(174 194 211 / 0%);">{{ place_id }}</div>
  </div>
</template>

<script>
import icon from '../../assets/icon.png';

export default {
  name: "GoogleMaps",
  props: {
    position_api: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      map: null,
    };
  },
  watch: {
    position_api: {
      immediate: true,
      handler(newVal) {
        console.log("place in google map: ", newVal);
        this.initMap(newVal);
      },
    },
  },
  methods: {
    initMap(placeDetails) {
      const googleMapsScript = document.createElement("script");
      googleMapsScript.src = `https://maps.googleapis.com/maps/api/js?key=${import.meta.env.VITE_APP_GOOGLE_MAPS_API_KEY
        }&libraries=places&callback=initGoogleMap`;
      document.head.appendChild(googleMapsScript);

      window.initGoogleMap = () => this.initGoogleMap(placeDetails);
    },
    initGoogleMap(placeDetails) {
      // ให้ใช้ place_id ที่ได้รับมาแทนที่ตำแหน่งที่กำหนดไว้ล่วงหน้า
      const lastIndex = placeDetails.length - 1;
      const centerPosition = placeDetails[lastIndex]; // ใช้ตำแหน่งที่ตัวสุดท้ายของ array

      this.map = new window.google.maps.Map(document.getElementById("map"), {
        center: {
          lat: centerPosition.lat,
          lng: centerPosition.lng,
        },
        zoom: 12,
      });
      https://raw.githubusercontent.com/Concept211/Google-Maps-Markers/master/images/marker_green3.png
      // ให้ใช้ place_id ที่ได้รับมาแทนที่ตำแหน่งที่กำหนดไว้ล่วงหน้า
      placeDetails.forEach((position, index) => {
        const markerOptions = {
          position: {
            lat: position.lat,
            lng: position.lng,
          },
          
          map: this.map,
        };
        // เพิ่ม Icon สำหรับ Marker ที่ตำแหน่งที่ตัวสุดท้ายของ array
        if (index === lastIndex) {
          markerOptions.icon = {
            url: icon,
            scaledSize: new window.google.maps.Size(60, 60), // ปรับขนาดตามที่คุณต้องการ
          };
        }else {
          markerOptions.label= {
            text: `${index+1}`,
            color: "#ffffff",
            fontSize: "12px",
          };
        }
      
        new window.google.maps.Marker(markerOptions);
      });

    },
  },
};
</script>

<style>
#map {
  height: 600px;
  width: 100%;
  border-radius: 6px;
}
</style>
