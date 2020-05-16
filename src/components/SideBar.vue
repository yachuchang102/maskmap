<template>
<div>
<div class="toolbox">
  <div class="sticky-top bg-white shadow-sm p-2">
  <div class="form-group d-flex">
  <label for="cityName" class="mr-2 col-form-label text-right">縣市</label>
  <div class="flex-fill">
  <select id="cityName" class="form-control"
  @change="
  areaSel = ''
  filterWay='bySelect'
  "
  v-model="citySel">
  <option value disabled selected hidden>-- Select One --</option>
  <option v-for="city in cities" :key="city">
    {{ city }}
  </option>
  </select>
  </div>
  </div>
  <div class="form-group d-flex">
  <label for="area" class="mr-2 col-form-label text-right">地區</label>
  <div class="flex-fill">
  <select id="area" class="form-control"
    v-model="areaSel">
  <option value disabled selected hidden>-- Select One --</option>
  <option
    v-for="area in areas" :key="area">
    {{ area }}
  </option>
  </select>
  </div>
  </div>
  <p class="mb-0 small text-muted text-right">請先選擇區域查看（綠色表示還有口罩）{{data}}</p>
  </div>
 <cards :data.sync="filterdData" :newCenter="newCenter" @newCenter="emitNewCenter"/>
  </div>
    </div>
</template>
<script>
/* eslint linebreak-style: ["error", "windows"] */
import Cities from '@/assets/cityName.json';
import Cards from '@/components/Cards.vue';

import $ from 'jquery';
import L from 'leaflet';
// eslint-disable-next-line
console.log($);
// eslint-disable-next-line
console.log(L);

export default {
  name: 'SideBar',
  props: ['allData', 'userPosition'],
  components: { Cards },
  data() {
    return {
      filterWay: '',
      data: null,
      citySel: '',
      areaSel: '',
      newCenter: '',
      distance: 5000,
    };
  },
  computed: {
    filterdData() {
      const vm = this;
      let data;
      if (vm.allData) {
        if (vm.filterWay === 'bySelect') {
          // 使用縣市，取得所有位置
          const filterCounty = vm.allData.filter((p) => p.properties.county === vm.citySel);
          data = vm.areaSel === '' ? filterCounty : filterCounty.filter((p) => p.properties.town === vm.areaSel);
        } else {
          // 預設，使用所在地，取得方圓五里內藥局（預設），並由近排至遠
          const latlng = L.latLng(vm.userPosition);
          data = vm.allData
            .filter((p) => {
              // eslint-disable-next-line no-param-reassign
              p.dist = latlng.distanceTo(L.latLng(p.geometry.coordinates[1],
                p.geometry.coordinates[0]));
              return p.dist < vm.distance;
            })
            .sort((a, b) => a.dist - b.dist);
        }
      }
      return data;
    },
    cities() {
      return Cities.map((city) => city.CityName);
    },
    areas() {
      const vm = this;
      let areaList;
      if (vm.citySel) {
        const areas = Cities.find((city) => city.CityName === vm.citySel);
        areaList = areas.AreaList.map((area) => area.AreaName);
      }
      return areaList;
    },
  },
  watch: {
    // 使用watch，change無法即時傳遞變更值
    filterdData() {
      const vm = this;
      if (vm.filterdData.length > 0) {
        const coordinates = [...vm.filterdData[0].geometry.coordinates];
        vm.newCenter = [coordinates[1], coordinates[0]];
        vm.emitNewCenter(vm.newCenter);
      }
    },
  },
  methods: {
    emitNewCenter(newCenter) {
      const vm = this;
      vm.$emit('getNewCenter', newCenter);
    },
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
