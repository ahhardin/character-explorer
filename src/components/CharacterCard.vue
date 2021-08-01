<template>
  <div class="row">
    <div class="col s12 m8 l12">
      <div @click="selectCharacter(character)" class="card horizontal">
        <div class="card-image">
          <img class="responsive-img" :src="character.image">
        </div>
        <div class="card-stacked">
          <div class="card-content">
            <h5 style="font-weight: bold">{{ character.name }}</h5>
            <h6 style="color: darkgrey">{{ character.location }}</h6>
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