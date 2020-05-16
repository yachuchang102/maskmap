<template>
<div class="h-100">
  <l-map
      :zoom="zoom"
      :minZoom="6"
      :center="userPosition"
      ref="mapInfo"
    >
      <l-tile-layer :url="url"></l-tile-layer>
      <l-marker-cluster>
      <l-marker v-for="(item, i) in allData"
      :lat-lng="[item['geometry']['coordinates'][1], item['geometry']['coordinates'][0]]"
      :key="i"
      @add="openPopup">
      <l-popup
      :options="{  autoPan: false, autoClose: false, closeOnClick: false, }">
      <a target="_blank" class="text-center d-block" :href="`https://www.google.com.tw/maps/place/${item.properties.address}`">{{ item['properties']['name'] }}</a>
        <hr>
              <p>
                成人口罩:{{ item.properties['mask_adult'] }}<br>
                兒童口罩:{{ item.properties['mask_child'] }}
              </p>
      </l-popup>
      </l-marker>
      </l-marker-cluster>
    </l-map>
</div>
</template>

<script>
/* eslint linebreak-style: ["error", "windows"] */
import {
  LMap, LTileLayer, LMarker, LPopup,
} from 'vue2-leaflet';
import 'leaflet/dist/leaflet.css';
import LMarkerCluster from 'vue2-leaflet-markercluster';
import 'leaflet.markercluster/dist/MarkerCluster.css';
import 'leaflet.markercluster/dist/MarkerCluster.Default.css';
import { Icon } from 'leaflet';

// eslint-disable-next-line
delete Icon.Default.prototype._getIconUrl  
// eslint-disable-next-line
Icon.Default.mergeOptions({  
  iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
  iconUrl: require('leaflet/dist/images/marker-icon.png'),
  shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
});
export default {
  name: 'OsmMap',
  props: ['allData', 'newCenter', 'userPosition'],
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LMarkerCluster,
    LPopup,
  },
  data() {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      zoom: 16,
      map: null,
    };
  },
  methods: {
    openPopup(e) {
      this.$nextTick(() => {
        e.target.openPopup();
      });
    },
  },
  mounted() {
    this.$nextTick(() => {
      this.map = this.$refs.mapInfo.mapObject;
    });
  },
};
</script>

<style lang="scss">
@import 'bootstrap/scss/bootstrap';
</style>
