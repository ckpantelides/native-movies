<template >
  <ListView @loaded="loaded" for="result in results" height="100%">
    <v-template>
      <card-view class="cardStyle" margin="10" elevation="40" radius="1" height="200">
        <StackLayout>
          <Label class="cardContent" :text="result.title" textWrap="true"/>
          <ScrollView class="footer" orientation="horizontal">
            <StackLayout orientation="horizontal" horizontalAlignment="center">
              <Label class="times" :text="' ' + ' ' + el + ' ' + ' '" v-for="el in result.times"/>
            </StackLayout>
          </ScrollView>
        </StackLayout>
      </card-view>
    </v-template>
  </ListView>
</template>

<script >
import axios from "axios";
import Vue from "nativescript-vue";

const API = "https://cinelistapi.herokuapp.com/get/times/cinema/";

export default {
  name: "MovieDay1",
  components: {},
  props: {
    IDtoSearch: Number
  },
  data: function() {
    return {
      results: [],
      loader: true
    };
  },
  methods: {
    getMovies(url) {
      axios
        .get(url)
        .then(response => {
          this.results = response.data.listings;
          this.loader = false;
          // data emitted to server, so server can perform movie image search
          socket.emit("request images", { data: response.data.listings });
        })
        .catch(error => {
          console.log(error);
        });
    },
    buildUrl() {
      this.getMovies(API + this.IDtoSearch + "?day=1");
    },
    loaded() {
      this.buildUrl();
    }
  }
};
</script>

<style scoped>
.title {
  font-family: Josefin Sans, sans-serif;
  font-size: 20;
  color: white;
  margin-top: 0;
  text-align: center;
  margin-top: 5%;
  font-weight: bold;
}

.message {
  vertical-align: center;
  text-align: center;
  font-size: 20;
  color: #333333;
}

.times {
  padding-bottom: 10;
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
