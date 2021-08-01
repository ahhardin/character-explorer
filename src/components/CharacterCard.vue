<template>
  <div class="row">
    <div class="col s12 m8 l12">
      <div @click="selectCharacter(character)" class="card horizontal">
        <div class="card-image">
          <img class="responsive-img" :src="character.image">
        </div>
        <div class="card-stacked">
          <div class="card-content">
            <h3>{{ character.name }}</h3>
            <h4 style="color: darkgrey">{{ character.location }}</h4>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
export default {
  name: 'CharacterCard',
  props: {
  character: Object,
  selectCharacter: Function
  }
}
</script>