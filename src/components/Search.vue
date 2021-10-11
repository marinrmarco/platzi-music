<template lang="pug">
  main
    transition(name="move")
      pm-notification(v-show="showNotification", :numOfTracks = "tracks.length")
        p(slot="body") {{ msgNotification }}

    transition(name="move")
      pm-loader(v-show="isLoading")
    section.section
      nav.nav
        .container
          input.input.is-large(
            type="text",
            placeholder="Buscar canciones",
            v-model="searchQuery",
            @keyup.enter="search")
          a.button.is-info.is-large(@click="search") Buscar
          a.button.is-danger.is-large &times;
      .container
        p
          small {{ searchMessage }}

      .container.results
        .columns.is-multiline
          .column.is-one-quarter(v-for="t in tracks")
            pm-track(
              v-blur="t.preview_url"
              :class="{ 'is-active': t.id === selectedTrack }",
              :track="t",
              @select="setSelectedTrack")
</template>

<script>
  import trackService from '@/services/track'
  import PmTrack from '@/components/Track.vue'

  import PmNotification from '@/components/shared/Notification.vue'
  import PmLoader from '@/components/shared/Loader.vue'


  export default ({
    name: 'App',
    components: {
      PmTrack,
      PmLoader,
      PmNotification
    },
    data () {
      return ({
        searchQuery: '',
        tracks: [],
        isLoading: false,
        selectedTrack: '',
        showNotification: false,
        msgNotification: '',
      })
    },
    computed: {
      searchMessage(){
        return `Encontrados ${this.tracks.length}`
      }
    },
    watch: {
      showNotification() {
        if(this.showNotification){
          setTimeout(()=>{
            this.showNotification = false
          }, 3000)
        }
      }

    },
    methods:{
      search () {
        this.isLoading = true
        if(!this.searchQuery){ return }

        trackService.search(this.searchQuery)
          .then(res => {
            (res.tracks.total > 0) ? this.msgNotification = `Se encontraron ${res.tracks.total} resultados`
            : this.msgNotification = 'No se encontraron Resultados'

            this.showNotification = true
            this.tracks = res.tracks.items
            this.isLoading = false
          })
      },
      setSelectedTrack(id){
        this.selectedTrack = id
      }
    }
  })

</script>
<style lang="scss">
  .results {
    margin-top: 50px;
  }
  .is-active {
    border: 3px #23d160 solid;
  }
</style>
