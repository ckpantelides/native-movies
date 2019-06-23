<template>
  <Page @loaded="loaded" actionBarHidden="true">
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
        @submit="newSearch"
        height="30"
        class="search"
      />
      <label class="title" text="Cinemas"/>
      <ActivityIndicator :busy="loading" height="20"/>
      <label v-if="error" class="error" text="Sorry I couldn't find any cinemas"/>
      <label v-if="error" class="error" text="Try another place name"/>
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
import Vue from "nativescript-vue";
import axios from "axios";

const API = "https://cinelistapi.herokuapp.com/search/cinemas/location/";

export default {
  name: "NewCinemaSearch",
  props: {
    newLocation: String
  },
  data() {
    return {
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
          this.location = "";
          this.loading = false;
          this.error = false;
        })
        .catch(error => {
          console.log("Error with location search");
          //this.getCinemas(
          //  "https://cinelistapi.herokuapp.com/search/cinemas/location/finchley"
          //);
          this.loading = false;
          this.error = true;
        });
    },
    buildUrl() {
      this.getCinemas(API + this.newLocation);
    },
    cinemaChosen(cinema) {
      this.$emit("cinemaChosen", cinema);
    },
    newSearch() {
      this.results = [];
      this.loader = true;
      this.error = false;
      this.getCinemas(API + this.location);
    },
    loaded() {
      this.buildUrl();
    },
    revealSearchBar() {
      this.showSearchBar = true;
      this.showSearchIcon = false;
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
  background: linear-gradient(to bottom, #53ba82 30%, #f3f3f3 0%);
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

.error {
  font-size: 15;
  font-family: Josefin Sans, sans-serif;
  text-align: center;
  font-weight: bold;
}
</style>
