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

    <!-- Main Weather Data -->
    <div
      v-if="selectedCity.name"
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
            <div class="d-flex align-items-center gap-2">
              <img
                width="30"
                height="30"
                src="https://img.icons8.com/sf-black/64/EBEBEB/temperature.png"
                alt="temperature"
              />
              <h7>Feels Like</h7>
            </div>

            <p class="num">{{ getCelsiusLike }} °C</p>
            <p class="dis">depends on humidity</p>
          </div>
          <div class="card">
            <div class="d-flex align-items-center gap-2">
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
              The concentration of water vapor present in the air
            </p>
          </div>
          <div class="card">
            <div class="d-flex align-items-center gap-2">
              <img
                width="28"
                height="28"
                src="https://img.icons8.com/ios-filled/50/EBEBEB/invisible.png"
                alt="visibility"
              />
              <h7>Visibility</h7>
            </div>

            <p class="num">{{ weatherData.visibility }} m</p>
            <p class="dis">Depends on humidity</p>
          </div>
          <div class="card">
            <div class="d-flex align-items-center gap-2">
              <img
                width="30"
                height="30"
                src="https://img.icons8.com/sf-black/64/EBEBEB/pressure.png"
                alt="pressure"
              />

              <h7>Pressure</h7>
            </div>

            <p class="num">{{ weatherData.temp.pressure }} inHg</p>
            <p class="dis">Typically cannot feel air pressure</p>
          </div>
        </div>
      </div>

      <!-- Secondary Weather Data -->
      <div
        class="secondary-weather-data d-flex flex-column justify-content-around"
      >
        <div class="card">
          <div class="d-flex align-items-center gap-2">
            <img
              width="26"
              height="26"
              src="https://img.icons8.com/external-vitaliy-gorbachev-fill-vitaly-gorbachev/60/EBEBEB/external-wind-nature-resource-vitaliy-gorbachev-fill-vitaly-gorbachev.png"
              alt="Condition"
            />
            <h7>Condition</h7>
          </div>
          <div class="d-flex gap-3">
            <p class="title">Condition:</p>
            <p>{{ weatherData.condition }}</p>
          </div>
          <div class="d-flex gap-3">
            <p class="title">Description:</p>
            <p>{{ weatherData.description }}</p>
          </div>
        </div>

        <div class="card">
          <div class="d-flex align-items-center gap-2">
            <img
              width="26"
              height="26"
              src="https://img.icons8.com/external-vitaliy-gorbachev-fill-vitaly-gorbachev/60/EBEBEB/external-wind-nature-resource-vitaliy-gorbachev-fill-vitaly-gorbachev.png"
              alt="Wind"
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
                <p>{{ weatherData.wind.speed }}</p>
                <div class="d-flex flex-column align-items-start">
                  <span>MPH</span>
                  <p>Speed</p>
                </div>
              </div>
              <div
                class="wind-num d-flex align-items-center justify-content-between"
              >
                <p>{{ weatherData.wind.deg ?? "N/A" }}</p>
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

    <!-- City Image Component -->
    <CityImage id="CityImg" v-if="selectedCity.name" :search="cityImageName" />
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import CityImage from "./CityImage.vue";

const CityName = ref("");
const CitySuggestions = ref([]);
const selectedCity = ref({ name: "", lat: 0, lon: 0, country: "" });
const weatherData = ref({});
const cityImageName = ref("");

const APIKey = "220dcae415dac2f2b672bab67ab28160";

const getCityDetails = async () => {
  if (!CityName.value.trim()) return;

  try {
    const res = await fetch(
      `https://api.openweathermap.org/geo/1.0/direct?q=${CityName.value}&limit=5&appid=${APIKey}`
    );
    if (!res.ok) throw new Error(`API Error: ${res.status}`);
    const locations = await res.json();
    CitySuggestions.value = locations || [];
  } catch (err) {
    alert(err.message);
  }
};

const selectCity = async (city) => {
  selectedCity.value = {
    name: city.name,
    lat: city.lat,
    lon: city.lon,
    country: city.country,
  };

  CitySuggestions.value = [];
  CityName.value = "";

  cityImageName.value = selectedCity.value.name;

  await getWeatherDetails();
};

const getWeatherDetails = async () => {
  if (!selectedCity.value) return;

  try {
    const res = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?lat=${selectedCity.value.lat}&lon=${selectedCity.value.lon}&appid=${APIKey}`
    );
    const data = await res.json();

    weatherData.value = {
      condition: data.weather[0]?.main,
      description: data.weather[0]?.description,
      temp: {
        current: data.main.temp,
        feels: data.main.feels_like,
        pressure: (data.main.pressure * 0.02953).toFixed(2), // hPa -> inHg
      },
      humidity: data.main.humidity,
      visibility: data.visibility,
      wind: {
        speed: (data.wind.speed * 2.237).toFixed(1), // m/s -> MPH
        deg: data.wind.deg,
      },
    };
  } catch (err) {
    alert(`Failed to fetch weather: ${err.message}`);
  }
};

const getCelsiusCurrent = computed(() =>
  weatherData.value?.temp?.current
    ? (weatherData.value.temp.current - 273.15).toFixed(1)
    : null
);
const getCelsiusLike = computed(() =>
  weatherData.value?.temp?.feels
    ? (weatherData.value.temp.feels - 273.15).toFixed(1)
    : null
);
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
