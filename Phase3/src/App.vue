<script setup>
  import Watchlist from './Watchlist.vue'
  import MyMovies from './MyMovies.vue'
  import papaparse from 'papaparse';
  import {ref, onMounted} from 'vue';


  import Home from "./Home.vue"
  import MyMovies from "./MyMovies.vue"
  import Watchlist from "./Watchlist.vue"
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
    const weWantTheseMovies = ref([])
    for (let item of Data.value) {
      const listOfGen = item.Genre.split(", ")
    
      const listOfr = rat.value
      const Title = item.Series_Title

      let stat = false
      console.log("selectedGenres.value: " + selectedGenres.value)
      for (let G of selectedGenres.value) {
        if (listOfGen.includes(G)) {
          stat = true;
          break;
        }
      }
      if (stat === true) {
        weWantTheseMovies.value.push(Title)
      }
    }
    
    movieTitles.value = weWantTheseMovies.value

    console.log("movieTitles.value: " + movieTitles.value)
      // const r = item.IMDB_Rating;
  
      // movieTitles.value = movieTitles.value.filter((title) => {
      //   for (let i in Data) {
      //     if (r in listOfr) {
      //       return (title === Data[i].Series_Title)
      //     }
      //   }
      // })
      //test
    //}
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
  <div class="container-fluid">
    <!-- <a class="navbar-brand" href="#">Navbar</a> -->

    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNavDropdown">
      <div class="navbar-nav">
        <ul class="navbar-nav ms-auto mb-2 mb-lg-0">
          <li class="nav-item">
            <a :class="{'nav-link active': current == 'Home', 'nav-link': current != 'Home'}" aria-current="page" @click="current = 'Home'">test</a>
          </li>
          <li class="nav-item">
            <a :class="{'nav-link active': current == 'Watchlist', 'nav-link': current != 'Watchlist'}" aria-current="page" @click="current = 'Watchlist'">Watchlist</a>
          </li>
          <li class="nav-item">
            <a :class="{'nav-link active': current == 'MyMovies', 'nav-link': current != 'MyMovies'}" aria-current="page" @click="current = 'My Movies'">My Movies</a>
          </li>

          <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" data-bs-auto-close="outside" data-bs-toggle="dropdown" aria-expanded="false">
              Filter
            </a>
            <ul class="dropdown-menu">
              <li class="dropend">
                <a class="dropdown-item dropdown-toggle" data-bs-toggle="dropdown">
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
              <li>

              </li>
              <input class="form-control" type="text" v-model="rating" placeholder="Enter rating"/>
              <li>
                <input class="form-control" type="text" v-model="director" placeholder="Enter Director"/>
              </li>
              <li>
                <input class="form-control" type="text" v-model="cast" placeholder="Enter Cast"/>
              </li>
              <li v-if="current == 'Home'">
                <button type="button" class="btn btn-success" @click="fitleredMovies()">Add Filter</button>
              </li>
              <li v-else-if="current == 'MyMovies'">
                <button type="button" class="btn btn-success" @click="fitleredMyMovies()">Add Filter</button>
              </li>
              <li v-else>
                <button type="button" class="btn btn-success" @click="fitleredWatchlist()">Add Filter</button>
              </li>
              <li>
                <button type="button" class="btn btn-success">Reset Filters</button>
              </li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
  </div>
</nav>


  <div v-if="loaded">
    <div v-for="movie of movieTitles">
      <p>{{ movie }}</p>
    </div>
  </div>
  <div v-else>
    Loading Data...
  </div>
</template>
