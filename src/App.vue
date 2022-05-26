<template>
  <div v-if="location">
    <form @submit.prevent="">
      <input
        v-model.lazy="search"
        placeholder="Search for a city"
        @change="findMe"
      />
    </form>
    <div v-if="errorMessage" class="error">
      {{ errorMessage }}
    </div>
    <div class="card">
      <h2>{{ city }}</h2>
      <h3>{{ country }}</h3>
      <h3>
        {{ textCondition }}
        <span>
          Wind {{ wind }}km/h <span class="dot">•</span> Humidity
          {{ humidity }}%
        </span>
      </h3>
      <div class="temp-wrapper">
        <img class="icon" :src="iconCondition" alt="icon-weather" />
        <h1>{{ temp }}°</h1>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref } from "@vue/reactivity";

const keyAPI = '885071ef04cf4336bc8171930222305';

const search = ref(null);
const temp = ref(null);
const wind = ref(null);
const humidity = ref(null);
const textCondition = ref(null);
const iconCondition = ref("/");
const city = ref(null);
const country = ref(null);
const lat = ref(null);
const lon = ref(null);
const location = ref(null);
const errorMessage = ref(null);

const shouldUseGeolocation = () => !search.value && navigator.geolocation;

const findMe = () => {
  if (shouldUseGeolocation) {
    navigator.geolocation.getCurrentPosition(geoPosition, geoError);
  }
};

findMe();

function geoPosition(position) {
  lat.value = position.coords.latitude;
  lon.value = position.coords.longitude;
  setLocation(`${lat.value},${lon.value}`);
}

function geoError() {
  setLocation("London");
}

const setLocation = (point) => {
  location.value = search.value ? search.value : point;
  getWeather();
};

const getWeather = async () => {
  try {
    const response = await axios.get(
      `https://api.weatherapi.com/v1/current.json?key=${keyAPI}&q=${location.value}`
    );
    console.log(response.data);

    temp.value = Math.round(response.data.current.temp_c);
    wind.value = response.data.current.wind_kph;
    humidity.value = response.data.current.humidity;
    textCondition.value = response.data.current.condition.text;
    iconCondition.value = response.data.current.condition.icon;
    city.value = response.data.location.name;
    country.value = response.data.location.country;
    errorMessage.value = null;
  } catch (error) {
    console.log(error);
    errorMessage.value = "No city with that name...";
  }
};
</script>



<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,300;0,400;1,300&display=swap");

html,
body {
  background-color: #f3f3f3;
  font-family: "Roboto", sans-serif;
}

form {
  display: flex;
  justify-content: center;
  margin: 40px 0;
  animation: up 2s cubic-bezier(0.39, 0, 0.38, 1) 0.2s;
}

input {
  padding: 15px;
  width: 315px;
  font-size: 15px;
  font-style: italic;
  box-shadow: 1px 2px 10px rgba(0, 0, 0, 0.2);
}

.error {
  color: red;
  display: flex;
  justify-content: center;
}

.card {
  margin: 0 auto;
  margin-top: 5%;
  padding: 5px 30px;
  width: 290px;
  height: 385px;
  border-radius: 3px;
  background-color: #fff;
  box-shadow: 1px 2px 10px rgba(0, 0, 0, 0.2);
  animation: open 2s cubic-bezier(0.39, 0, 0.38, 1);
}

@keyframes open {
  from {
    padding: 0 30px;
    height: 0;
  }
  to {
    height: 385px;
  }
}

h1,
h2,
h3,
h4 {
  position: relative;
}

h1 {
  color: #666;
  font-weight: 300;
  font-size: 6.59375em;
  line-height: 0.2em;
  animation: up 2s cubic-bezier(0.39, 0, 0.38, 1) 0.2s;
}

h2 {
  font-weight: 300;
  font-size: 2.25em;
  animation: up 2s cubic-bezier(0.39, 0, 0.38, 1);
}

h3 {
  display: flex;
  justify-content: space-between;
  color: #777;
  font-weight: 400;
  font-size: 1em;
  animation: up 2s cubic-bezier(0.39, 0, 0.38, 1) 0.1s;
}

.temp-wrapper {
  display: flex;
  justify-content: space-between;
  align-items: center;
  animation: up 2s cubic-bezier(0.39, 0, 0.38, 1) 0.1s;
}

span {
  color: #999;
  font-weight: 300;
}

.dot {
  font-size: 0.9em;
}

@keyframes up {
  0% {
    opacity: 0;
    transform: translateY(15px);
  }
  50% {
    opacity: 0;
    transform: translateY(15px);
  }
  99% {
    animation-play-state: paused;
  }
  100% {
    opacity: 1;
  }
}
</style>
