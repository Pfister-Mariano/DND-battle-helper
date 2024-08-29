<template>
   <Transition>
      <Overlay v-if="searchModal" @close="searchModal = false">
         <SearchOverlay v-model="selectedMonster" />
      </Overlay>
   </Transition>

   <section class="monsterList">
      <Monster v-for="(monster, index) in selectedMonster" :monster="monster" :monsterIndex="index" @copyMonster="copyMonster"/>
      <div class="monsterSearchButton" @click="searchModal = true"><i class="fa-solid fa-plus"></i></div>
   </section>
</template>

<script setup>
import Monster from '@/components/Monster.vue'
import SearchOverlay from '@/components/SearchOverlay.vue'
import Overlay from '@/components/Overlay.vue'

import { ref } from 'vue';

const selectedMonster = ref([])
const searchModal = ref(false)

function copyMonster(monsterToCopy) {
   console.log();
   selectedMonster.value.push(monsterToCopy)
}

</script>

<style lang="scss">
@import './styles/main.scss';

#app{
   padding-top: 2.5%;
}

.v-enter-active,
.v-leave-active {
   transition: opacity .2s ease;
}

.v-enter-from,
.v-leave-to {
   opacity: 0;
}

section.monsterList {
   display: flex;
   margin: auto;
   gap: 40px;
   max-width: 1720px;
   height: 90vh;
   flex-wrap: wrap;
   justify-content: space-around;
   .monsterSearchButton{
      height: 100%;
      width: 100%;
      max-width: 400px;
      max-height: 580px;
      border-radius: 25px;
      background-color: $color-blue-50;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 50px;
      transition: all ease-in-out 0.2s;
      &:hover{
         background-color: $color-blue-highlight;
         color: $color-blue-25;
         opacity: .6;
      }
   }
}


</style>