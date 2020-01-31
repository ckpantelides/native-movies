```diff
- This version of the app has been superceded (as the CineList-API is now deprecated)
- Version 2 of the app can be found here: https://github.com/ckpantelides/movietimes-native
```

Movie times app
=================

The backend code - used to request the movies' posters and descriptions - is [here](https://github.com/ckpantelides/movietime-server)

#### A cinema and film listing app built with NativeScript-Vue

It finds your location using the android geolocation API. It then finds your local cinemas using the [CineList API](https://github.com/seanmtracey/CineList-API) - which I run my own clone of on heroku. You can also search for cinemas by place name. 

CineList is also used to get the the film listings for 7 days. The calls between the CineList API are managed through axios.

When the movie listings are displayed, the frontend connects via socket.io to a separate [backend](https://github.com/ckpantelides/movietime-server). I send an array of the film names to this backend, which searches [The Movie DB](https://www.themoviedb.org/) for the films' posters and descriptions, and sends these back to the frontend.

By clicking on an individual film listing, it shows you the movie's description. 

The results of the cinema search and movie listings are cached using the [nativescript-localstorage](https://www.npmjs.com/package/nativescript-localstorage) package.

#### Code structure

The Cinema component is intially displayed on startup. It get's your location, makes the request to CineList and displays the results. Once the user has selected a cinema from the results, the cinema's id is passed to the MovieTimes component, which has seven child components for each day of the week. The component for "today" is the first of these that will display, and will send a request to CineList for the movie times using the cinema id. There's a separate component for cinema searches by place name.

As the structure is straightforward, routing is handled manually, and data and events are passed from component to component (rather than using an event bus or Vuex).

#### Notes on development

This is an android port of a [vue web app](https://github.com/ckpantelides/movietimes) I developed. Porting it wasn't too difficult, the component structure is generally the same, although the app has 7 days of movie listings instead of 3. NativeScript doesn't use divs, so I used mostly StackLayout and ListView to structure the code.

Another difference is that the "view" doesn't update automatically (for example when the movie posters are received from the backend). To get around this, I gave my list of movies a reference called listView (ref="listView"). I used a watcher to look for changes in the poster images' data, which would fire the following code when the images were received: this.$refs.listView.nativeView.refresh(). NB code that uses "refs" can't be used in functions that fire when the component is loaded or mounted.

The last major difference is that NativeScript doesn't support rotateY. In the web version of the app, when a movie listing is tapped, it flips over to show the movie's description. Without rotateY the flip animation isn't possible.

#### Building the release app

I had to add the following to the node field in webpack.config.js: node: { net: 'empty', tls: 'empty', dns: 'empty' }. Building through the CLI wouldn't complete, so I built the app through NativeScript Sidekick. The NativeScript ID in the package.json has to be changed from the default first. And I had to convert the keystore.jks file to a .p12 file.

![img1] ![img2]

[img1]: https://github.com/ckpantelides/native-movies/blob/images/movie-app1.jpg
[img2]: https://github.com/ckpantelides/native-movies/blob/images/movie-app2.jpg


#### Installation

> npm install // install dependencies

> tns run android --bundle // compile for development

> tns build android --bundle // build for production
