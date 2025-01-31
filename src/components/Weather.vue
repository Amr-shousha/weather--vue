<template>
  <div
    class="main container d-flex flex-column align-items-center justify-content-center"
  >
    <!-- Display city data -->
    <div
      class="input-container d-flex flex-column justify-content-center align-items-center"
    >
      <input
        v-model="CityName"
        @click="selectedCity.name = ''"
        @input="getCityDetails"
        :placeholder="
          selectedCity.name && selectedCity.country
            ? selectedCity.name + ', ' + selectedCity.country
            : 'Enter city and country'
        "
        type="text"
      />

      <div v-if="CitySuggestions.length > 0">
        <ul>
          <li
            v-for="(suggestion, index) in CitySuggestions"
            :key="index"
            @click="selectCity(suggestion)"
          >
            {{ suggestion.name }}, {{ suggestion.country }}
          </li>
        </ul>
      </div>
    </div>
    <div
      v-if="selectedCity"
      class="main-container container d-flex align-items-stretch justify-content-center"
    >
      <div
        class="main-weather-data d-flex flex-column justify-content-center align-items-center"
      >
        <div class="main-temp">
          <p id="main-deg">{{ getCelsiusCurrent }} °C</p>
        </div>
        <div
          class="main-data d-flex flex-wrap justify-content-center align-items-center"
        >
          <div class="card">
            <div class="d-flex align-items-cnter gap-2">
              <img
                width="30"
                height="30"
                src="https://img.icons8.com/sf-black/64/EBEBEB/temperature.png"
                alt="temperature"
              />
              <h7>Feels Like</h7>
            </div>

            <p class="num">{{ getCelsiusLike }} °C</p>
            <p class="dis">depends on humedity</p>
          </div>
          <div class="card">
            <div class="d-flex align-items-cnter gap-2">
              <img
                width="30"
                height="30"
                src="https://img.icons8.com/sf-black/64/EBEBEB/humidity.png"
                alt="humidity"
              />
              <h7>Humidity</h7>
            </div>

            <p class="num">{{ weatherData.humidity }} %</p>
            <p class="dis">
              the concentration of water vapor present in the air
            </p>
          </div>
          <div class="card">
            <div class="d-flex align-items-cnter gap-2">
              <img
                width="28"
                height="28"
                src="https://img.icons8.com/ios-filled/50/EBEBEB/invisible.png"
                alt="invisible"
              />
              <h7>visibility</h7>
            </div>

            <p class="num">{{ weatherData.visibility }} mi</p>
            <p class="dis">depends on humedity</p>
          </div>
          <div class="card">
            <div class="d-flex align-items-cnter gap-2">
              <img
                width="30"
                height="30"
                src="https://img.icons8.com/sf-black/64/EBEBEB/pressure.png"
                alt="pressure"
              />

              <h7>pressure</h7>
            </div>

            <p class="num">{{ weatherData.temp.pressure }} inHg</p>
            <p class="dis">typically can not feel air pressure</p>
          </div>
        </div>
      </div>
      <div
        class="secondary-weather-data d-flex flex-column justify-content-around"
      >
        <div class="card">
          <div class="d-flex align-items-cnter gap-2">
            <img
              width="26"
              height="26"
              src="https://img.icons8.com/external-vitaliy-gorbachev-fill-vitaly-gorbachev/60/EBEBEB/external-wind-nature-resource-vitaliy-gorbachev-fill-vitaly-gorbachev.png"
              alt="external-wind-nature-resource-vitaliy-gorbachev-fill-vitaly-gorbachev"
            />

            <h7>Condition</h7>
          </div>
          <div class="d-flex gap-3">
            <p class="title">Condition:</p>
            <p>{{ weatherData.condition }}</p>
          </div>
          <div class="d-flex gap-3">
            <p class="title">description:</p>
            <p>{{ weatherData.description }}</p>
          </div>
        </div>
        <div class="card">
          <div class="d-flex align-items-cnter gap-2">
            <img
              width="26"
              height="26"
              src="https://img.icons8.com/external-vitaliy-gorbachev-fill-vitaly-gorbachev/60/EBEBEB/external-wind-nature-resource-vitaliy-gorbachev-fill-vitaly-gorbachev.png"
              alt="external-wind-nature-resource-vitaliy-gorbachev-fill-vitaly-gorbachev"
            />

            <h7>Wind</h7>
          </div>
          <div
            class="wind-container d-flex align-items-center justify-content-between"
          >
            <div class="wind-num-container">
              <div
                class="wind-num d-flex align-items-center justify-content-between"
              >
                <p>{{ weatherData.wind.wind_speed }}</p>
                <div class="d-flex flex-column align-items-start">
                  <span>MPH</span>
                  <p>Speed</p>
                </div>
              </div>
              <div
                class="wind-num d-flex align-items-center justify-content-between"
              >
                <p>{{ weatherData.wind.wind_deg }}</p>
                <div class="d-flex flex-column align-items-start">
                  <span>°</span>
                  <p>Degree</p>
                </div>
              </div>
            </div>
            <div class="windImg">
              <img src="../assets/compass.png" alt="" />
            </div>
          </div>
        </div>
      </div>
    </div>
    {{ selectedCity }}
    <CityImage id="CityImg" :search="selectedCity.name" />
  </div>
</template>

<script setup>
import { computed, onMounted, ref } from "vue";
import CityImage from "./CityImage.vue";

const CityName = ref("");
const CityData = ref({});
const selectedCity = ref("");
const weatherData = ref({});

const APIKey = "220dcae415dac2f2b672bab67ab28160";

const formattedCityName = decodeURIComponent(CityName.value);
const CitySuggestions = ref([]); // This will hold all the location suggestions

const getCityDetails = async (formattedCityName) => {
  try {
    const res = await fetch(
      `http://api.openweathermap.org/geo/1.0/direct?q=${CityName.value}&limit=5&appid=${APIKey}`
    );

    if (!res.ok) {
      throw new Error(`API Error: ${res.status} - ${res.statusText}`);
    }

    const locations = await res.json();
    if (locations.length > 0) {
      const location = locations[0]; //مهم
      CitySuggestions.value = locations; // Store all location suggestions
      // Get the first location from the array
      CityData.value = {
        lat: location.lat,
        lon: location.lon,
        country: location.country,
      };
    } else {
      throw new Error("Location not found.");
    }
  } catch (err) {
    alert(err.message);
  }
};

const getWeatherDetails = async () => {
  try {
    const res = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?lat=${selectedCity.value.lat}&lon=${selectedCity.value.lon}&appid=${APIKey}`
    );
    const weatherDetails = await res.json();
    weatherData.value = {
      condition: weatherDetails.weather[0]?.main, //weather is an array. Accessing the first element with weather[0]?. ensures the code won't break if the array is empty or undefined.
      description: weatherDetails.weather[0]?.description,
      icon: weatherDetails.weather.icon,

      temp: {
        current: weatherDetails.main.temp,
        feels: weatherDetails.main.feels_like,
        pressure: weatherDetails.main.pressure,
      },
      visibility: weatherDetails.visibility,
      humidity: weatherDetails.main.humidity,
      wind: {
        wind_speed: weatherDetails.wind.speed,
        wind_deg: weatherDetails.wind.deg,
      },
    };
  } catch (err) {
    alert(`Failed to fetch weather details: ${err.message}`);
  }
};

const selectCity = (city) => {
  selectedCity.value = {
    name: city.name,
    lat: city.lat,
    lon: city.lon,
    country: city.country,
  };
  CitySuggestions.value = [];
  getWeatherDetails();
  CityName.value = "";
  getCelsiuscurrent();
  getCelsiusLike();
  console.log("imgLink");
};
const getCelsiusCurrent = computed(() => {
  if (weatherData.value?.temp?.current) {
    //? =>It ensures safe access to deeply nested properties by checking if the preceding property exists before trying to access the next one
    return (weatherData.value.temp.current - 273.15).toFixed(1); // Convert Kelvin to Celsius
  }
  return null; // Handle cases where the data isn't ready
});
const getCelsiusLike = computed(() => {
  if (weatherData.value?.temp?.feels) {
    //? =>It ensures safe access to deeply nested properties by checking if the preceding property exists before trying to access the next one
    return (weatherData.value.temp.feels - 273.15).toFixed(1); // Convert Kelvin to Celsius
  }
  return null; // Handle cases where the data isn't ready
});
</script>

<style lang="scss" scoped>
.main {
  h4 {
    color: rgb(255, 172, 172);
  }
  //background: url(../assets/bg.jpg) no-repeat center/cover;
  min-height: 500px;

  .input-container {
    // background-color: rgba(17, 17, 17, 0.505);
    padding: 30px 0;

    margin-top: 30px;

    input {
      background-color: rgb(30, 30, 30);
      padding: 10px;
      color: aliceblue;
      border: none;
      border-radius: 10px;
      width: 300px;
      &::placeholder {
        color: rgb(189, 189, 189);
      }
    }

    ul {
      margin-top: 15px;
      border-radius: 10px;
      background-color: rgb(30, 30, 30);
      color: aliceblue;
      list-style: none;
      width: 300px;
      text-align: left;
      padding: 10px 16px;

      li {
        padding: 3px 3px;
        &:hover {
          cursor: pointer;
          background-color: rgb(54, 54, 54);
        }
      }
    }
  }
  @media screen and (max-width: 769px) {
    .main-container {
      flex-direction: column !important;

      .secondary-weather-data {
        width: 100%;
      }

      .main-data {
        gap: 30px !important;
      }
    }
  }
  .main-weather-data {
    background-color: rgba(30, 30, 30, 0.543);
    padding: 0 20px 20px 20px;
    position: relative;
    // &::after {
    //   content: "";
    //   position: absolute;
    //   top: 10px;
    //   left: 50px;
    //   width: 250px;
    //   height: 80px;
    // }

    .main-data {
      max-width: 434px;
      gap: 10px;

      .card {
        padding: 10px 10px 10px 10px;
        color: rgb(163, 163, 163);
        background-color: rgba(30, 30, 30, 0.852);
        height: 170px;
        p {
          margin: 0;
        }
        .num {
          color: rgb(255, 255, 255);
          margin: 10px;
          font-size: 26px;
        }
        .dis {
          font-size: 14px;
          max-width: 140px;
        }
      }
    }
    .main-temp {
      width: 50%;
      #main-deg {
        margin-top: 10px;
        border-radius: 10px;
        background: url(../assets/gradiant.jpg) no-repeat center/cover;
        color: rgb(255, 255, 255);
        font-size: 45px;
        font-weight: 600;

        p {
          margin: 5px 10px;
        }
      }
    }
  }

  .secondary-weather-data {
    width: 340px;
    gap: 10px;
    background-color: rgba(17, 17, 17, 0.505);
    padding: 60px 10px 10px 10px;

    #CityImg {
      max-width: 100%;
      overflow: hidden;
    }
    .card {
      p {
        margin: 0px;
      }
      padding: 10px;
      color: aliceblue;
      background-color: rgb(41, 41, 41);
      .title {
        color: rgb(163, 163, 163);
      }
      .windImg img {
        width: 90px;
      }
      .wind-num {
        width: 120px;
        span {
          color: rgb(163, 163, 163);
        }
        p {
          font-size: 20px;
        }
      }
    }
  }
}
</style>
