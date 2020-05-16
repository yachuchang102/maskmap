<template>
    <div>
  <ul class="list-group"
  v-for="(item, i) in data"
  :key="item.properties.i"
  @click="emitNewCenter(item)"
  :id="i">
  <template>
  <a class="list-group-item text-left"
  :class="{ 'highlight': item.properties.mask_adult || item.properties.mask_child}">
  <h3>{{ item.properties.name }}</h3>
  <p class="mb-0">
  成人口罩：{{ item.properties['mask_adult'] }}| 兒童口罩：{{ item.properties['mask_child'] }}
  </p>
  <p class="mb-0">地址：<a target="_blank" :href="`https://www.google.com.tw/maps/place/${item.properties.address}`">
  {{item.properties.address}}</a>
  </p>
  <p class="mb-0">電話：{{item.properties.phone}}</p>
  </a>
  </template>
  </ul>
    </div>
</template>

<script>
/* eslint linebreak-style: ["error", "windows"] */

export default {
  name: 'Cards',
  props: {
    data: {
      type: Array,
    },
  },
  methods: {
    emitNewCenter(item) {
      const vm = this;
      const { coordinates } = item.geometry;
      const newCenter = [coordinates[1], coordinates[0]];
      vm.$emit('newCenter', newCenter);
    },
  },
};
</script>

<style>
.highlight {
  background: #e9ffe3;
}
</style>
