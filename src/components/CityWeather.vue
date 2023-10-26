<template>
  <div class="container">
    <h1>Weather App</h1>
    <div class="input-container">
      <div class="input-group" style="padding-right: 450px;">
        <h2 style="font-size: 23px;">Додати місто</h2>
        <input class="city-input" v-model="cityInput" placeholder="Enter a city" @input="getWeather" />
        <br>
        <button @click="addCity" style="margin-top: 32px;">Додати місто</button>
      </div>
      <div class="input-group">
        <h2 style="font-size: 23px;">Додані міста</h2>
        <select class="selected-city-select" v-model="selectedCity" @change="getSelectedCityWeather">
          <option value="">Оберіть місто</option>
          <option v-for="selectedCity in selectedCities" :key="selectedCity" :value="selectedCity">{{ selectedCity }}
          </option>
        </select>
      </div>
    </div>
    <div class="weather-details">
      <h2>Weather in {{ selectedCity }}</h2>
      <div class="weather-details-content">
        <template v-if="selectedCityWeather">
          <div class="weather-info">
            <p>
              <font-awesome-icon class="fa-2x" icon="fa-solid fa-temperature-three-quarters" style="color: white" />
              Temperature: {{ selectedCityWeather.main.temp }}°C
            </p>
            <p>
              <font-awesome-icon class="fa-2x" icon="fa-solid fa-cloud" style="color: white" />
              Humidity: {{ selectedCityWeather.main.humidity }}%
            </p>
            <p>
              <font-awesome-icon class="fa-2x" icon="fa-solid fa-water" style="color: white" />
              Pressure: {{ selectedCityWeather.main.pressure }}hPa
            </p>
            <p>
              <font-awesome-icon class="fa-2x" icon="fa-solid fa-cloud-sun" style="color: white" />
              Weather Description: {{ selectedCityWeather.weather[0].description }}
            </p>
            <p>
              <font-awesome-icon class="fa-2x" icon="fa-solid fa-wind" style="color: white" />
              Wind Speed: {{ selectedCityWeather.wind.speed }} m/s
            </p>
          </div>
        </template>
        <template v-else>
          <p>Loading weather data...</p>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, reactive, onMounted } from "vue";

const cityInput = ref("");
const selectedCity = ref("");
const selectedCities = reactive([]);
const selectedCityWeather = ref(null);

const getWeatherByLocation = (position) => {
  const { latitude, longitude } = position.coords;
  axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=7914d5a440960cfd5df3bd0388a7ad0f&units=metric`)
    .then((response) => {
      selectedCity.value = response.data.name;
      getSelectedCityWeather();
    })
    .catch((error) => {
      console.error("Error fetching weather data by location", error);
    });
};

const getUserLocation = () => {
  if ("geolocation" in navigator) {
    navigator.geolocation.getCurrentPosition(getWeatherByLocation);
  } else {
    console.error("Geolocation is not available in this browser.");
  }
};

const getWeather = async () => {
  if (selectedCity.value) {
    try {
      const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${selectedCity.value}&appid=7914d5a440960cfd5df3bd0388a7ad0f&units=metric`);
      selectedCityWeather.value = response.data;
    } catch (error) {
      console.error("Error fetching weather data", error);
    }
  }
};

const addCity = () => {
  if (cityInput.value && !selectedCities.includes(cityInput.value)) {
    selectedCities.push(cityInput.value);
  }
  cityInput.value = "";
};

const getSelectedCityWeather = () => {
  if (selectedCity.value) {
    axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${selectedCity.value}&appid=7914d5a440960cfd5df3bd0388a7ad0f&units=metric`)
      .then((response) => {
        selectedCityWeather.value = response.data;
      })
      .catch((error) => {
        console.error("Error fetching weather data for the selected city", error);
        selectedCityWeather.value = null;
      });
  }
};

const showWeather = () => {
  getSelectedCityWeather();
};

onMounted(() => {
 getUserLocation(); 
});
</script>

<style>
.selected-city-select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #fff;
  color: #333;
  outline: none; 
  transition: border-color 0.2s;
  font-family: 'Georgia', serif;
}
.selected-city-select option {
  font-size: 16px;
  background-color: #ffffffab;
  color: #333;
}
.selected-city-select option[value=""] {
  font-weight: bold;
}
body {
  font-family: 'Georgia', serif;
}
.city-input {
width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}
.container {
  text-align: center;
  background-color: #dd9797;
  padding: 20px;
  border-radius: 10px;
  width: 800px;
  margin: 0 auto;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
}
.input-container {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}
input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}
button {
  background-color: #a55151;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 10px 20px;
  cursor: pointer;
  margin-top: 20px;
font-family: 'Georgia', serif;
font-weight: bold;
}
.weather-details {
  background-color: #b780807a;
  border-radius: 5px;
  padding: 10px;
  margin-bottom: 20px;
  display: flex; 
}
.weather-info {
  text-align: left; 
}
.selected-cities {
  display: flex;
  justify-content: space-between;
}
.selected-cities-list {
  flex: 1;
}
select {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-bottom: 10px;
}
.selected-city-weather {
  background-color: #FFF;
  border: 1px solid #ddd;
  border-radius: 5px;
  padding: 10px;
}
</style>