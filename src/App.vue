<template>
  <div class="wrapper">
    <h1>Weather app</h1>
    <p>
      Find out the weather in
      {{ city == "" ? "your city or country" : cityName }}
    </p>
    <input
      type="text"
      v-model="city"
      @keydown.enter="submitWeather"
      @keydown="checkKey"
      placeholder="Enter city or country name"
    />
    <button class="active-btn" v-if="city != ''" @click="getWeather()">
      Check the weather
    </button>
    <button disabled v-else>Check the weather</button>
    <p class="error">{{ error }}</p>
    <div v-if="info != null">
      <p>{{ showTemp }}</p>
      <p>{{ showFeelsLike }}</p>
      <p>{{ showMinTemp }}</p>
      <p>{{ showMaxTemp }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      city: "",
      error: "",
      info: null,
    };
  },
  computed: {
    cityName() {
      return "''" + this.city + "''";
    },
    showTemp() {
      return "Temperature: " + this.info.main.temp;
    },
    showFeelsLike() {
      return "Feels like: " + this.info.main.feels_like;
    },
    showMinTemp() {
      return "Minimum temperature: " + this.info.main.temp_min;
    },
    showMaxTemp() {
      return "Maximum temperature: " + this.info.main.temp_max;
    },
  },
  methods: {
    getWeather() {
      if (this.city.trim().length < 2) {
        this.error = "Bro, there's no one-letter city or country :)";
        return false;
      }

      this.error = "";

      axios
        .get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=768e0288406389e6e0f9840659813b24`
        )
        .then((res) => {
          if (res.data.cod === "404") {
            this.error = "City or country not found.";
          } else {
            this.info = res.data;
          }
        })
        .catch((error) => {
          console.error(error);
          this.error = "City or country not found.";
        });
    },

    submitWeather() {
      this.getWeather();
    },

    checkKey(event) {
      const allowedKeys = [
        "Backspace",
        "Delete",
        "Space",
        "Enter",

        ..."ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz -".split(""),
      ];
      if (!allowedKeys.includes(event.key)) {
        event.preventDefault();
      }
      if (event.key === "Backspace" || this.city.length === 0) {
        this.info = null;
        this.error = "";
      }
    },
  },
};
</script>

<style scoped>
.error {
  color: #d03939;
}

.wrapper {
  width: 900px;
  height: 500px;
  border-radius: 50px;
  padding: 20px;
  background: #1f0f25;
  text-align: center;
  color: rgb(224, 227, 227);
}
.wrapper p {
  margin-top: 20px;
  font-size: 19px;
}

.wrapper input {
  margin-top: 30px;
  background: transparent;
  border: 0;
  border-bottom: 2px solid #110813;
  color: #fcfcfc;
  font-size: 14px;
  padding: 5px 8px;
  outline: none;
}
.wrapper input:focus {
  border-bottom-color: #6e2d7d;
}

.active-btn {
  background: rgb(238,174,202);
  color: rgb(255, 255, 255);
  border-radius: 10px;
  border: 2px solid rgb(44, 7, 7);
  padding: 10px 15px;
  margin-left: 20px;
  cursor: pointer;
  transform: transform 500ms ease;
}
.active-btn:hover {
  transform: scale(1.1) translateY(-5px);
}

.wrapper button:disabled {
  background: rgb(169, 124, 143);
  cursor: not-allowed;
  color: rgb(255, 255, 255);
  border-radius: 10px;
  border: 2px solid #0e0404;
  padding: 10px 15px;
  margin-left: 20px;
}
@media (max-width: 768px) {
 .wrapper {
    width: 100%;
    padding: 10px;
    
  }
 .wrapper input {
    font-size: 12px;
  }
 .wrapper button {
    padding: 5px 10px;
  }
}

@media (min-width: 320px) {
    .wrapper {
        width: 80%;
        margin: 25px;
        
        
  }
 .wrapper input {
    font-size: 12px;
  }
 .wrapper button {
    padding: 5px 10px;
    margin: 15px;
  }

}

</style>
