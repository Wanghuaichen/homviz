<template>
  <div id="app">
    <Slide id="slide"/>
    <Selectors id="selectors"/>
    <Stats id="stats"/>
    <Homunculus id="homunculus"/>
  </div>
</template>

<script>
  import Homunculus from './components/Homunculus.vue';
  import Slide from './components/Slider.vue';
  import Stats from './components/Stats.vue';
  import Selectors from './components/Selectors.vue';
  import { firebase } from './firebaseConfig';
  export default {
    name: 'app',
    components: {
      Homunculus,
      Slide,
      Stats,
      Selectors
    },
    mounted() {
        const db = firebase.firestore();
        const getDatabaseInfo = async () => {
            try {
                const databaseInfoSnapshot = await db.collection('databaseInfo').limit(5).get()
                console.log("getDatabaseInfo")
                databaseInfoSnapshot.forEach(doc => {
                    // doc.data() is never undefined for query doc snapshots
                    const data = doc.data();
                    console.log(doc.id, " => ", data);
                    if(doc.id == "classes"){
                        this.$store.commit('loadGenres', data);
                    }
                    if(doc.id == "subclasses"){
                        this.$store.commit('loadSubgenres', data);
                    }
                    if(doc.id == "languages"){
                        this.$store.commit('loadLanguages', data);
                    }
                    if(doc.id == "bodyParts"){
                        this.$store.commit('loadBodyParts', data);
                        var homunculusState = {}
                        var bodyParts = Object.keys(data)
                        for (var index = 0; index < bodyParts.length; index++){
                          homunculusState[bodyParts[index]] = 0
                        }
                        this.$store.commit('loadHomunculusState', homunculusState)
                    }
                });
            } catch (err) {
                console.error(err)
            }
          
        }
        getDatabaseInfo()

        this.$store.subscribe((mutation) => {
          if( mutation.type == "changeSelectedSubgenre" || (mutation.type == "changeSelectedLanguage")){
            this.$store.dispatch('queryBooks')
          }
        })    
    },
  }
</script>

<style>
  html {
    height: 100%; overflow: hidden;
  }
  body {
    height: 100%; overflow: hidden;
  }
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: -8px;
    position: relative
  }
  #slide {
    position: absolute;
    bottom: 0px;
    height: 10%;
    background: linear-gradient( rgba(0, 0, 0, 0.25) 100%, #436f7c 100%),
  url('/static/bg.jpg') no-repeat center center fixed;
    z-index: 4;
  }
  #homunculus {
    width: 100%;
    height: 100%;
    position: relative;
  }
  #selectors {
    z-index: 4;
    position: absolute
  }
  #stats {
    z-index: 3;
    right: 0%;
    position: absolute
  }
</style>
