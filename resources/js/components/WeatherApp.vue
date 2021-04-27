<template>
  <div class="text-white mb-8">
      <div class="places-input">
        <Dropdown @citySelected="changeCity($event)" />
      </div>

      <div class="weather-container font-sans w-128 max-w-lg overflow-hidden bg-gray-900 shadow-lg mt-4 rounded-lg">
          <div class="current-weather flex items-center justify-between px-6 py-8">
            <div class="flex items-center">
                <div>
                    <div class="text-6xl font-semibold">{{ currentTemperature.actual }}°C</div>
                    <div>Feels like {{ currentTemperature.feels }}°C</div>
                </div>
                <div class="mx-5">
                    <div class="font-semibold">{{ currentTemperature.summary }}</div>
                    <div>{{ location.name }}</div>
                </div>
            </div>
            <div>
                <img :src="currentTemperature.icon" alt="">
            </div>
          </div>

          <div class="future-weather text-sm bg-gray-600 px-6 py-8 overflow-hidden rounded-lg">
              <div 
                v-for="(day, index) in daily" 
                class="flex items-center"
                :key="day.time"
                :class="{ 'mt-8' : index > 0}"
                v-if="index < 5"
                >
                  <div class="w-1/6 text-lg text-gray-200">{{ toDayOfWeek(day.dt) }}</div>
                  <div class="w-4/6 px-4 flex items-center">
                    <div>
                      <img :src="'http://openweathermap.org/img/wn/' + day.weather[0].icon + '@2x.png'" alt="" class="w-10">
                    </div>
                    <div class="">{{day.weather[0].description}}</div>
                  </div>
                  <div class="w-1/6 text-right">
                    <div class="text-sm">{{ Math.round(day.temp.max) }}°C</div>
                    <div class="text-sm">{{ Math.round(day.temp.min) }}°C</div>
                  </div>
              </div>
          </div>
      </div>
  </div>
</template>


<script>
import Dropdown from "./Dropdown"

export default {
  components: {
    Dropdown
  },
    methods: {
        fetchData() {
            fetch(`/api/weather?lat=${this.location.lat}&lon=${this.location.lon}`)
                .then(response=> response.json())
                .then(data => {
                  console.log(data)
                  this.currentTemperature.actual = Math.round(data.current.temp);
                  this.currentTemperature.feels = Math.round(data.current.feels_like);
                  this.currentTemperature.summary = data.current.weather[0].main;
                  this.currentTemperature.icon = "http://openweathermap.org/img/wn/" + data.current.weather[0].icon + "@2x.png";
                  this.location.name = data.timezone.replace("/", ", ");
                  this.daily = data.daily;
                  console.log('hello');
                })
        },
        toDayOfWeek(timestamp) {
          const newDate = new Date(timestamp*1000);
          const days = ['MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT', 'SUN'];

          return days[newDate.getDay()];
        },
        changeCity(coord) {
          this.location.lat = coord[1];
          this.location.lon = coord[2];
          this.fetchData();
        }
    },
    mounted() {
      this.fetchData();

    },
    data() {
      return {
        location: {
          name: '',
          lat: 41.01,
          lon: 28.97,
        },
        currentTemperature: {
          actual: '',
          feels: '',
          summary: '',
          icon: '',
        },
        daily: [],
      }
    },
}
</script>

