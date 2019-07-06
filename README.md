Movie times app
=================

The backend code - used to request the movies' posters and descriptions - is [here](https://github.com/ckpantelides/movietime-server)

#### A cinema and film listing app built with NativeScript-Vue

It finds your location using the android geolocation API. It then finds your local cinemas using the [CineList API](https://github.com/seanmtracey/CineList-API) - which I run my own clone of on heroku. You can also search for cinemas by place name. 

CineList is also used to get the the film listings for 7 days. The calls between the CineList API are managed through axios.

When the movie listings are displayed, the frontend connects via socket.io to a separate [backend](https://github.com/ckpantelides/movietime-server). I send an array of the film names to this backend, which searches [The Movie DB](https://www.themoviedb.org/) for the films' posters and descriptions, and sends these back to the frontend.

By clicking on an individual film listing, it shows you the movie's description.

#### Notes on development

This is an android port of a [vue web app](https://github.com/ckpantelides/movietimes) I developed. Porting it wasn't too difficult, the component structure is generally the same, although the app has 7 days of movie times instead of 3. NativeScript doesn't use divs, so I used mostly StackLayout and ListView to structure the code.

Another difference is that the "view" doesn't update automatically (for example when the movie posters are received from the back end). To get around this, I gave my list a movies a reference called listView (ref="listView"). I used a watcher to look for changes in the poster images' data, which would fire the following code when the images were received: this.$refs.listView.nativeView.refresh(). Code that use "refs" can't be used in functions that fire when the component is loaded or mounted.

The last major difference is that NativeScript doesn't support rotateY. In the web version of the app, when a movie listing is tapped, it flips over to show the movie's description. Without rotateY the flip animation isn't possible.

![img1] ![img2]

[img1]: https://github.com/ckpantelides/movietimes/blob/images/movie-web1.jpg
[img2]: https://github.com/ckpantelides/movietimes/blob/images/movie-web2.jpg

#### Installation

> npm install // install dependencies

> tns build android --bundle // build for production

> tns run android --bundle // compile for development with hot reloads
