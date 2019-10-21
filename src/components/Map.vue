<template>
  <div id="map-content">
    <div id="map">

    </div>
  </div>
</template>

<script>
  import L from 'leaflet';

  export default {
    name: 'Map',

    props: {
      markers: Object
    },

    data () {
      return {
        map: null,
        plottedMarkers: [],
        myMarker: {},

        pinUser: L.icon({
          iconUrl: require('../assets/pin2.svg'),
          iconSize: [40, 100]
        }),

        pinOthers: L.icon({
          iconUrl: require('../assets/pin.svg'),
          iconSize: [40, 100]
        })
      };
    },

    methods: {
      initMap() {
        this.map = L.map('map').setView([-28.2636916,-52.4116074], 15);

        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
          attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(this.map);
      },
      
      plotMarkers(marker, idMyMarker = false) {
        if (marker.id && marker.lat && marker.lng)
        {
          if (this.plottedMarkers[marker.id] !== undefined)
            this.plottedMarkers[marker.id].setLatLng(new L.LatLng(marker.lat, marker.lng));
          else
            this.plottedMarkers[marker.id] = L.marker([marker.lat, marker.lng], {icon: (idMyMarker) ? this.pinUser 
                                                                                                    : this.pinOthers}).addTo(this.map);
        }
      }
    }, 

    watch: {
      markers(val, oldVal) {
        let arId       = [];
        let newMarkers = JSON.parse(JSON.stringify(val));
        
        for (let idx in newMarkers.markers)
        {
          arId.push(newMarkers.markers[idx].id);
          this.plotMarkers(newMarkers.markers[idx]);
        }
          
        this.plotMarkers(newMarkers.myMarker, true);

        let removedMarkers = this.plottedMarkers.filter((e, idx) => {
          if (!arId.includes(idx) && idx != newMarkers.myMarker.id)
            return true
        }, arId);

        removedMarkers.forEach((e, i) => {
          this.map.removeLayer(e);
        });
      }
    },

    mounted() {
      this.initMap();
    },
  }
</script>

<style>
  #map-content {
    height: 100%;
  }

  #map {
    height: 100%;
  }
</style>