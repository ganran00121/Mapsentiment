
<template>
  <goooglemap :position_api="position" />
  <div v-for="item in photo_api " :key="item">
  </div>
  <section class="articles">
    <article v-for="item in photo_api" :key="item">
      <div class="article-wrapper">
        <figure>
          <img
            :src="`https://maps.googleapis.com/maps/api/place/photo?photoreference=${item.photo_reference}&sensor=false&maxheight=1000&maxwidth=1000&key=AIzaSyCqkOQW0vrz8Lvc7GRjnJ8o_YIYFMa6gxs`"
            alt="" />
        </figure>
        <div class="article-body">
          
          <h2>{{ item.title }}</h2>
          <div style="position: start; margin: 0px;">
          <!-- <p>lat lon : {{ item.lat }} , {{ item.lng }}  </p> -->
          <p>Distance : {{ item.distance }} km </p>
          <p>Score : {{ item.score }} rating</p>
          <p>
            algo : {{ item.algo }} Pts.
          </p>
        </div>
          <a href="#" class="read-more">
            <span class="sr-only"></span>
            <svg xmlns="http://www.w3.org/2000/svg" class="icon" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd"
                d="M12.293 5.293a1 1 0 011.414 0l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414-1.414L14.586 11H3a1 1 0 110-2h11.586l-2.293-2.293a1 1 0 010-1.414z"
                clip-rule="evenodd" />
            </svg>
          </a>
        </div>
      </div>
    </article>
  </section>
</template>

<script>
import { ref, onMounted, watchEffect } from 'vue';
import axios from 'axios';
import goooglemap from "./Map/GoogleMaps.vue";

export default {
  name: "PlaceID",
  components: {
    goooglemap,
  },
  props: {
    user_position: {
      type: Array,
      default: () => [],
    },
  },
  setup(props) {
    const responseData = ref(null);
    const data_review = ref(null);
    const position = ref(null);
    const photo_api = ref(null);

    const Distance = (lat1, lon1, lat2, lon2) => {
      const R = 6371; //รัศมี โลก
      // Haversine formula
      // dlat, dlon ความแตกต่างจากเส้นศูนย์สูตร แนวนอน,แนวตั้ง
      const dLat = (lat2 - lat1) * (Math.PI / 180);
      const dLon = (lon2 - lon1) * (Math.PI / 180);
      // a สูตรของ Haversine คำนวณจาก dlat, dlon
      const a =
        Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos(lat1 * (Math.PI / 180)) *
        Math.cos(lat2 * (Math.PI / 180)) *
        Math.sin(dLon / 2) *
        Math.sin(dLon / 2);
      // Haversine c ค่าความยาวของเส้นที่ผ่านในระยะบนผิวโลกระหว่าง จุด 2จุด ทีไ่ด้จาก  a
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
      //หาระยะทาง รัศมี * C
      const distance = R * c;

      return distance

    }

    const fetchData = async (userPosition) => {
      try {
        console.log("user :::::: ", userPosition.lat);
        console.log("user :::::: ", userPosition.lng);
        const response = await axios.get(`https://maps.googleapis.com/maps/api/place/nearbysearch/json?location=${userPosition.lat},${userPosition.lng}&radius=10000&type=tourist_attraction&key=AIzaSyCqkOQW0vrz8Lvc7GRjnJ8o_YIYFMa6gxs`);
        responseData.value = response.data;
        console.log("all API GET : ", response.data);
        // this not place_id this posiiton
        position.value = responseData.value.results.map((data) => {
          return {
            lat: data.geometry.location.lat,
            lng: data.geometry.location.lng,
          };
        });
        position.value.push({
          lat: userPosition.lat,
          lng: userPosition.lng,
        });
        photo_api.value = await Promise.all(responseData.value.results.map(async (data) => {
          const distance = Distance(userPosition.lat, userPosition.lng, data.geometry.location.lat, data.geometry.location.lng).toFixed(2);
          var score = (await reviews(data.place_id)).toFixed(4);
          score = parseFloat(score);
          const algo = algorithms(distance, score);
          return {
            title: data.name,
            photo_reference: data.photos[0].photo_reference,
            distance: distance,
            score: 10 - (score.toFixed(2)),
            algo: algo.toFixed(2)
          };
        })).then((results) => {
          results = results.sort((a, b) => a.algo - b.algo);
          // console.log("rank",results);
          return  results
        });

        
        // console.log("ALL photo api : ", photo_api);
      } catch (error) {
        console.error(error);
      }
    };
    const algorithms = (distance, score) => {
      distance = parseFloat(distance);
      score = parseFloat(score);
      return (distance * 0.3) + (score * 0.7)
    }

    const reviews = async (place) => {
      try {
        const response = await axios.get(`https://maps.googleapis.com/maps/api/place/details/json?place_id=${place}&fields=name,rating,reviews&key=AIzaSyCqkOQW0vrz8Lvc7GRjnJ8o_YIYFMa6gxs`);

        var Score_Reviews = 0
        var review_number = 0
        const All_Review = await Promise.all(response.data.result.reviews.map(async (data) => {
          // console.log(data.text);
          var result = await sentiment(data.text)
          Score_Reviews += result
          review_number += 1
        }))

        // console.log("Score :", Score_Reviews / review_number);
        // console.log("Name :", response.data.result.name);
        return Score_Reviews / review_number

      } catch (error) {
        console.error(error);
      }
    };

    // SENTIMENT 
    const sentiment = async (reviews) => {
      try {
        const response = await axios.get("https://api.aiforthai.in.th/ssense", {
          headers: {
            "Content-Type": "application/x-www-form-urlencoded",
            Apikey: "nn6tZctdSvv8RASCk8oU62F0QWTbSB7s"
          },
          params: {
            text: reviews
          }
        });

        // console.log("sentiment  : ", response.data);
        const score = response.data.sentiment.score;
        const polarity = response.data.sentiment.polarity;

        let polaScore = 0;
        if (polarity === "positive") {
          polaScore = (100 - score) / 20;
        } else if (polarity === "negative") {
          polaScore = score / 20 + 5;
        } else {
          polaScore = 5;
        }

        return polaScore;
      } catch (error) {
        console.log(error);
        return 5; // ถ้าเกิด error ให้ส่งค่า 5 กลับไป
      }
    };


    watchEffect(() => {
      fetchData(props.user_position);
    });

    return {
      Distance,
      data_review,
      responseData,
      position,
      photo_api
    };
  },
};
</script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Kanit:wght@100;400&family=Prompt:wght@600&display=swap');
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500&display=swap");

* {
  font-family: "Inter", sans-serif ;
  font-weight: 500; /* เพิ่ม property เพื่อให้เป็น font-medium */
}
h2{
  font-family: 'Kanit', sans-serif!important;
  font-family: 'Prompt', sans-serif!important; 
  font-weight: 600 !important;
}
#app {
  text-align: center;
  color: #2c3e50;
}
</style>
