<template>
  <v-container class="bg">
    <div class="content mb-6">
      <h1 class="display-1 text-center">Welcome to Weather App</h1>
      <h1 class="display-1 text-center">Welcome to Weather App</h1>
    </div>
    <transition >
      <v-flex xs15 class="margin "  >
        <v-card color="#E1F5FE" light class="animate__animated animate__fadeInDown">
          <v-card-text>
            <v-layout justify-center>
              <v-flex class="text-center zoom-in">
                <h3  class="animate__animated animate__backInLeft">Temperature</h3>
                <h1 class="display-1 mt-3 animate__animated animate__backInLeft">{{weather.name}}</h1>
                <img :src="icon" alt="weather icon" class="animate__animated animate__backInLeft">
                <p class="animate__animated animate__backInLeft">
                  <span class="display-1 ">{{ temp() }}&#176;C</span>
                  <span class="ml-4 ">{{ weather.weather[0].description }}</span>
                </p>
              </v-flex>
              <v-flex class="text-center zoom-in">
                <h3 class="animate__animated animate__backInDown">Wind & Pressure </h3>
                <h3 class="headline mt-4 animate__animated animate__backInDown">
                  Wind : {{weather.wind.speed}}m/s ({{weather.wind.deg}} &deg;)
                </h3>
                <h3 class="headline mt-4 animate__animated animate__backInDown">
                  Humidity : {{weather.main.humidity}}%
                </h3>
                <h3 class="headline mt-4 animate__animated animate__backInDown">
                  Pressure : {{weather.main.pressure}} hPa
                </h3>
              </v-flex>
              <v-flex class="text-center zoom-in">
                <h3 class="animate__animated animate__backInRight">Others</h3>
                <h3 class="headline mt-4 animate__animated animate__backInRight">Min Temperature : {{ Math.round(weather.main.temp_min - 273) }}&#176;C</h3>
                <h3 class="headline mt-4 animate__animated animate__backInRight">Max Temperature : {{ Math.round(weather.main.temp_max - 273) }}&#176;C</h3>
                <h3 class="headline mt-4 animate__animated animate__backInRight">Feels like : {{ Math.round(weather.main.feels_like - 273) }}&#176;C</h3>
              </v-flex>
            </v-layout>
          </v-card-text>
        </v-card>
        <v-card color="#E1F5FE" light class="animate__animated animate__fadeInUp" >
          <v-card-text>
            <v-layout justify-center>
              <v-flex v-for="(day, index) in forecast" :key="index" class="text-center zoom-in" >
                <ul >
                  <li class="list">
                    <h5 class="mt-4 animate__animated animate__backInUp">Date : {{ forecast[index].dt_txt }} </h5>
                    <h5 class="mt-4 animate__animated animate__backInUp">Weather : {{ day.weather[0].description }} </h5>
                    <h5 class="mt-4 animate__animated animate__backInUp">Temperature : {{ Math.round(day.main.temp - 273) }}&#176;</h5>
                  </li>
                </ul>
              </v-flex>
            </v-layout>
          </v-card-text>
        </v-card>
      </v-flex>
    </transition>
      <v-flex xs12 class="mt-4">
        <v-form @submit.prevent="onSearch" >
          <v-text-field
          v-model="searchInput"
          label="Please Enter City Name"
          variant="underlined"
          >
          </v-text-field>
        </v-form>
        <v-btn density="compact" type="submit" @click="getCurrentLocation">Get your location</v-btn>
      </v-flex>
  </v-container>
</template>

<script>
import 'animate.css';
export default {
  asyncData({ params, $axios }) {
     return $axios.$get(`https://api.openweathermap.org/data/2.5/weather?q=Ankara&appid=32fed05c48d72082323a34f9f96bdce5`,)
      .then(res => {
        return {
          weather: res
        }
      })
  },
  data(){
    return{
      city: "Ankara",
      forecast:[]
    }
  },
  computed:{
    icon(){
      return this.weather.weather ? `http://openweathermap.org/img/w/${this.weather.weather[0].icon}.png`:""
    }
  },
  methods:{
    temp(){
      return  Math.round(this.weather.main.temp - 273)
    },

    getWeatherInfo(city){
      this.$axios.$get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=32fed05c48d72082323a34f9f96bdce5`,)
      .then(res => {
        this.weather = res;
        this.city = city;
        this.getForecastInfo(city);
      })
    },
    getCurrentLocation(city){
      if(navigator.geolocation){
        navigator.geolocation.getCurrentPosition(position => {
          const lat = position.coords.latitude;
          const lon = position.coords.longitude;
          this.$axios
            .$get(
              `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=32fed05c48d72082323a34f9f96bdce5`
            )
            .then(res => {
              this.weather = res;
              this.city =res.name;
              this.getForecastInfo(city);
            });
        });
      }else{
        alert("Geolocation is not support by this browser");
      }
    },
    getForecastInfo(city){
      this.$axios.$get(`https://api.openweathermap.org/data/2.5/forecast?q=${this.city}&appid=32fed05c48d72082323a34f9f96bdce5`,)
      .then(res => {
        this.forecast = res.list.filter((item, index) => index %8 === 0);
      })
    },
    onSearch(){
      this.getWeatherInfo(this.searchInput);
    },
  },
}
</script>

<style scoped lang="scss">
body {
  display: flex;
  min-height: 100vh;
  align-items: center;
  justify-content: center;
}
.list{
  list-style: none;
}
.zoom-in{
  transform-origin: center;
  transition: transform 0.4s ease-in-out;
}
.zoom-in:hover{
  transform: scale(1.1);
  .weather-icon{
    transition: transform 0.8s ease-in-out;
  }
}

.weather-icon:hover {
    transform: rotate(360deg);
  }
.margin{
  position: relative;
}
.content {
  position: relative;
}

.content h1 {
  margin-left: 50%;
  color: #fff;
  font-size: 8em;
  position: absolute;
  transform: translate(-50%, -50%);
}

.content h1:nth-child(1) {
  color: transparent;
  -webkit-text-stroke: 2px #03a9f4;
}

.content h1:nth-child(2) {
  color: #03a9f4;
  animation: animate 4s ease-in-out infinite;
}

@keyframes animate {
  0%,
  100% {
    clip-path: polygon(
      0% 45%,
      16% 44%,
      33% 50%,
      54% 60%,
      70% 61%,
      84% 59%,
      100% 52%,
      100% 100%,
      0% 100%
    );
  }

  50% {
    clip-path: polygon(
      0% 60%,
      15% 65%,
      34% 66%,
      51% 62%,
      67% 50%,
      84% 45%,
      100% 46%,
      100% 100%,
      0% 100%
    );
  }
}

</style>
