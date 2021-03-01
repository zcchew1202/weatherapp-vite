<template>
  <div id="v-model-select" class="demo">
    <select v-model="selected" @change="onChange($event)">
      <option disabled value="">Please select one</option>
      <option v-for="city in $options.cities" :key="city.id" :value="city.id">
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

  <button @click="getForecast">See forecast</button>
    <template v-if="clicked">
		<tr>
			<th>Date</th>
			<th>Temp</th>
			<th>Min Temp</th>
			<th>Max Temp</th>
			<th>Wind</th>
			<th>Description</th>
		</tr>
		<!-- do a for loop to generate table data for each day-->
		<tr>
			<!-- another for loop here for each hour -->
			<td></td>
		</tr>
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
  clicked: false
});
const onChange = (event) => {
  // let location = event.target.value.split(" ");
  // let cityName = location[0]
  // console.log(cities)
	console.log(event.target)
	state.coord = event.target.value.coord;
  axios
    .get(
      `http://api.openweathermap.org/data/2.5/weather?id=${event.target.value}&appid=538882fc8387290c6cee83f313a6acf5&units=metric`
    )
    .then((response) => {
      //   clouds = response.data;
      state.clouds = response.data.weather[0].description;
      state.temperature = response.data.main.temp;
      state.wind = response.data.wind.speed;
      console.log(response.data);
    });
};
const getForecast = () => {
	state.clicked = true;
// 	axios.get(`https://api.openweathermap.org/data/2.5/onecall?id=${state.coord}&exclude=hourly&appid=538882fc8387290c6cee83f313a6acf5&units=metric`).then((response) => {
// 		console.log(response.data)
// });
}
</script>
