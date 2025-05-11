
<template>
    <place :user_position="currentPosition" />
  </template>
  
  <script>
  import { watchEffect, ref, defineComponent } from "vue";
  import place from "./place.vue";

  export default {
    components: {
        place,
    },
    setup() {
      const currentPosition = ref({lat: null, lng: null,}); // before location
      const newPosition = ref({lat: null, lng: null,}) // for new location
      const loadGoogleMapsAPI = () => {
        return new Promise((resolve) => {
          if (typeof google !== "undefined") {
            // Google Maps API ถูกโหลดแล้ว
            resolve();
          } else {
            // ยังไม่โหลด ให้โหลด
            const script = document.createElement("script");
            script.src = "https://maps.googleapis.com/maps/api/js?key=AIzaSyDcqf2oTW1ekEhs-0hljFSQ_ENmeQ7-2Qo";
            script.onload = resolve;
            document.head.appendChild(script);
          }
        });
      };
  
      const getCurrentLocation = async () => {
        // โหลด Google Maps API ก่อน
        await loadGoogleMapsAPI();
  
        if (navigator.geolocation) {
          await new Promise((resolve) => {
            navigator.geolocation.getCurrentPosition(
              (position) => {
                currentPosition.value.lat = position.coords.latitude
                currentPosition.value.lng =  position.coords.longitude
                console.log(position.coords.latitude);
                console.log(position.coords.longitude);
                resolve();
              },
              (error) => {
                console.error(error.message);
                resolve();
              }
            );
          });
  
        } else {
          console.error("Geolocation is not supported by this browser.");
        }
      };
      getCurrentLocation();
      console.log(currentPosition);
      watchEffect(() => {
      console.log(currentPosition);
      });
      return {
        currentPosition,
  };
    },
  };
  </script>
  
  <style lang="scss" scoped></style>
  