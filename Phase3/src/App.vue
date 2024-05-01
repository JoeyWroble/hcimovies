<script setup>

  import papaparse from 'papaparse';
  import {ref, onMounted} from 'vue';


  import Home from "./Home.vue"
  import MyMovies from "./MyMovies.vue"
  import Watchlist from "./Watchlist.vue"
  const weWantTheseMovies = ref([])
  const rating = ref()
  const director = ref()
  const cast = ref()
  const current = ref("Home")
  const movieTitles = ref([])
  const Data = ref([]);
  const loaded = ref(false)
  const genres = ref([])
  const ratings = ref([10, 9, 8, 7, 6, 5, 4, 3, 2, 1])
  const ratingsOfAllMovies = ref([])
  const selectedGenres = ref([])
  const rat = ref([])
  const dataFile = "./src/components/imdb_top_1000.csv"
  const watchlist = ref([]);
  const mymovies = ref([]);

  function fitleredMovies() {
    weWantTheseMovies.value = []
    let moviesOfCorrectGenre = []
    // checks to see if the user wants to filter by genre
    if (selectedGenres.value.length > 0) {
      // for each row in dataset
      for (let item of Data.value) {
        // Create a list of genres in the row
        const listOfGen = item.Genre.split(", ")
      
        const listOfr = rat.value

        const Title = item.Series_Title

        let stat = false
        // For each genre that the user selected,
        for (let G of selectedGenres.value) {
          // Check to see if the row includes that genre
          if (listOfGen.includes(G)) {
            stat = true;
            break;
          }
        }
        // If it does
        if (stat === true) {
          // The movie is added to moviesOfCorrectGenre since the movie includes the genre that the user wanted.
          moviesOfCorrectGenre.push(Title)
        }
      }
    }
    // If no genres were selected,
    else {
      // All movies were added to moviesOfCorrectGenre since no restrictions were placed on the genre.
      for (let movie of movieTitles.value) {
        moviesOfCorrectGenre.push(movie)
      }
    }
    //movieTitles.value = weWantTheseMovies.value



    let moviesOfCorrectRating = []
    // If statement ensures that rating is a valid float
    if (typeof rating.value == 'string' && rating.value.length == 3) {
      if ("." == rating.value[1]) {
        if (7 == rating.value[0]) {
          if (["6", "7", "8", "9"].includes(rating.value[2])) {
            for (let i in Data.value) {
              // For each row if the IMDb rating is some variation of a 7,
              // The movie at that row is added to moviesOfCorrectRating because they correspond to a valid rating
              if (Data.value[i].IMDB_Rating == rating.value) {
                moviesOfCorrectRating.push(Data.value[i].Series_Title)
              }
            }
          }
        }


        if (8 == rating.value[0]) {
          if (["1", "2", "3", "4", "5", "6", "7", "8", "9"].includes(rating.value[2])) {
            for (let i in Data.value) {
              // For each row if the IMDb rating is some variation of an 8,
              // The movie at that row is added to moviesOfCorrectRating because they correspond to a valid rating
              if (Data.value[i].IMDB_Rating == rating.value) {
                moviesOfCorrectRating.push(Data.value[i].Series_Title)
              }
            }
          }
        }
        
        




        if (9 == rating.value[0]) {
          if (["2", "3"].includes(rating.value[2])) {
            for (let i in Data.value) {
              // For each row if the IMDb rating is some variation of an 8,
              // The movie at that row is added to moviesOfCorrectRating because they correspond to a valid rating
              if (Data.value[i].IMDB_Rating == rating.value) {
                moviesOfCorrectRating.push(Data.value[i].Series_Title)
              }
            }
          }
        }
      }
    }

    // If the rating is a valid integer
    else if (typeof rating.value == 'string' && rating.value.length == 1 && ['7', '8', '9'].includes(rating.value)) {
      const ratingMap = {'7': ["7.6", "7.7", "7.8", "7.9"], '8': ["8", "8.1", "8.2", "8.3", "8.4", "8.5", "8.6", "8.7", "8.8", "8.9"], '9': ["9", "9.2", "9.3"]}
      for (let i = 0; i < Data.value.length; i++) {
        // For each row, if the IMDB rating is some variation of the integer rating input,
        if (ratingMap[rating.value].includes(Data.value[i].IMDB_Rating)) {
          // The movie at that row is added to moviesOfCorrectRating
          moviesOfCorrectRating.push(Data.value[i].Series_Title)
        }
      }
      
    }
    // If the user did not want to filter by rating
    else {
      // Add all movies to moviesOfCorrectRating
      for (let movie of movieTitles.value) {
        moviesOfCorrectRating.push(movie)
      }
    }


    let moviesOfCorrectDirector = []
    // Check to see if Director is a valid string
    if (typeof director.value == 'string' && director.value.length > 0) {
      for (let i in Data.value) {
        // Convert the director in the data set to lowercase
        let d = Data.value[i].Director
        d = d.toLowerCase()
        // If the input Director is the same as the Director in the data set,
        if (d == director.value.toLowerCase()) {
          // The corresponding movie is added to moviesOfCorrectDirector
          moviesOfCorrectDirector.push(Data.value[i].Series_Title)
        }
      }
    }
    // if there is no Director input
    else {
      // Add every movie to moviesOfCorrectDirector since no restrictions replaced on the direct
      for (let movie of movieTitles.value) {
        moviesOfCorrectDirector.push(movie)
      }
    }



    
    let moviesOfCorrectCast = []
    // If statement ensures that cast is a valid string
    if (typeof cast.value == 'string' && cast.value.length > 0) {
      // If statement determines if there are multiple cast numbers
      if (cast.value.includes(",")) {
        cast.value = cast.value.split(",")
        // For loop converts each cast member in input to all lowercase and removes surrounding white space
        for (let i in cast.value) {
          cast.value[i] = cast.value[i].trim()
          cast.value[i] = cast.value[i].toLowerCase()
        }
        // For loop Ads movies with correct cast to moviesOfCorrectCast
        for (let i in Data.value) {
          // Each cast member in the row is converted to lowercase
          let c1 = Data.value[i].Star1
          c1 = c1.toLowerCase()
          let c2 = Data.value[i].Star2
          c2 = c2.toLowerCase()
          let c3 = Data.value[i].Star3
          c3 = c3.toLowerCase()
          let c4 = Data.value[i].Star4
          c4 = c4.toLowerCase()
          const c = [c1, c2, c3, c4]
          let s = true
          // For loop checks to see if any cast member from the input is not in the current row data set
          for (let cc of cast.value) {
            if (!c.includes(cc)) {
              s = false
              break
            }
          }
          // If all cast members from input are in the current row, the movie at that row is added to moviesOfCorrectCast
          if (s == true) {
            moviesOfCorrectCast.push(Data.value[i].Series_Title)
          }
        }
      }
      // If there is only one cast member in the input
      else {
        // For loop Ads movies with correct cast to moviesOfCorrectCast
        for (let i in Data.value) {
          // Each cast member in the row is converted to lowercase
          let c1 = Data.value[i].Star1
          c1 = c1.toLowerCase()
          let c2 = Data.value[i].Star2
          c2 = c2.toLowerCase()
          let c3 = Data.value[i].Star3
          c3 = c3.toLowerCase()
          let c4 = Data.value[i].Star1
          c1 = c4.toLowerCase()
          const c = [c1, c2, c3, c4]
          // if statement checks to see if any cast member from the input is not in the current row data set
          if (c.includes(cast.value.toLowerCase())) {
            // The movie at that row is added to moviesOfCorrectCast
            moviesOfCorrectCast.push(Data.value[i].Series_Title)
          }
        }
      }

    }
    // If no input was entered for cast
    else {
      // All movies are added to moviesOfCorrectCast since no restrictions were placed on cast
      for (let movie of movieTitles.value) {
        moviesOfCorrectCast.push(movie)
      }
    }

    // For each movie
    for (let movie of movieTitles.value) {
      // If the movie meets all filter requirements,
      if (moviesOfCorrectGenre.includes(movie) && moviesOfCorrectRating.includes(movie) && moviesOfCorrectDirector.includes(movie) && moviesOfCorrectCast.includes(movie)) {
        // That movie is added to weWantTheseMovies
        weWantTheseMovies.value.push(movie)
        // weWantTheseMovies are the movies that get displayed on the website.
      }
    }
  }

  function resetFilters() { // resets the filter
    selectedGenres.value = []
    rating.value = ""
    director.value = ""
    cast.value = ""
    weWantTheseMovies.value = [...movieTitles.value]
  }

  function addToWatchlist(movieTitle, movieDetails) { // adds a movie to the watchlist by putting all of the details into one thing
  const movieData = {
    title: movieTitle,
    overview: movieDetails.overview,
    poster: movieDetails.poster,
    director: movieDetails.director,
    genres: movieDetails.genres,
    year: movieDetails.year,
    cast: movieDetails.cast,
    gross: movieDetails.gross,
    runtime: movieDetails.runtime,
    rating: movieDetails.rating
  };


  if (!watchlist.value.some(movie => movie.title === movieTitle)) {
    watchlist.value.push(movieData);
    alert(`Added ${movieTitle} to your watchlist!`); // This function alerts the user that they have successfully added a movie to the watchlist.
  }
}

function addToMyMovies(movieTitle, movieDetails) { // same as watchlist function
  const movieData = {
    title: movieTitle,
    overview: movieDetails.overview,
    poster: movieDetails.poster,
    director: movieDetails.director,
    genres: movieDetails.genres,
    year: movieDetails.year,
    cast: movieDetails.cast,
    gross: movieDetails.gross,
    runtime: movieDetails.runtime,
    rating: movieDetails.rating
  };

  if (!mymovies.value.some(movie => movie.title === movieTitle)) {
    mymovies.value.push(movieData);
    alert(`Added ${movieTitle} to your My Movies!`);
  }
}

  // The dated is fetched
  onMounted(() => fetch(dataFile)
    .then((res) => res.text())
    .then((text) => loadData(text))
    .then((parsedText) => showData(parsedText)));

  // The data is loaded
  async function loadData(dataText) {
    console.log("ready to load data");
    let data = papaparse.parse(dataText, {delimter: ",", header: true})
    return data;
  }

  function uniqueGenres(parsedData) {
    let genresWithDup = []
    
    for (let entry of parsedData.data) {
      const listOfGenres = entry.Genre.split(", ");
      for (let word of listOfGenres) {
        genresWithDup.push(word)
      }
    }
    genres.value = [...new Set(genresWithDup)]
    loaded.value = true;
  }
  function showData(parsedData) {
    parsedData.data.pop()
    Data.value = parsedData.data
    for (let i = 0; i < parsedData.data.length; i++) {
      weWantTheseMovies.value.push(parsedData.data[i].Series_Title)
      movieTitles.value.push(parsedData.data[i].Series_Title)
    }
    for (let i = 0; i < parsedData.data.length; i++) {
      ratingsOfAllMovies.value.push(parsedData.data[i].IMDB_Rating)
    }
    uniqueGenres(parsedData)
  }
</script>

<template>

<!-- implemented the navbar -->
  <nav class="navbar navbar-expand-lg bg-body-tertiary">

    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNavDropdown">
      <div class="navbar-nav">
        <ul class="navbar-nav ms-auto mb-2 mb-lg-0">
          <!-- made the different pages (Home, Watchlist, My Movies) adjust accordingly -->
          <li class="nav-item">
            <a href="#" :class="{'nav-link active': current == 'Home', 'nav-link': current != 'Home'}" aria-current="page" @click="current = 'Home'">Home</a>
          </li>
          <li class="nav-item">
            <a href="#" :class="{'nav-link active': current == 'Watchlist', 'nav-link': current != 'Watchlist'}" aria-current="page" @click="current = 'Watchlist'">Watchlist</a>
          </li>
          <li class="nav-item">
            <a href="#" :class="{'nav-link active': current == 'MyMovies', 'nav-link': current != 'MyMovies'}" aria-current="page" @click="current = 'My Movies'">My Movies</a>
          </li>
          <!-- got insperation for nested dropdowns from this youtube video: https://www.youtube.com/watch?v=h1hNZUs95nY&t=1s-->
          <li class="nav-item dropdown">
            <a href="#" class="nav-link dropdown-toggle" data-bs-auto-close="outside" data-bs-toggle="dropdown" aria-expanded="false">
              Filter
            </a>
            <ul class="dropdown-menu">
              <li class="dropend">
                <a href="#" class="dropdown-item dropdown-toggle" data-bs-toggle="dropdown">
                  Genre
                </a>
                <ul class="dropdown-menu">
                  <li v-for="genre of genres" :key="genre">
                    <div class="d-flex flex-row">
                      <input type="checkbox"
                            :id="genre"
                            v-model="selectedGenres"
                            :value="genre"
                            name="genre">
                      <label for="genre">{{ genre }}</label>
                    </div>
                  </li>
                  <li>
                    <div class="mt-3">Selected Genres: {{ selectedGenres }}</div>
                  </li>               
                </ul>
              </li>
                <input class="form-control" type="text" v-model="rating" placeholder="Enter Rating"/>
                <small class="form-text text-muted">Rating must be between 7.6 and 9.3. It can be an integer or a decimal rounded to the tenths place.</small>
              <li>
                <input class="form-control" type="text" v-model="director" placeholder="Enter Director"/>
              </li>
              <li>
                <input class="form-control" type="text" v-model="cast" placeholder="Enter Cast"/>
                <small class="form-text text-muted">Ether enter 1 cast member or enter multiple cast members separated by commas</small>
              </li>



              
              <li v-if="current == 'Home'">
                <button type="button" class="btn btn-success" @click="fitleredMovies()">Apply Filters</button>
              </li>
              <!--  
              <li v-else-if="current == 'MyMovies'">
                <button type="button" class="btn btn-success" @click="fitleredMyMovies()">Apply Filters</button>
              </li>
              <li v-else>
                <button type="button" class="btn btn-success" @click="fitleredWatchlist()">Apply Filters</button>
              </li>
              -->
              <li>
                <button type="button" class="btn btn-danger" @click="resetFilters()">Reset Filters</button>
              </li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
  </nav>



<div class="container-fluid" style="padding-top: 10px;">
    <div v-if="loaded">
      <div v-if="current == 'Home'">
        <Home 
          :Movies="weWantTheseMovies"
          :data="Data"
          @add-to-watchlist="addToWatchlist"
          @add-to-mymovies="addToMyMovies"
        />
      </div>
      <div v-else-if="current == 'Watchlist'">
        <Watchlist :watchlist="watchlist"/>
      </div>
      <div v-else>
        <MyMovies :mymovies="mymovies"/>
      </div>
    </div>

    <div v-else>
      <h1>Loading Data...</h1>
    </div>
  </div>
</template>