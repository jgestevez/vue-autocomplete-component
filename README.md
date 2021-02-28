## 🎯 install

```bash
$ yarn add vue-autocomplete-component
# npm install vue-autocomplete-component --save
```

## 🚀 Usage

```vue
<template>
  <Autocomplete
    :options="[
      { value: 'david', label: 'David' },
      { value: 'clement', label: 'Clement' },
      { value: 'leo', label: 'Leo' },
    ]"
    :searchApi="
      'https://5e0deade36b80000143db93c.mockapi.io/get-options?search'
    "
    :defaultValue="3"
    :onSearchComplete="onSearchComplete"
    :onSearchError="onSearchError"
    :onSelect="onSelect"
    :onOpen="onOpen"
    :onClose="onClose"
    :transformResult="transformResult"
  />
</template>

<script>
import { Autocomplete } from "vue-autocomplete-component";

export default {
  components: {
    Autocomplete,
  },
  methods: {
    onSearchComplete: function() {
      console.log("onSearchComplete");
    },
    transformResult: function(values) {
      console.log("transformResult with values is: ", values);
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
  },
};
</script>
```

## Props

| Prop name        | Prop type      | Description                                                                                                     |
| ---------------- | -------------- | --------------------------------------------------------------------------------------------------------------- |
| options          | Array          | The options to display in the dropdown menu, default is array object with required keys are "value" and "label" |
| searchApi        | String         | Api url to search when input typing                                                                             |
| defaultValue     | String, Number | initital value for dropdown                                                                                     |
| onSearchComplete | Func           | handle when search api completed                                                                                |
| onSearchError    | Func           | handle when search api error                                                                                    |
| onSelect         | Func           | handle when a item selected                                                                                     |
| onOpen           | Func           | handle when dropdown is opened                                                                                  |
| onClose          | Func           | handle when dropdown is closed                                                                                  |
| transformResult  | Func           | handle when data dropdown is change                                                                             |
| placeholderText  | String         | custom placeholder input                                                                                        |
| loadingText      | String         | custom loading text                                                                                             |
