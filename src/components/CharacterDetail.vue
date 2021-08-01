<template>
  <div class="enter-align row">
    <div class="col s12">
      <div class="card">
        <div class="card-image">
          <img :src="character.image">
        </div>
        <div class="card-content">
          <h4 style="font-weight: bold">{{ character.name }}</h4>
          <div v-for="attr in displayAttributes" :key="attr.name">
            <div>
              <h5>{{ attr.name }}</h5>
            </div>
            <div>
              <h6 style="color: darkgrey">{{ character[attr.value] }}</h6>
            </div>
          </div>
        </div>
        <div v-for="nearbyCharacter in nearbyCharacters" :key="nearbyCharacter.id">
          <CharacterCard 
            :character="nearbyCharacter" 
            :selectCharacter="selectCharacter"
          />
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import CharacterCard from './CharacterCard.vue'

export default {
  name: 'CharacterDetail',
  components: {
    CharacterCard,
  },
  props: {
    character: Object,
    displayAttributes: Array,
    selectCharacter: Function,
    fetchCharacter: Function,
  },
  data: function() {
    return {
      nearbyCharacters: []
    }
  },
  created: function() {
    // on load, fetch all the nearby characters
    this.fetchNearbyCharacters()
  },
  methods: {
    fetchNearbyCharacters: async function() {
    if (this.character.locationUrl) {
      const location = await fetch(this.character.locationUrl)
        .then(res => {
          if (!res.ok) throw res
          return res.json()
        }).catch(err => console.error(err))
      if (location && location.residents) {
        // limit to 10 nearby characters, and filter out the character whose page we're on
        const nearbyCharacters = await Promise.all(location.residents.slice(0,10).map(async r => {
          return await this.fetchCharacter(r)
        }))
        this.nearbyCharacters = nearbyCharacters.filter(nc => nc.id !== this.character.id)
      }
    }
  },
  }
}
</script>