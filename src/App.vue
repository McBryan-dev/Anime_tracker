<template class="m-0 p-0 box-border font-serif">
  <main class="p-6 gap-4 mx-auto text-center" >

    <h1 class="text-6xl font-semibold font-serif" >Mc Anime Tracker</h1>
    <br />
    <form @submit.prevent="searchAnime" class="justify-center flex gap-3">
      <input 
        type="text"
        placeholder="Search an anime"
        v-model="query"
        @input="handleInput"
        class="p-1 h-8 mt-auto mb-auto"
      >

      <button type="submit" class="bg-zinc-300 p-2">Search</button>
    </form>

    <div v-if="search_results.length > 0" class="results">
      
      <div class="results" v-for="anime in search_results" :key="anime.id">
        
        <img :src="anime.images.jpg.image_url" />

        <div class="details">
          <h3>{{anime.title}}</h3>
          <p v-if="anime.synopsis">{{anime.synopsis}}...</p>
          <span class="flex-1"></span>
          <button @click="addAnime(anime)">ADD TO MY ANIME</button>
        </div>
      </div>

    </div>

    <div v-if="my_anime.length > 0" class="text-center mt-3">
      <h2 class="text-2xl font-bold">My Anime</h2>
    </div>
      
    <div class="grid grid-cols-3 align-center justify-center items-center text-cenetr">

      <div class="myAnime mt-4 grid gap-4" v-if="my_anime.length > 0">

        <br />
        <div v-for="anime in my_anime_asc" :key="anime.id" class="anime text-start p-2 mr-auto">
          <img :src="anime.image" class="img_fluid" />
          <h3 class="p-2 text-xl font-semibold">{{anime.title}}</h3>
          <div class="flex-1"></div>
          <span class="episodes p-2 text-xl">
            {{anime.watched_episodes}} / {{anime.total_episodes}}
          </span>
          <button 
            v-if="anime.total_episodes !== anime.watched_episodes"
            @click="increaseWatch(anime)"
            class="p-1"
          >
            +
          </button> 

          <button 
            v-if="anime.watched_episodes > 0"
            @click="decreaseWatch(anime)"
          >
            -
          </button> 
        </div>

      </div>

    </div>

  </main>
</template>

<script setup>

  import {ref, computed, onMounted} from 'vue';

  const query = ref('');
  const my_anime = ref([]);
  const search_results = ref([])

  const my_anime_asc = computed(() => {
    return my_anime.value.sort((a, b) => {
      return a.title.localeCompare(b.title) 
    })
  })

  const searchAnime = () => {
    const url = `https://api.jikan.moe/v4/anime?q=${query.value}`

    fetch (url)
      .then(res => res.json())
      .then(res => {
        search_results.value = res.data
      })
  }

  const handleInput = (e) => {
    if(!e.target.value) {
      search_results.value = []
    }
  }

  const addAnime = anime => {
    search_results.value = []
    query.value = ''

    my_anime.value.push({
      id: anime.mal_id,
      title: anime.title,
      image: anime.images.jpg.image_url,
      total_episodes: anime.episodes,
      watched_episodes: 0
    })

    localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
  }

  const increaseWatch = anime => {
    anime.watched_episodes++
    localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
  }

  const decreaseWatch = anime => {
    anime.watched_episodes--
    localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
  }

  onMounted(() => {
    my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
  })
  
</script>

<style scoped>

</style>