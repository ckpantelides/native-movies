<template>
  <Page class="cinemas" @loaded="loaded" actionBarHidden="true">
    <StackLayout>
      <SearchBar
        v-if="showSearchBar"
        ref="searchBar"
        hint="Search by place name"
        :text="searchPhrase"
        @submit="onSubmit"
        height="30"
        class="search"
      />
      <label class="title" text="Cinemas"/>
      <ListView for="result in results" height="100%">
        <v-template>
          <card-view
            @tap="cinemaChosen(result.id)"
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
var view = require("ui/core/view");

const API = "https://cinelistapi.herokuapp.com/search/cinemas/coordinates/";

export default {
  data() {
    return {
      msg: "Hello There",
      cinemaID: Number,
      results: [],
      location: "",
      loader: true,
      showSearchBar: false,
      searchPhrase: ""
    };
  },
  methods: {
    getCinemas(url) {
      axios
        .get(url)
        .then(response => {
          let firstTenResults = response.data.cinemas.slice(0, 10);
          this.results = firstTenResults;
        })
        .catch(error => {
          console.log("Error with coordinate search");
          /****************************************************/
          // Need to catch error with this too
          this.getCinemas(
            "https://cinelistapi.herokuapp.com/search/cinemas/location/finchley"
          );
        });
    },
    loaded() {
      // this.hideKeyBoard();
      const lat = 51.510357;
      const lon = -0.116773;

      this.getCinemas(API + lat + "/" + lon);
    },
    cinemaChosen(cinemaID) {
      this.$emit("cinemaChosen", cinemaID);
    },
    saveLocation() {
      this.$emit("newCinemaSearch", this.location);
      // },
      // hideKeyBoard() {
      //   setTimeout(() => {
      //     this.$refs.searchBar.nativeView.dismissSoftInput();
      //   }, 5);
    }
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
  margin-top: 5%;
  font-weight: bold;
}

.search {
  font-family: Josefin Sans, sans-serif;
  font-size: 15;
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
}
</style>
