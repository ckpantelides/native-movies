<template >
  <StackLayout>
    <ActivityIndicator :busy="loading" height="20"/>
    <ListView ref="listView" @loaded="loaded" for="(result, index) in results" height="100%">
      <v-template>
        <card-view
          @tap="flip(index)"
          class="cardStyle"
          margin="10"
          elevation="40"
          radius="1"
          height="200"
        >
          <StackLayout>
            <StackLayout :class="['front', { active: showBack[index] === true }]">
              <StackLayout orientation="horizontal">
                <Image :src="images[index].poster" stretch="aspectFill"></Image>
                <Label class="cardContent" :text="result.title" textWrap="true"/>
              </StackLayout>
              <ScrollView class="footer" orientation="horizontal">
                <StackLayout orientation="horizontal" horizontalAlignment="center">
                  <Label
                    class="times"
                    :text="' ' + ' ' + el + ' ' + ' '"
                    v-for="el in result.times"
                  />
                </StackLayout>
              </ScrollView>
            </StackLayout>
            <StackLayout :class="['back', { active: showBack[index] === true }]">
              <Label class="cardContent" :text="images[index].blurb" textWrap="true"/>
            </StackLayout>
          </StackLayout>
        </card-view>
      </v-template>
    </ListView>
  </StackLayout>
</template>

<script >
import axios from "axios";
import Vue from "nativescript-vue";
import Image from "tns-core-modules/ui/image";

const SocketIO = require("nativescript-socket.io");
const socket = SocketIO.connect("https://movietime-server.herokuapp.com/");

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
      images: [
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        }
      ],
      showBack: [
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false
      ],
      loading: true
    };
  },
  watch: {
    images: function(val) {
      // when the images are received via socketio, this view will be reloaded
      this.refreshView();
    }
  },
  methods: {
    getMovies(url) {
      axios
        .get(url)
        .then(response => {
          this.results = response.data.listings;
          this.loading = false;
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
    refreshView() {
      this.$refs.listView.nativeView.refresh();
    },
    loaded() {
      this.buildUrl();
      socket.on("image links", data => {
        this.images = data;
      });
    },
    flip(i) {
      if (this.showBack[i] === false) {
        this.showBack[i] = true;
        this.refreshView();
      } else if (this.showBack[i] === true) {
        this.showBack[i] = false;
        this.refreshView();
      }
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
  padding-left: 5;
  padding-right: 5;
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
  font-weight: bold;
  text-align: center;
}

.front {
  visibility: visible;
}
.front.active {
  visibility: collapse;
}

.back {
  visibility: collapse;
}

.back.active {
  visibility: visible;
}

.day.active {
  font-size: 16;
}

image {
  height: 50;
  width: 50;
  margin-left: 5;
}
</style>
