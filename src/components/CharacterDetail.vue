<template>
  <div class="row">
    <div class="col s12 m7">
      <div class="card">
        <div class="card-image">
          <img :src="character.image">
        </div>
        <div class="card-content">
          <h3>{{ character.name }}</h3>
          <div v-for="attr in displayAttributes" :key="attr.name">
            <div>
              <h4>{{ attr.name }}</h4>
            </div>
            <div>
              <h5 style="color: darkgrey">{{ character[attr.value] }}</h5>
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