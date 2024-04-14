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

  function fitleredMovies() {
    weWantTheseMovies.value = []
    let moviesOfCorrectGenre = []
    if (selectedGenres.value.length > 0) {
      for (let item of Data.value) {
        const listOfGen = item.Genre.split(", ")
      
        const listOfr = rat.value
        const Title = item.Series_Title

        let stat = false
        for (let G of selectedGenres.value) {
          if (listOfGen.includes(G)) {
            stat = true;
            break;
          }
        }
        if (stat === true) {
          moviesOfCorrectGenre.push(Title)
        }
      }
    }
    else {
      for (let movie of movieTitles.value) {
        moviesOfCorrectGenre.push(movie)
      }
    }
    //movieTitles.value = weWantTheseMovies.value



    let moviesOfCorrectRating = []
    if (typeof rating.value == 'string' && rating.value.length > 0) {
      //if (rating.value.length > 0) {
        const ratingMap = {'7': ["7", "7.1", "7.2", "7.3", "7.4", "7.5", "7.6", "7.7", "7.8", "7.9"], '8': ["8.1", "8.2", "8.3", "8.4", "8.5", "8.6", "8.7", "8.8", "8.9"], '9': ["9", "9.1", "9.2", "9.3"]}
        if (rating.value.length == 3 && rating.value.split(".").length == 2) {
          for (let i in Data.value) {
            if (Data.value[i].IMDB_Rating == rating.value) {
              moviesOfCorrectRating.push(Data.value[i].Series_Title)
            }
          }
        }
        else if (rating.value.length == 1 && ['7', '8', '9'].includes(rating.value)) {
          for (let i = 0; i < Data.value.length; i++) {
            if (ratingMap[rating.value].includes(Data.value[i].IMDB_Rating)) {
              moviesOfCorrectRating.push(Data.value[i].Series_Title)
            }
          }
          
        }
      //}
    }
    else {
      for (let movie of movieTitles.value) {
        moviesOfCorrectRating.push(movie)
      }
    }


    let moviesOfCorrectDirector = []
    if (typeof director.value == 'string' && director.value.length > 0) {
      for (let i in Data.value) {
        let d = Data.value[i].Director
        d = d.toLowerCase()
        if (d == director.value.toLowerCase()) {
          moviesOfCorrectDirector.push(Data.value[i].Series_Title)
        }
      }
    }
    else {
      for (let movie of movieTitles.value) {
        moviesOfCorrectDirector.push(movie)
      }
    }



    let moviesOfCorrectCast = []
    if (typeof cast.value == 'string' && cast.value.length > 0) {
      if (cast.value.includes(",")) {
        cast.value = cast.value.split(",")
        for (let i in cast.value) {
          cast.value[i] = cast.value[i].trim()
          cast.value[i] = cast.value[i].toLowerCase()
        }
        for (let i in Data.value) {
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
          for (let cc of cast.value) {
            if (!c.includes(cc)) {
              s = false
              break
            }
          }
          if (s == true) {
            moviesOfCorrectCast.push(Data.value[i].Series_Title)
          }
        }
      }
      else {
        for (let i in Data.value) {
          let c1 = Data.value[i].Star1
          c1 = c1.toLowerCase()
          let c2 = Data.value[i].Star2
          c2 = c2.toLowerCase()
          let c3 = Data.value[i].Star3
          c3 = c3.toLowerCase()
          let c4 = Data.value[i].Star1
          c1 = c4.toLowerCase()
          const c = [c1, c2, c3, c4]
          if (c.includes(cast.value.toLowerCase())) {
            moviesOfCorrectCast.push(Data.value[i].Series_Title)
          }
        }
      }

    }
    else {
      for (let movie of movieTitles.value) {
        moviesOfCorrectCast.push(movie)
      }
    }

    for (let movie of movieTitles.value) {
      if (moviesOfCorrectGenre.includes(movie) && moviesOfCorrectRating.includes(movie) && moviesOfCorrectDirector.includes(movie) && moviesOfCorrectCast.includes(movie)) {
        weWantTheseMovies.value.push(movie)
      }
    }
  }

  function resetFilters() {
    selectedGenres.value = []
    rating.value = ""
    director.value = ""
    cast.value = ""
    weWantTheseMovies.value = [...movieTitles.value]
  }
  
  onMounted(() => fetch(dataFile)
    .then((res) => res.text())
    .then((text) => loadData(text))
    .then((parsedText) => showData(parsedText)));

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


  <nav class="navbar navbar-expand-lg bg-body-tertiary">
    <!-- <a class="navbar-brand" href="#">Navbar</a> -->

    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNavDropdown">
      <div class="navbar-nav">
        <ul class="navbar-nav ms-auto mb-2 mb-lg-0">
          <li class="nav-item">
            <a href="#" :class="{'nav-link active': current == 'Home', 'nav-link': current != 'Home'}" aria-current="page" @click="current = 'Home'">Home</a>
          </li>
          <li class="nav-item">
            <a href="#" :class="{'nav-link active': current == 'Watchlist', 'nav-link': current != 'Watchlist'}" aria-current="page" @click="current = 'Watchlist'">Watchlist</a>
          </li>
          <li class="nav-item">
            <a href="#" :class="{'nav-link active': current == 'MyMovies', 'nav-link': current != 'MyMovies'}" aria-current="page" @click="current = 'My Movies'">My Movies</a>
          </li>

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
              <li v-else-if="current == 'MyMovies'">
                <button type="button" class="btn btn-success" @click="fitleredMyMovies()">Apply Filters</button>
              </li>
              <li v-else>
                <button type="button" class="btn btn-success" @click="fitleredWatchlist()">Apply Filters</button>
              </li>
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
          :Movies=weWantTheseMovies
          :data="Data"
        />
      </div>
      <div v-else-if="current == 'Watchlist'">
        <Watchlist
        />
      </div>
      <div v-else>
        <MyMovies
        />
      </div>
    </div>

    <div v-else>
      <h1>Loading Data...</h1>
    </div>
  </div>
</template>