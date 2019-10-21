<template>
    <md-app md-mode="reveal">
      <md-app-toolbar class="md-primary">
        <md-button class="md-icon-button" @click="menuVisible = !menuVisible">
          <md-icon>menu</md-icon>
        </md-button>
        <span class="md-title">Maps Realtime</span>
      </md-app-toolbar>

      <md-app-drawer :md-active.sync="menuVisible">
        <md-toolbar class="md-transparent" md-elevation="0">Active Markers</md-toolbar>

        <md-list>
          <md-list-item v-for="marker in markers.markers" :key="marker.id">
              <div style="display:flex; flex-flow:row wrap;">
                <div style="display: flex;">
                  <md-icon>room</md-icon>
                  <span class="md-list-item-text">{{marker.id}}</span>
                </div>
                <div style="display: flex;">
                  <small style="margin: 5px;">Lat: {{marker.lat}}</small>
                  <small style="margin: 5px;">Lng: {{marker.lng}}</small>
                </div>

              </div>
          </md-list-item>
        </md-list>
      </md-app-drawer>

      <md-app-content>
        <Map :markers="markers"/>
      </md-app-content>
    </md-app>
</template>

<script>
  import Map  from './Map.vue';

  export default {
    name: 'Template',
    components: {
      Map
    },

    data: function() {
      return {
        menuVisible: false,

        positionOptions: {
          enableHighAccuracy: true, 
          timeout: 15000, 
          maximumAge: 0
        },

        markers: {}
      };
    },

    methods: {
      wsConnect() {
        this.ws = new WebSocket('ws://localhost:8070');

        this.ws.onopen = () => { 
          this.sendMyPosition();
        }

        this.ws.onmessage = (response) => {
          this.markers = JSON.parse(response.data);
        }
      },

      wsSendMessage(data) {
        if (this.ws.readyState !== this.ws.OPEN) {
          console.log('Problemas na conexão. Tentando reconectar...');

          this.wsConnect(() => {
            this.wsSendMessage(data);
          });

          return;
        }

        this.ws.send(JSON.stringify(data));
      },

      sendMyPosition() {
        navigator.geolocation.watchPosition(position => {
          const data = {
            coords: {
              lat: position.coords.latitude, 
              lng: position.coords.longitude
            },

            clientId: this.clientId
          }

          if (data.coords.lat != null && data.coords.lng != null)
            this.wsSendMessage(data);

        }, console.log, self.positionOptions);
      }
    },

    created() {
      this.wsConnect();
    }
  }
</script>

<style scoped>
  .md-app {
    height: 100vh;
    border: 1px solid rgba(#000, .12);
  }

  .md-drawer {
    width: 230px;
    max-width: calc(100vw - 125px);
  }

  .md-content{
    padding: 0;
  }
</style>
