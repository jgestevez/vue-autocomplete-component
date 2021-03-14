<script>
import Vue from "vue";
import { Autocomplete } from "@/entry.esm";

export default Vue.extend({
  name: "ServeDev",
  components: {
    Autocomplete,
  },
  methods: {
    onSearchComplete: function() {
      
    },
    transformResult: function(values) {
      
      return values;
    },
    onSearchError: function(err) {
      
    },
    onSelect: function(value) {
      
    },
    onOpen: function() {
      
    },
    onClose: function() {
      
    },
  },
});
</script>

<template>
  <div id="app">
    <h1>Autocomplete Component</h1>
    <div>
      <Autocomplete
        :options="[
          { value: 'david', label: 'David' },
          { value: 'clement', label: 'Clement' },
          { value: 'leo', label: 'Leo' },
        ]"
        :searchApi="
          'https://5e0deade36b80000143db93c.mockapi.io/get-options?search'
        "
        :onSearchComplete="onSearchComplete"
        :onSearchError="onSearchError"
        :onSelect="onSelect"
        :onOpen="onOpen"
        :onClose="onClose"
        :defaultValue="3"
        :transformResult="transformResult"
        :placeholderText="'Enter to search'"
        :loadingText="'Loading...'"
        :emptyResultText="'No result'"
      />
      <input type="text" style="width:300px;height:30px;background:white;margin-top:10px">
    </div>
  </div>
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@100&display=swap");

#app {
  width: 100%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: "Roboto", sans-serif;
}
</style>
