<template>
  <div id="v-model-select" class="demo">
    <select v-model="selected" @change="onChange($event)">
      <option disabled value="">Please select one</option>
      <option
        v-for="city in $options.cities"
        :key="city.id"
        :value="
          city.id + ` ${city.coord.lat.toString()} ${city.coord.lon.toString()}`
        "
      >
        {{ city.name + `, ${city.country}` }}
      </option>
    </select>
  </div>
  <template v-if="state.clouds">
    <h2>Clouds:</h2>
    <p>{{ state.clouds }}</p>
    <h2>Temperature:</h2>
    <p>{{ state.temperature }}&#176;C</p>
    <h2>Wind speed:</h2>
    <p>{{ state.wind }} m/s</p>
  </template>

  <button @click="getForecast">Get forecast</button>
  <template v-if="!state.hide">
    <button @click="state.hide = true">Show/hide forecast</button>
      <table style="width: 100%">
        <tr>
          <th>Time</th>
          <th>Temp</th>
          <th>Feels Like</th>
          <th>Wind Speed</th>
          <th>Humidity</th>
        </tr>
        
        <tr v-for="hourly in state.data.hourly" :key="hourly.dt" :value="hourly.dt">
          <td>{{hourly.dt}}</td>
          <td>{{hourly.temp}}&#176;C</td>
          <td>{{hourly.feels_like}}&#176;C</td>
          <td>{{hourly.wind_speed}}m/s</td>
          <td>{{hourly.humidity}}%</td>
        </tr>
      </table>
  </template>
</template>

<script>
import cityListJson from "../assets/cities.json";
import axios from "axios";

export default {
  cities: cityListJson,
};
</script>
<script setup>
import { defineProps, reactive } from "vue";
defineProps({
  selected: "",
});
const state = reactive({
  clouds: "",
  temperature: "",
  wind: "",
  coord: "",
  data: "",
  hide: true,
  numButtons: 0,
  hourlyForecast: {},
  clickedButton: ""
});
const onChange = (event) => {
  console.log(event.target.value);
  const params = event.target.value.split(" ");

  state.coord = {
    lat: params[1],
    lon: params[2],
  };
  axios
    .get(
      `https://api.openweathermap.org/data/2.5/onecall?lat=${params[1]}&lon=${params[2]}&exclude=minutely&appid=538882fc8387290c6cee83f313a6acf5&units=metric`
    )
    .then((response) => {
      state.clouds = response.data.current.weather[0].description;
      state.temperature = response.data.current.temp;
      state.wind = response.data.current.wind_speed;
      console.log(response.data);

      state.data = response.data;
      toDateTime(state.data.daily, "date");
      toDateTime(state.data.hourly, "time");
      for (let day of state.data.daily) {
        state.hourlyForecast[day.dt] = {times:[],show: false};
      }
      for (let hour of state.data.hourly) {
        const date = new Date(hour.dt).toDateString();
        if(state.hourlyForecast.hasOwnProperty(date)) {
          const time = new Date(hour.dt).toTimeString();
          state.hourlyForecast[date].times.push(time);
        }
      }
      console.log(state.hourlyForecast);
    });
};
function toDateTime(forecasts, format) {
    forecasts.forEach((element) => {
      let realTime = element.dt * 1000;
      if (format === "date") {
        realTime = new Date(realTime).toDateString();
      } else if (format === "time") {
        realTime = new Date(realTime).toString();
      }
      element.dt = realTime;
    });
}
const showDailyForecast = () => {
  console.log(event.target.value)
  state.clickedButton = event.target.value;
  state.hourlyForecast[event.target.value].show = true;
  
};
const getForecast = () => {
  state.hide = false;
  // convert epochs to date/time
  
};
</script>
