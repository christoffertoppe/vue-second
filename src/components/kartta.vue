<template>
    <div id="container">
        <div class="row">
            <div class="col-md-9">
                <div id="mapContainer" class="map">
                </div>

            </div>
            <div class="col-md-3">
                <Saa/>
                   <h4>Mapview</h4>
                <div class="form-check" v-for="layer in layers" :key="layer.id">
                    <label class="form-check-label">
                        <input
                                class="form-check-input"
                                type="checkbox"
                                v-model="layer.active"
                                @change="layerChanged(layer.id, layer.active)"
                        />
                        {{ layer.name }}
                    </label>
                </div>
            </div>
        </div>
    </div>

</template>


<script>
  import 'leaflet/dist/leaflet.css';
  import L from 'leaflet';
  import Saa from '@/components/saa.vue'

  const myIcon = L.icon({
    iconUrl: require('@/assets/map-marker-icon.png'),
    iconSize: [12,12],
    iconAnchor: [2, 2],
    popupAnchor: [0, -2]

  });

  export default {
    name: 'kartta',
    boo: false,
    components: {
      Saa,
    },
    data() {
      return {
        weather: Array,
        map: null,
        tileLayer: null,
        //layers: null,

        layers: [
          {
            id: 0,
            name: 'Bikestations',
            active: false,
            features: []

          },
        ],
        content: [],
        stations: Array,
      };
    },

    mounted() {
      this.getBikeStations();
      this.initMap();
      this.initLayers();
    },

    methods: {

   getBikeStations() {
        fetch('https://api.digitransit.fi/routing/v1/routers/hsl/index/graphql', {
          method: 'POST',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify({
            query: `{
            bikeRentalStations {
              stationId
              name
              bikesAvailable
              spacesAvailable
              lat
              lon
              allowDropoff

            }
          }`,
          }),
        }).then(res => res.json()).then(res => {
          this.content = res.data.bikeRentalStations;
          console.log('fillaripaikat',this.content);
          this.initStations();
        });
      },
      async initMap() {
        this.map = L.map('mapContainer').setView([60.4, 24.9], 10);
        this.tileLayer = await L.tileLayer(
            'https://cartodb-basemaps-{s}.global.ssl.fastly.net/rastertiles/voyager/{z}/{x}/{y}.png',
            {
              maxZoom: 18,
              attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>, &copy; <a href="https://carto.com/attribution">CARTO</a>',
            },
        );
        this.tileLayer.addTo(this.map);
      },
      initLayers() {
        this.layers.forEach((layer) => {
          const markerFeatures = layer.features.filter(feature => feature.type === 'marker');
          markerFeatures.forEach((feature) => {
            feature.leafletObject = L.marker(feature.coords, {icon: myIcon}).bindPopup(feature.name);
          });
        });
      },

      initStations() {
        this.content.forEach((station) => {
          station.leafletObject = L.marker([station.lat , station.lon ], {icon: myIcon}).bindPopup(station.name);
        });
        this.layers[0].features = this.content
      },

      layerChanged(layerId, active) {
        const layer = this.layers.find(layer => layer.id === layerId);
        layer.features.forEach((feature) => {
          if (active) {
            feature.leafletObject.addTo(this.map);
          } else {
            feature.leafletObject.removeFrom(this.map);
          }
        });
      },
    }
  };
</script>

<style scoped>
    #mapContainer {
        height: 500px;
        margin-left: 2px;
    }
 .col-md-3 {
     min-width: 30px;
 }

</style>