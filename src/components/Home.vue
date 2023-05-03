<template>
  <div class="container">
    <!-- Clock and Greetings -->
    <div class="timeBlock">
      <div class="clock">{{ time.hours }} : {{ time.minutes }}</div>
      <div class="greetings">{{ greetings }}</div>
    </div>

    <!-- Date and Weather -->
    <div class="weatherBlock">
      <div class="weatherBlockDate">{{ time.month }} {{ time.date }}</div>
      <div class="weatherBlockWeather">
        <div class="weatherIcon">
          <img :src="iconElement" />
        </div>
        <div class="temperatureValue">
          <p class="">{{ tempElement }}{{ tempUnit }}</p>
        </div>
        <div class="temperatureDescription">
          <p class="">{{ descElement }}</p>
        </div>
      </div>
    </div>
  </div>

  <!-- Search Bar -->
  <div class="searchBar">
    <form class="searchForm" action="https://www.google.com/search">
      <input type="text" name="q" placeholder="Search Google" />
    </form>
  </div>

  <div class="container">
    <!-- Buttons Links -->
    <div class="buttonLink">
      <ul>
        <li
          v-for="website in buttonLinkWebsite"
          :key="website.id"
          class="buttonLinkWebsite"
        >
          <a :href="website.url"><img :src="website.icon" /></a>
        </li>
      </ul>
    </div>

    <!-- Lists -->
    <div class="cardList1">
      <ul>
        <img :src="cardListIcon" />
        <li
          v-for="website in cardList1Website"
          :key="website.id"
          class="cardList1Website"
        >
          <a :href="website.url">{{ website.name }}</a>
        </li>
      </ul>
    </div>

    <div class="cardList2">
      <ul>
        <img :src="cardListIcon2" />
        <li
          v-for="website in cardList2Website"
          :key="website.id"
          class="cardList2Website"
        >
          <a :href="website.url">{{ website.name }}</a>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

/* time */
const monthNames = [
  "Jan",
  "Feb",
  "Mar",
  "Apr",
  "May",
  "Jun",
  "Jul",
  "Aug",
  "Sep",
  "Oct",
  "Nov",
  "Dec",
];
const time = ref(<any>[]);
const getTime = () => {
  const t = new Date();
  const month = monthNames[t.getMonth()];
  const date = t.getDate();
  const hours = t.getHours();
  const minutes = t.getMinutes() < 10 ? "0" + t.getMinutes() : t.getMinutes();
  time.value = { month, date, hours, minutes };
};
getTime();
setInterval(() => {
  getTime();
}, 1000);

/* weather */

let iconElement = ref("icons/OneDark/unknown.png");
let tempElement = ref("");
let descElement = ref("");
// App data
interface Weather {
  temperature: {
    unit: string;
    value?: number;
  };
  description?: string;
  iconId?: string;
}
const weather = ref(<any>[]) as Weather;
weather.temperature = {
  unit: "celsius",
};

// change to "F" for Fahrenheit
const tempUnit = "C";
const KELVIN = 273.15;
const LATITUDE = import.meta.env.VITE_LATITUDE;
const LONGITUDE = import.meta.env.VITE_LONGITUDE;
const WEATHER_API_KEY = import.meta.env.VITE_WEATHER_API_KEY;
const api = `https://api.openweathermap.org/data/2.5/weather?lat=${LATITUDE}&lon=${LONGITUDE}&appid=${WEATHER_API_KEY}`;

// Display Weather info
function displayWeather() {
  iconElement = `icons/OneDark/${weather.iconId}.png` as any;
  tempElement = `${weather.temperature.value}°` as any;
  descElement = weather.description! as any;
}

const getWeather = () => {
  // Use an API to get the current weather and set the weather data to the weather variable
  fetch(api)
    .then(function (response) {
      let data = response.json();
      return data;
    })
    .then(function (data) {
      let celsius = Math.floor(data.main.temp - KELVIN);
      weather.temperature.value =
        tempUnit == "C" ? celsius : (celsius * 9) / 5 + 32;
      weather.description = data.weather[0].description;
      weather.iconId = data.weather[0].icon;
    })
    .then(function () {
      displayWeather();
    });
};

getWeather();

/* Links */

interface Website {
  id: number;
  name: string;
  url: string;
  icon: string;
}

const buttonLinkWebsite = ref<Website[]>([
  { id: 0, name: "", url: "", icon: "" },
]);
const cardList1Website = ref<Website[]>([
  { id: 0, name: "", url: "", icon: "" },
]);
const cardList2Website = ref<Website[]>([
  { id: 0, name: "", url: "", icon: "" },
]);

const loadJSON = async (path: string, dataTarget: any) => {
  const response = await fetch(path);
  const data = await response.json();
  dataTarget.value = data;
  // dataTarget = dataTarget as { id: number; name: string; url: string }[];
};

loadJSON("./data/buttonLink.json", buttonLinkWebsite);
loadJSON("./data/cardList1.json", cardList1Website);
loadJSON("./data/cardList2.json", cardList2Website);

const cardListIcon = ref("icons/FeatherIcon/coffee.svg");
const cardListIcon2 = ref("icons/FeatherIcon/film.svg");

/* Greeting */

let greetings = ref("");
const name = "Nigel Liao";
const gree1 = "Go to Sleep!  ";
const gree2 = "Good morning!  ";
const gree3 = "Good afternoon,  ";
const gree4 = "Good evening,  ";

if (time.hours >= 23 && time.hours < 5) {
  greetings = (gree1 + name + ".") as any;
} else if (time.hours >= 6 && time.hours < 12) {
  greetings = (gree2 + name + ".") as any;
} else if (time.hours >= 12 && time.hours < 19) {
  greetings = (gree3 + name + ".") as any;
} else {
  greetings = (gree4 + name + ".") as any;
}
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Open+Sans:wght@300;400;700&display=swap");

/* General Style */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Open Sans", sans-serif;
  font-size: 1.2rem;
  line-height: 1.5;
  transition: 0.2s ease-in-out;
}

.container {
  width: 100%;
  height: 33vh;
  display: grid;
  grid-template-columns: repeat(8, 1fr);
  grid-template-rows: repeat(8, 1fr);
  grid-gap: 20px;
}

/* search bar */
.searchBar {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
  width: 100%;
  height: 100px;
}

.searchForm {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 50px;
  border-radius: 10px;
  background-color: #3b4252;
}

.searchForm input[type="text"] {
  width: 100%;
  height: 100%;
  padding: 0 20px;
  border: none;
  border-radius: 10px;
  font-size: 1.2rem;
  outline: none;
  text-align: center;
  background-color: #2e3440;
}

.searchForm input[type="text"]::placeholder {
  color: #aaa;
}

/* clock and greeting */
.timeBlock {
  grid-column: 1 / span 4;
  grid-row: 1;
  color: #c6d0f5;
}

.clock {
  display: fit-content;
  justify-content: center;
  align-items: center;
  font-size: 11rem;
  font-weight: bolder;
}

.greetings {
  display: fit-content;
  justify-content: center;
  align-items: center;
  margin-left: 0;
  font-size: 2rem;
}

/* date and weather */
.weatherBlock {
  grid-column: 5 / span 8;
  grid-row: 1;
  color: #c6d0f5;
}

.weatherBlockDate {
  display: fit-content;
  justify-content: center;
  align-items: center;
  margin-top: 30px;
  font-size: 8rem;
  font-weight: bolder;
}

.weatherBlockWeather {
  display: flex;
  justify-content: center;
  align-items: center;
}

.weatherIcon {
  width: 4rem;
  height: 7rem;
}

.temperatureValue {
  font-size: 3rem;
  font-weight: bolder;
  margin-left: 40px;
  padding: 20px 0;
}

.temperatureDescription {
  font-size: 2rem;
  margin-left: 15px;
  padding: 20px 0;
}

/* buttons links*/
.buttonLink {
  grid-column: 2;
  grid-row: 1;
}
.buttonLink ul {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(2, 1fr);
  grid-gap: 20px;
  padding: 20px;
}

.buttonLink li {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
}

.buttonLink a {
  display: block;
  justify-content: center;
  align-items: center;
  width: 170px;
  height: 150px;
  padding: 55px;
  border: 1px solid #3b4252;
  background-color: #2e3440;
  text-decoration-color: none;
  transition: all 0.3s ease-in-out;
  box-sizing: border-box;
  border-radius: 7px;
  color: #c6d0f5;
}

.buttonLink a:hover {
  background-color: #5e81ac;
  color: #fff;
}

/* links */

.cardList1 {
  grid-column: 5;
  grid-row: 1;
  border: 1px solid #3b4252;
  background-color: #2e3440;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
  border-radius: 7px;
  padding: 40px;
  width: 270px;
  height: 360px;
  overflow: auto;
}
.cardList2 {
  grid-column: 7;
  grid-row: 1;
  border: 1px solid #3b4252;
  background-color: #2e3440;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
  border-radius: 7px;
  padding: 40px;
  width: 270px;
  height: 360px;
  overflow: auto;
}

.cardList1Website,
.cardList2Website {
  list-style: none;
  margin-bottom: 20px;
}

.cardList1 a,
.cardList2 a {
  margin-bottom: 20px;
  text-decoration: none;
  font-size: 16px;
  color: #c6d0f5;
  margin-top: 1px;
  padding: 6px 12px;
  border-radius: 5px;
  font-weight: bold;
}

.cardList1 a:hover,
.cardList2 a:hover {
  background-color: #5e81ac;
  color: #d8dee9;
}
</style>
