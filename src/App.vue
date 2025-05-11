<template>
  
  <div id="app">
    <button @click="change_data(false)" style="margin: 5px;"> Dashboard </button>
    <button @click="change_data(true)" style="margin: 5px;"> Acc Sentiment </button>
    <acc_sentiment v-if="showtest == true" />
    <position v-if="showtest == false"/>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import place from './components/place.vue';
import position from './components/Userposition.vue';
import acc_sentiment from './components/acc_sentiment.vue';



export default {
  name: 'App',
  components: {
    acc_sentiment,
    position
  },
  setup() {
        const showtest = ref(false);
        const change_data = (value) => {
          showtest.value = value
        }
        onMounted(async () => {
            change_data(false);
        });

        return {
            showtest,
            change_data
        };
    },
};
</script>

<style>
#app {
  /* font-family: Avenir, Helvetica, Arial, sans-serif; */
  text-align: center;
  color: #2c3e50;
}
</style>
