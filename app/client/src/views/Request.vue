<template>
  <v-container id="request">
    <v-container grid-list-xs fluid fill-height align-center justify-center>
      <form class="request-form blurred-bg tinted" @submit.prevent="getPath" action='#' method="GET">
        <!-- Left Form -->
        <v-layout row wrap>
          <v-flex xs12 md6>
            <section class="request-form-left">
              <v-layout row wrap>
                <v-flex xs12 md10 align-center class="prevent-scroll">
                  <v-form>
                    <div class="request-form-left-select-fields">
                      <!-- CHAMP DEPART -->
                      <multi-select class="subheading" :label='"Départ"' :maxHeight='500' :placeholder='"Station, lieu ou adresse"' @stationSelected='showStartOnMap'
                        v-model="query.start" />
                      <!-- CHAMP ARRIVEE -->
                      <multi-select class="subheading" :label='"Arrivée"' :maxHeight='500' :placeholder="'Station, lieu ou adresse'" @stationSelected='showEndOnMap'
                        v-model="query.end" />
                    </div>
                    <!-- CHAMP OPTIONS -->
                    <v-expansion-panel white>
                      <v-expansion-panel-content :value='true'>
                        <div class="subheading" slot="header">Options</div>
                        <v-card white>
                          <v-card-text>
                            <v-layout row wrap class="options-container">
                              <!--<v-layout xs6 class="options-moyens">-->
                                Moyens:
                              <v-container class="options-moyens" white>
                                <v-select color="black" label="Moyens" :items="moyens_items" v-model="moyens_model" multiple max-height="400" hint="Choisir vos moyens de transport"
                                  persistent-hint></v-select> 
                              </v-container>
                              <!--</v-layout>-->
                              <br>
                              <!--<v-layout xs6 class="options-facteurs">-->
                              <v-container class="options-facteurs" white>
                                <v-select color="black" label="Facteur" :items="facteurs_items" v-model="facteurs_model" hint="Choisir votre facteur" persistent-hint
                                  single-line></v-select>
                              </v-container>
                              <!--</v-layout>-->
                            </v-layout>
                          </v-card-text>
                        </v-card>
                      </v-expansion-panel-content>
                    </v-expansion-panel>

                  </v-form>
                </v-flex>

              </v-layout>
            </section>
          </v-flex>

          <!-- RIGHT FORM -->
          <v-flex xs12 md6>
            <v-layout row wrap class="request-form-right prevent-scroll">
              <v-flex x12 class="map-container">
                <map-section name="request-map" :coords='mapCoords' class="map"></map-section>
              </v-flex>
              <v-flex x12 class="center-button">
                <v-btn type="submit" color="primary" dark large>
                  <v-icon left dark>forward</v-icon>Avoir le Chemin</v-btn>
              </v-flex>
            </v-layout>
          </v-flex>

        </v-layout>
      </form>
    </v-container>

  </v-container>
</template>



<script>
  /*  eslint-disable */
  import MapSection from '@/components/Map'
  import MultiSelect from '@/components/MultiSelect'

  export default {
    name: 'request',
    components: {
      'map-section': MapSection,
      'multi-select': MultiSelect
      // put custom components here
    },
    data() {
      return {
        moyens_model: [0, 1, 2], // multiple select
        facteurs_model: [], // one select
        moyens_items: ['Bus', 'Tramway', 'Marche'],
        facteurs_items: [
          'Min de temps',
          'Min de correspondance',
          'Min de marche'
        ],
        mapCoords: [null, null],
        query: {
          // Requete à envoyer à Response
          start: '',
          end: '',
          bus: true,
          tram: true,
          walk: true,
          facteurs: 0
        }
      }
    },
    methods: {
      showStartOnMap(s) {
        if (s.coord) {
          this.$set(this.mapCoords, 0, {
            lat: s.coord.lat,
            lng: s.coord.lon
          })
        }
      },
      showEndOnMap(s) {
        if (s.coord) {
          this.$set(this.mapCoords, 1, {
            lat: s.coord.lat,
            lng: s.coord.lon
          })
        }
      },
      getPath(event) {
        this.$router.push({
          name: 'response',
          query: this.query
        })
      }
    }
  }

</script>

<style lang="scss" scoped>
@import '../assets/scss/variables';

#request {
  position: relative;
  background-image: $request-page-bg-img;
  width: 100%;
  max-width: 100%;
  display: flex;
  color: black;
}

.blurred-bg {
  background: $request-page-bg-blurred-img,
    -webkit-linear-gradient(
        0deg,
        rgba(255, 255, 255, 0.2),
        rgba(255, 255, 255, 0.2)
      ) fixed center;
  background-repeat: no-repeat;
  background-size: cover; // NOTE: This is a hack to make the background fixed in SECOND section (FullPageJs)
  background-position-y: 100vh;
  background-attachment: fixed;
}

.request-form {
  display: block;
  padding: 10px;
  width: $form-width;
  height: $form-height;
  margin-left: auto;
  margin-right: auto;
  border: 5px solid rgba(255, 255, 255, 0.5);
  overflow: hidden;

  &-left {
    margin-top: 10px;
    margin-left: 20px;
    max-height: $form-height;

    &-select-fields {
      height: 0.35 * $form-height;
    }
  }

  &-right {
    overflow: hidden;
    position: relative;
    max-height: $form-height;
    margin: auto;
    padding-right: 30px;
  }
}

.map-container {
  overflow: hidden;
  position: relative;
  padding: 0;
  margin-top: 20px;
  width: 100%;
  height: 60%;
  border: 1px solid black;

  .map {
    position: relative;
    width: 100%;
    height: 100%;
  }
}

.center-button {
  width: 100%;
  text-align: center;

  button {
    // padding
  }
}

.options-container {
  margin-top: 30px;
  border: 3px solid rgba(255, 255, 255, 0.2);
}

@media screen and (max-height: 800px) {
  .request-form {
    margin-top: 35px;
    height: $form-height-min-screen;
  }
}

@media screen and (max-width: 400px) {
  .request-form {
    margin-top: 35px;
    height: $form-height-min-screen;
    width: $form-width-min-screen;
  }

  .map-container {
    display: none;
  }
}

.request-form-left-select-fields {
  color: white;
}
</style>
