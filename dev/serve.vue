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
      console.log("onSearchComplete");
    },
    transformResult: function(values) {
      console.log("transformResult with values is: ", values);
      return values;
    },
    onSearchError: function(err) {
      console.log("onSearchError with error is: ", err);
    },
    onSelect: function(value) {
      console.log("onSelect with value is: ", value);
    },
    onOpen: function() {
      console.log("onOpen");
    },
    onClose: function() {
      console.log("onClose");
    },
  }
});
</script>

<template>
  <div id="app">
    <h1>Autocomplete Component</h1>
    <Autocomplete
      :options="[
        {value: 'david', label: 'David'},
        {value: 'clement', label: 'Clement'},
        {value: 'leo', label: 'Leo'},
      ]"
      :searchApi="'https://5e0deade36b80000143db93c.mockapi.io/get-options?search'"
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
  </div>
</template>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@100&display=swap');

  #app {
    width: 100%;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: 'Roboto', sans-serif;
  }
</style>
