<template>
  <header>
    <h1>WeatherBox</h1>
  </header>
<main>
  <input type="text" class="city" placeholder="Enter your city name" v-model="city" @keyup.enter="searchWeather">
  <button class="search" @click="searchWeather">Search</button>
  <div class="error" v-if="message">{{ message }}</div>
  <div class="loading" v-if="isLoading">Loading...</div>
  <div class="weatherInfo fade-in" v-if="weatherData && !isLoading">
    <img :src="weatherData.iconUrl" class="weather-icon" :alt="weatherData.condition" v-if="weatherData.iconUrl">
    <div class="weather-item weather-city">City: {{ weatherData.city }}</div>
    <div class="weather-item weather-temp">Temperature: {{ weatherData.temperature }}°C</div>
    <div class="weather-item weather-condition">Condition: {{ weatherData.condition }}</div>
    <div class="weather-item weather-humidity">Humidity: {{ weatherData.humidity }}%</div>
  </div>
</main>
</template>

<script>
import axios from 'axios'; // For HTTP request

export default {
  name:'App',
  data(){
    return {
      city: '',
      message:'',
      weatherData: null,
      isLoading: false
    };
  },
  methods:{
    async searchWeather(){
      this.weatherData = null;
      this.message = '';
      this.isLoading = true;

      const inputCity = this.city.trim();
      if (!inputCity){
        this.message = 'Please enter a city name!';
        this.isLoading = false;
        return;
      }

      //OpenWeatherMap API
      const apiKey = import.meta.env.VITE_API_KEY;
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${inputCity}&appid=${apiKey}&units=metric`;

      try{
        const response = await axios.get(url); // Send GET request
        this.weatherData = {
          city: response.data.name,
          temperature: Math.round(response.data.main.temp),
          condition: response.data.weather[0].main,
          humidity:response.data.main.humidity,
          iconUrl: `http://openweathermap.org/img/wn/${response.data.weather[0].icon}@2x.png`
        };
      }catch(error){
        if(error.response&&error.response.status === 404){ // City not found
          this.message = 'City not found!';
        }else{
          this.message = 'Error fetching weather data. Please try again'; // Other errors
        }
      }finally{
        this.isLoading = false;
      }
    }
  }
};
</script>

<style scoped>

header{
  text-align: center;
}

header h1{
  color:#218ae0;
  font-size: 45px;
  font-family:'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
  text-shadow: 1px 1px 3px blue;
}

main{
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 30px;
  padding: 10px;
  background-color: lightblue;
  min-height: calc(100vh - 80px); /* Extend main to cover below area */
}

.city{
  margin-top: 20px;
  padding: 10px;
  width: 200px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 15px;
}

.search{
  padding: 10px 20px;
  background-color: rgb(10, 239, 10);
  border: none;
  border-radius: 5px;
  cursor: pointer;
  border: 0.5px solid greenyellow ;
}

.search:hover{
  background-color: rgb(84, 145, 84);
  color: yellow;
  transition: 0.3s;
}

.weatherInfo{
  width: 300px;
  min-height: 300px;
  background-color: white;
  border-radius: 5px;
  box-shadow: 2px 2px 5px rgb(57, 57, 57);
  text-align: center;
  font-family: 'Courier New', Courier, monospace;
  padding: 20px 0;
}

.weather-item{
  font-size: 20px;
  color: #333;
  margin-bottom: 15px;
  text-transform: uppercase;
}

.error{
  color: red;
  text-align: center;
  margin: 10px 0;
}

.weather-icon{
  width: 50px;
  height: 50px;
  margin-bottom: 10px;
}

.fade-in{
  animation: fadeIn 0.5s ease-in;
}

@keyframes fadeIn{
  from{
    opacity: 0; /*透明*/
    transform: translateY(10px);
  }
  to{
    opacity: 1; /*不透明 */
    transform: translateY(0);
  }
}

.loading{
  color: black;
  font-size: 15px;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
  text-align: center;
  margin: 10px 0;
}
</style>

