<template>
  <div id="app" ref="app">
    <div v-if="Object.keys(selectedCharacter).length">
      <div>
        <a @click="selectedCharacter = {}" style="color: black; border-radius: 10px;" class="btn white">RETURN TO SEARCH</a>
      </div>
      <CharacterDetail 
        :character="selectedCharacter" 
        :displayAttributes="displayAttributes"
        :selectCharacter="selectCharacter"
        :fetchCharacter="fetchCharacter"
      />
    </div>
    <div v-else>
      <h1 style="font-weight: lighter;" class="center-align">Character Explorer</h1>
      <div class="row center-align">
        <form class="col s12">
          <div class="row">
            <div class="input-field">
              <i class="material-icons prefix">search</i>
              <input v-model="query" id="icon_prefix" type="text" class="validate">
              <label for="icon_prefix">Keyword</label>
            </div>
          </div>
        </form>
      </div>
      <div class="row">
        <div class="center-align">
        {{ numCharacters }} results
        </div>
      </div>
      <div v-if="characters.length">
        <CharacterCard 
          v-for="character in characters" 
          :key="character.id"
          :character="character" 
          :selectCharacter="selectCharacter"
        />
      </div>
      <div v-else>
        Sorry! No characters found!
      </div>
      <div class="row">
        <div class="col s6 left-align">
          <a v-if="prevUrl" @click="fetchPage(prevUrl, 'prev')" style="color: black; border-radius: 10px;" class="btn white">Prev Page</a>
        </div>
        <div class="col s6 right-align">
          <a v-if="nextUrl" @click="fetchPage(nextUrl, 'next')" style="color: black; border-radius: 10px;" class="btn white right-align">Next Page</a>
        </div>
      </div>
      <div class="row">
        <div class="center-align">
        Page {{ page }} of {{ pages }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CharacterCard from './components/CharacterCard.vue'
import CharacterDetail from './components/CharacterDetail.vue'
import M from 'materialize-css'
import debounce from "lodash/debounce"

export default {
  name: 'App',
  components: {
    CharacterCard,
    CharacterDetail,
  },
  data: function() {
    return {
      characters: [],
      query: null,
      characterUrl: "https://rickandmortyapi.com/api/character/",
      selectedCharacter: {},
      displayAttributes: [
        {
          name: "Location",
          value: "location"
        },
        {
          name: "Species",
          value: "species"
        },
        {
          name: "Gender",
          value: "gender"
        },
        {
          name: "Living Status",
          value: "status"
        },
        {
          name: "Episodes Seen In",
          value: "episodeCount"
        },
      ],
      numCharacters: null,
      nextUrl: null,
      prevUrl: null,
      pages: null,
      page: null,
    }
  },
  mounted: function() { 
    M.AutoInit() 
  },
  created: function() {
    this.fetchCharacters()
  },
  watch: {
    // automatically search when keyword entered with a 500 ms debounce
    query: debounce(function() {
      this.fetchCharacters()
    }, 500),
  },
  methods: {
    fetchCharacters: async function() {
      let url = this.query ? `${this.characterUrl}?name=${this.query}` : this.characterUrl
      const characters = await fetch(url)
        .then(res => {
          if (!res.ok) throw res
          return res.json()
      }).catch(err => {
        console.error(err)
        return {}
      })
      // if response has a results array, unpack and format data
      if (characters && characters.results) {
        // unpack data and organize for use
        this.characters = characters.results.map(c => {
          return this.formatCharacter(c)
        })
        this.numCharacters = characters.info.count
        this.nextUrl = characters.info.next
        this.prevUrl = characters.info.prev
        this.pages = characters.info.pages
        this.page = 1
        // if no results, set data accordingly 
      } else {
        this.characters = []
        this.page = 0
        this.pages = 0
        this.nextUrl = null
        this.prevUrl = null
      }
    },
    selectCharacter: function(character) {
      // select a character to show the detail page, always scroll to the top
      this.selectedCharacter = character
      window.scroll(0, 0)
    },
    fetchCharacter: async function(r) {
      // fetch a single character
      const character = await fetch(r)
        .then(res => {
            if (!res.ok) throw res
            return res.json()
        }).catch(err => console.error(err))
      return this.formatCharacter(character)
    },
    formatCharacter: function(character) {
      // save character data in the format needed
      let characterData = {}
      characterData.id = character.id
      characterData.name = character.name
      characterData.image = character.image
      characterData.location = character.location && character.location.name
      characterData.locationUrl = character.location && character.location.url
      characterData.species = character.species
      characterData.gender = character.gender
      characterData.status = character.status
      characterData.episodeCount = character.episode && character.episode.length
      return characterData
    },
    fetchPage: async function(url, type) {
      // fetch next or previous page
      const characters = await fetch(url)
        .then(res => {
          if (!res.ok) throw res
          return res.json()
      }).catch(err => console.error(err))
      if (characters) {
        this.characters = characters.results.map(c => this.formatCharacter(c))
        this.nextUrl = characters.info.next
        this.prevUrl = characters.info.prev
        window.scroll(0, 0)
      }
      // increment or decrement page number depending on if this is a "next" or "prev" type request
      if (type == 'next') {
        this.page += 1
      } else if (type == 'prev') {
        this.page -= 1
      }
    },
  }
}
</script>

<style>

</style>
