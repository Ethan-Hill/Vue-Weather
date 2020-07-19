<template>
  <div
    id="app"
    :class="typeof weatherM.main != 'undefined' && weatherM.main.temp > 16 ? 
    'warm' : ''"
  >
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          id="searchBar"
          @keypress="fetchWeather"
        />
      </div>
      <div class="weather-wrap" v-if="typeof weatherM.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weatherM.name }}, {{weatherM.sys.country}}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp" id="temp" v-on:click="changeTemp()">{{Math.round(weatherM.main.temp)}}°C</div>
          <div class="weather">{{weatherM.weather[0].main}}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api_key: "d12bc2e2839a1c0d87293e8575c2a7b3",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weatherM: {},
      weatherI: {},
      weatherStatus: "M"
    };
  },
  methods: {
    async fetchWeather(e) {
      var input = document.getElementById("searchBar");
      if (e.key == "Enter") {
        await fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        ).then(async response => {
          if (!response.ok) {
            input.style.backgroundColor = "rgba(255, 0, 0, 0.25)";
          } else {
            input.style.backgroundColor = "rgba(0, 255, 0, 0.25)";
            this.weather = await response.json().then(this.setResultsM);
          }
        });

        await fetch(
          `${this.url_base}weather?q=${this.query}&units=imperial&APPID=${this.api_key}`
        ).then(async response => {
          if (!response.ok) {
            input.style.backgroundColor = "rgba(255, 0, 0, 0.25)";
          } else {
            input.style.backgroundColor = "rgba(0, 255, 0, 0.25)";
            this.weather = await response.json().then(this.setResultsI);
          }
        });

        console.log(this.weatherI);
        console.log(this.weatherM);
      }
    },
    setResultsM(results) {
      this.weatherM = results;
    },
    setResultsI(results) {
      this.weatherI = results;
    },
    changeTemp() {
      if (this.weatherStatus == "M") {
        this.weatherStatus = "I";
        let box = document.getElementById("temp");
        box.innerHTML = Math.round(this.weatherI.main.temp) + "°F";
      } else {
        this.weatherStatus = "M";
        let box = document.getElementById("temp");
        box.innerHTML = Math.round(this.weatherM.main.temp) + "°C";
      }
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December"
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday"
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    }
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "montserrat", sans-serif;
}

#app {
  background-image: url("./assets/cold-bg.png");
  background-size: cover;
  background-position: center;
  transition: 0.4s;
}

#app.warm {
  background-image: url("./assets/warm-bg.png");
}

main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;

  appearance: none;
  order: none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 16px 16px 16px 16px;
  border: none;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 16px 16px 16px;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  text-align: center;
  font-style: italic;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  cursor: default;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  transition: 0.3s ease-in-out;
}

.weather-box .temp:hover {
  background-color: rgba(240, 238, 238, 0.5);
  transition: 0.4s ease-in-out;
}

.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
