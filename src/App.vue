<template>
<div id="app">
  <loading :active.sync="isLoading"
    :can-cancel="true"
    :opacity="0.9"
    :background-color="'#000'"
    :color="'white'"
    :is-full-page="fullPage">
  </loading>
  <div class="row no-gutters">
  <div class="col-sm-4">
  <side-bar
  :allData.sync="data"
  :userPosition="userPosition"
  @getNewCenter="updatePosition"
  />
  </div>
   <div class="col-sm-8">
    <osm-map :userPosition="userPosition" :allData.sync="data" ref="mapInfo"/>
   </div>
  </div>
</div>
</template>

<script>
import $ from 'jquery';
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

import OsmMap from '@/components/OsmMap.vue';
import SideBar from '@/components/SideBar.vue';
// eslint-disable-next-line
console.log($);

export default {
  name: 'App',
  components: { OsmMap, Loading, SideBar },
  data() {
    return {
      fullPage: true,
      data: null,
      userPosition: [25.033671, 121.564427],
      newCenter: '',
    };
  },
  computed: {
    isLoading() {
      const vm = this;
      return !vm.data;
    },
  },
  methods: {
    getData() {
      const vm = this;
      vm.data = null;
      const url = 'https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json';
      vm.$http.get(url).then((response) => {
        vm.data = response.data.features;
      });
    },
    updatePosition(newPosition) {
      const vm = this;
      const { map } = vm.$refs.mapInfo;
      map.setView(newPosition, 18);
    },
  },
  mounted() {
    const vm = this;
    navigator.geolocation.getCurrentPosition(
      // 使用者接受存取地點，設定當前位置;使用者反對存取地點，預設地點101
      (res) => {
        const { latitude, longitude } = res.coords;
        vm.userPosition = [latitude, longitude];
      },
    );
    vm.getData();
  },
};
</script>

<style lang="scss">
@import 'bootstrap/scss/bootstrap';

#map {
 height: 100vh;
}
.highlight {
 background: #e9ffe3;
}
.toolbox {
 height: 100vh;
 overflow-y: auto;
 a {
 cursor: pointer;
 }
}
</style>
