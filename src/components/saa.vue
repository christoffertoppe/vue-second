<template>
    <div>
        <h4>Weather in {{ this.weather.name }}</h4>

        <table class="weather_table">
            <tr>

                <th>{{ (this.weather.main.temp - 273.15).toFixed(1)}} &deg;C</th><th></th>
            </tr>
            <tr>
                <td>Wind:</td>
                <td>{{ this.weather.wind.speed }} m/s</td>
            </tr>

            <tr>
                <td>Pressure:</td>
                <td>{{ this.weather.main.pressure }} hpa</td>
            </tr>
            <tr>
                <td>Humidity:</td>
                <td>{{ this.weather.main.humidity }}%</td>
            </tr>
            <tr>
                <td>Sunrise:</td>
                <td>{{new Date(this.weather.sys.sunrise * 1000).toLocaleTimeString() }}</td>
            </tr>
            <tr>
                <td>Sunset:</td>
                <td>{{ new Date(this.weather.sys.sunset * 1000).toLocaleTimeString() }}</td>
            </tr>
            <tr>
                <td>Geo.coords:</td>
                <td>[{{ this.weather.coord.lat}}, {{ this.weather.coord.lon }}] </td>
            </tr>
        </table>
    </div>
    </template>

<script>

  export default {
    data() {
      return {
        weather: Array,
      }
    },
    mounted() {
      this.getWeather('helsinki, FI');
    },
    methods: {
      getWeather( cityID ) {

        var key = process.env.VUE_APP_WEATHER_KEY2;
        fetch('https://api.openweathermap.org/data/2.5/weather?q=' + cityID + '&appid=' + key).
            then(res => res.json()).then(res => {
              this.weather = res;
              console.log('sää', this.weather);
            });
      },
    }
  }

  </script>

<style>
    .weather_table, .weather_table tr {
        border-style: solid;
        margin-bottom: 10px;
        margin-left: 2px;

    }
</style>