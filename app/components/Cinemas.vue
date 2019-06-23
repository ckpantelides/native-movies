<template>
  <Page class="cinemas" actionBarHidden="true">
    <StackLayout>
      <Image
        v-if="showSearchIcon"
        @tap="revealSearchBar"
        height="16"
        class="searchIcon"
        src="~/assets/images/search.png"
      />
      <SearchBar
        v-if="showSearchBar"
        hint="City, town or placename"
        v-model="location"
        @submit="saveLocation"
        height="30"
        class="search"
      />
      <label class="title" text="Cinemas"/>
      <ActivityIndicator :busy="loading" height="20"/>
      <label v-if="error" class="error" text="Sorry I couldn't find any cinemas"/>
      <label v-if="error" class="error" text="Try the search button"/>
      <ListView for="result in results" height="100%">
        <v-template>
          <card-view
            @tap="cinemaChosen(result)"
            class="cardStyle"
            margin="10"
            elevation="40"
            radius="1"
            height="200"
          >
            <label class="cardContent" :text="result.name" textWrap="true"/>
          </card-view>
        </v-template>
      </ListView>
    </StackLayout>
  </Page>
</template>

<script >
import axios from "axios";
import Vue from "nativescript-vue";
import * as geolocation from "nativescript-geolocation";
import { Accuracy } from "tns-core-modules/ui/enums";

// localStorage will be used to cache results from API requests
import localStorage from "nativescript-localstorage";

const API = "https://cinelistapi.herokuapp.com/search/cinemas/coordinates/";
var util = require("util");
export default {
  data() {
    return {
      msg: "Hello There",
      cinemaID: Number,
      results: [],
      location: "",
      loading: true,
      showSearchBar: false,
      showSearchIcon: true,
      error: false
    };
  },
  methods: {
    getCinemas(url) {
      axios
        .get(url)
        .then(response => {
          let firstTenResults = response.data.cinemas.slice(0, 10);
          this.results = firstTenResults;
          // set results to cache. localStorage does not accept arrays
          localStorage.setItem(
            "cachedResults",
            JSON.stringify(firstTenResults)
          );
          this.loading = false;
          this.error = false;
        })
        .catch(error => {
          console.log("Error with coordinate search");
          //  this.getCinemas(
          //    "https://cinelistapi.herokuapp.com/search/cinemas/location/london"
          // );
          this.loading = false;
          this.error = true;
        });
    },
    // cinema data emitted to App.vue, so it can be used by MovieTimes
    cinemaChosen(cinema) {
      this.$emit("cinemaChosen", cinema);
    },
    saveLocation() {
      this.$emit("newCinemaSearch", this.location);
    },
    revealSearchBar() {
      this.showSearchBar = true;
      this.showSearchIcon = false;
    }
  },
  mounted() {
    console.log("Finding your location");
    geolocation
      .getCurrentLocation({
        // desiredAccuracy: Accuracy.high,
        maximumAge: 1000,
        timeout: 20000
      })
      .then(res => {
        // four decimal places gives 11m accuracy
        // three decimal places gives 110m accuracy
        let lat = res.latitude.toFixed(3);
        let lon = res.longitude.toFixed(3);

        let d = new Date();
        let date = d.getDate() + "." + d.getMonth() + "." + d.getFullYear();
        // compare current lat, lon and date with results in cache
        // not comparing type, as localStorage stores as string
        if (
          lat == localStorage.getItem("cachedLat") &&
          lon == localStorage.getItem("cachedLon") &&
          date == localStorage.getItem("cachedDate")
        ) {
          // use cache is lat, lon and date match
          this.results = JSON.parse(localStorage.getItem("cachedResults"));
          this.loading = false;
          console.log("Cache used");
        } else {
          // else perform axios request and set new values for cache
          this.getCinemas(API + lat + "/" + lon);
          console.log("New axios request");
          localStorage.setItem("cachedLat", lat);
          localStorage.setItem("cachedLon", lon);
          localStorage.setItem("cachedDate", date);
        }
      })
      .catch(e => {
        console.log("Error finding your location", e);
        this.loading = false;
        this.error = true;
      });
  }
};
</script>

<style scoped>
.title {
  font-family: Josefin Sans, sans-serif;
  font-size: 30;
  color: white;
  margin-top: 0;
  text-align: center;
  margin-top: 2%;
  font-weight: bold;
}

.search {
  font-family: Josefin Sans, sans-serif;
  font-size: 15;
}

.searchIcon {
  margin-top: 3%;
}

.message {
  vertical-align: center;
  text-align: center;
  font-size: 20;
  color: #333333;
}

Page {
  background: linear-gradient(to bottom, hsl(171, 100%, 41%) 30%, #f3f3f3 0%);
  height: 100%;
  margin: 0;
  padding: 0;
}

.cardStyle {
  background-color: #fff;
  color: rgb(43, 43, 43);
  overflow: auto;
  font-family: Josefin Sans, sans-serif;
}

.cardContent {
  padding: 20;
  font-size: 15;
  font-family: Josefin Sans, sans-serif;
  text-align: center;
  font-weight: bold;
}

.error {
  color: white;
  font-size: 15;
  font-family: Josefin Sans, sans-serif;
  text-align: center;
  font-weight: bold;
}
</style>
