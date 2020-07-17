<template lang="pug">
  main
    transition(name="move")
      pm-notification(v-show="showNotification == 1")
        div(slot="body").notification.is-danger
           p  No se encontraron resultados
    transition(name="move")
      pm-notification(v-show="showNotification == 2")
        div(slot="body").notification.is-success
          p Se encontraron {{cantidadResult}} resultados

    transition(name="move")
      pm-loader(v-show="isLoading")

    section.section(v-show="!isLoading")
      nav.nav
        .container
          input.input.is-large(
            type="text",
            placeholder="Bulscar Canciones",
            v-model="searchQuery",
            v-on:keyup.enter="search")
          a.button.is-info.is-large(@click="search") Buscar
          a.button.is-danger.is-large &times;
      .container
        p
          small {{ searchMessage }}

      .container.results
        .columns.is-multiline
          .column.is-one-quarter(v-for="t in tracks")
            pm-track(
              v-blur="t.preview_url",
              v-bind:class="{ 'is-active': t.id == selectedTrack }",
              v-bind:track="t",
              @select="setSelectedTrack")



</template>

<script>
import trackService from '@/services/track'

import PmTrack from '@/components/track.vue'

import PmNotification from '@/components/shared/notification.vue'
import PmLoader from '@/components/shared/Loader.vue'

export default {
  name: 'app',
  components: { PmTrack,PmLoader,PmNotification},
  data () {
    return {
    searchQuery: '',
    tracks: [],
    isLoading: false,
    showNotification: 0,
    cantidadResult: 0,

    selectedTrack : '',

    }
  },
  computed:
  {
    searchMessage()
      {
        return `Encontrados: ${this.tracks.length}`
      }
  },
  watch: {
    showNotification () {
    if (this.showNotification){
      setTimeout(()=> {
        this.showNotification = false
        }, 3000)
      }
    }
  },

  methods: {
  search()
    {
    if(!this.searchQuery){ return }

      this.isLoading =true
      trackService.search(this.searchQuery)
        .then(res =>{
          if ( res.tracks.total === 0){
            this.showNotification = 1
          }else
          {
            this.showNotification = 2
            this.cantidadResult = res.tracks.total
          }
          console.log(res)
          this.tracks = res.tracks.items
          this.isLoading = false
          })
    },

    setSelectedTrack(id) {
      this.selectedTrack = id
    }
  }


}
</script>

<style lang="scss">
.results
  {
    margin-top: 50px
  }
  .is-active {
    border : 3px #23d160 solid;
  }
</style>
