<script>
import Vue from "vue";

export default Vue.extend({
  name: "Autocomplete",
  props: {
    options: {
      type: Array,
      required: false,
      default: () => [],
    },
    searchApi: {
      type: String,
      required: false,
    },
    defaultValue: {
      type: [String, Number],
      required: false,
      default: "",
    },
    onSearchComplete: {
      type: Function,
      required: false,
    },
    onSearchError: {
      type: Function,
      required: false,
    },
    onSelect: {
      type: Function,
      required: false,
    },
    onOpen: {
      type: Function,
      required: false,
    },
    onClose: {
      type: Function,
      required: false,
    },
    transformResult: {
      type: Function,
      required: false,
    },
    placeholderText: {
      type: String,
      required: false,
      default: "",
    },
    items: {
      type: Number,
      require: false,
      default: 5,
    },
    loadingText: {
      type: String,
      required: false,
      default: "Loading results...",
    },
    emptyResultText: {
      type: String,
      required: false,
      default: "No matching results found",
    },
  },

  data() {
    return {
      isOpen: false,
      results: [],
      resultApi: [],
      searchValue: "",
      isLoading: false,
      arrowCounter: -1,
      value: this.defaultValue,
      firstSearch: false,
    };
  },

  methods: {
    onSearch() {
      this.isLoading = true;
      fetch(`${this.searchApi}=${this.searchValue}`)
        .then((response) => response.json())
        .then((data) => {
          if (this.onSearchComplete) {
            this.onSearchComplete();
          }
          this.results = this.transformResult
            ? this.transformResult(data)
            : data;

          if (this.results.length == 0) {
            this.$refs.scrollContainer.style.height = "48px";
          } else if (this.results.length < this.items) {
            this.$refs.scrollContainer.style.height =
              this.results.length * 48 + "px";
          } else if (this.results.length >= this.items) {
            this.$refs.scrollContainer.style.height = this.items * 48 + "px";
          }

          this.isLoading = false;
          this.firstSearch = true;
          this.isOpen = true;
        })
        .catch((err) => {
          if (this.onSearchError) {
            this.onSearchError(err);
          }
          if (isFirst) {
            this.isLoading = false;
          }
        });
    },

    onChange() {
      this.$emit("input", this.searchValue);
      if (this.searchApi && this.searchValue) {
        clearTimeout(this.debounce);
        this.debounce = setTimeout(() => {
          this.onSearch();
        }, 300);
      } else if (!this.searchApi) {
        this.results = this.options.filter((item) => {
          return (
            item.label.toLowerCase().indexOf(this.searchValue.toLowerCase()) >
            -1
          );
        });
      }
      if (!this.searchValue) this.isOpen = false;
    },

    onFocus() {
      if (this.searchValue && !this.isLoading) this.isOpen = true;
      if (window.screen.availWidth < 768) this.isOpen = true;
    },

    setResult(result) {
      if (this.onSelect) {
        this.onSelect(result);
      }
      this.searchValue = result.label;
      this.value = result.value;
      this.isOpen = false;
    },

    onArrowDown(evt) {
      evt.preventDefault();
      if (!this.isOpen) this.isOpen = true;
      if (this.arrowCounter < this.results.length - 1) {
        this.arrowCounter = this.arrowCounter + 1;
      } else {
        this.arrowCounter = 0;
      }
      this.fixScrolling();
    },

    onArrowUp(evt) {
      evt.preventDefault();
      if (!this.isOpen) this.isOpen = true;
      if (this.arrowCounter >= 1) {
        this.arrowCounter = this.arrowCounter - 1;
      } else {
        this.arrowCounter = this.results.length - 1;
      }
      this.fixScrolling();
    },

    fixScrolling() {
      if (this.arrowCounter > -1) {
        const element = this.$refs.scrollContainer.querySelectorAll("li")[
          this.arrowCounter
        ];
        element.scrollIntoView({
          behavior: "smooth",
          block: "nearest",
          inline: "start",
        });
      }
    },

    onEnter() {
      const result = this.results[this.arrowCounter];
      this.setResult(result);
    },

    handleClickOutside(evt) {
      if (!this.$el.contains(evt.target)) {
        this.isOpen = false;
        this.arrowCounter = -1;
      }
    },

    onGoBack(e) {
      e.preventDefault();
      this.isOpen = false;
    },
  },

  watch: {
    options: function(val, oldValue) {
      if (val.length !== oldValue.length) {
        this.results = val;
        this.isLoading = false;
      }
    },
    isOpen: function(val) {
      if (val && this.onOpen) {
        this.onOpen();
      }
      if (!val && this.onClose) {
        this.onClose();
      }
    },
  },

  mounted() {
    document.addEventListener("click", this.handleClickOutside);
    if (!this.searchApi && this.options) {
      this.results = this.options;
      if (this.defaultValue) {
        const defaultLabel = this.options.find(
          (item) => item.value === this.defaultValue
        );
        if (!defaultLabel) return;
        this.searchValue = defaultLabel.label;
      }
    }
    this.$refs.scrollContainer.style.height = this.items * 48 + "px";
  },

  unmounted() {
    document.removeEventListener("click", this.handleClickOutside);
  },
});
</script>

<template>
  <div
    class="vue-autocomplete-component__autocomplete"
    :class="{ 'is-open': isOpen }"
  >
    <div class="vue-autocomplete-component__input-wrapper">
      <button @click="onGoBack">
        <svg width="24" height="24" viewBox="0 0 24 24" focusable="false">
          <path
            d="M20 11H7.83l5.59-5.59L12 4l-8 8 8 8 1.41-1.41L7.83 13H20v-2z"
          ></path>
        </svg>
      </button>
      <input
        type="text"
        :placeholder="placeholderText"
        @input="onChange"
        @focus="onFocus"
        v-model="searchValue"
        @keydown.down="onArrowDown"
        @keydown.up="onArrowUp"
        @keydown.enter="onEnter"
      />
    </div>
    <ul
      v-show="isOpen"
      class="vue-autocomplete-component__autocomplete-results"
      ref="scrollContainer"
    >
      <li
        class="vue-autocomplete-component__autocomplete-result"
        v-if="results.length === 0 && searchValue && !isLoading && firstSearch"
      >
        {{ emptyResultText }}
      </li>
      <li
        v-else
        v-for="(result, i) in results"
        :key="i"
        @click="setResult(result)"
        class="vue-autocomplete-component__autocomplete-result"
        :class="{ 'is-active': i === arrowCounter || result.value === value }"
      >
        {{ result.label }}
      </li>
    </ul>
  </div>
</template>

<style>
.vue-autocomplete-component__autocomplete {
  position: relative;
  width: 100%;
  background-color: #fff;
  border-radius: 4px;
  box-shadow: 0 1px 3px 0 rgba(60, 64, 67, 0.3),
    0 4px 8px 3px rgba(60, 64, 67, 0.15);
  font-family: Arial, sans-serif;
  overflow: visible;
  height: 48px;
}

.vue-autocomplete-component__input-wrapper input {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  background-color: transparent;
  box-sizing: border-box;
  color: #3c4043;
  outline: none;
  padding: 8px 16px;
  width: 100%;
  height: 48px;
  border: none;
  font-size: 16px;
}

.vue-autocomplete-component__input-wrapper {
  display: flex;
}

.vue-autocomplete-component__autocomplete.is-open {
  border-bottom-left-radius: 0;
  border-bottom-right-radius: 0;
  z-index: 100;
}
.vue-autocomplete-component__autocomplete.is-open
  .vue-autocomplete-component__input-wrapper {
  border-bottom: 1px solid #dadce0;
}

.vue-autocomplete-component__input-wrapper button {
  display: none;
  min-width: 48px;
  background: transparent;
  border: none;
  outline: none;
  padding: 0;
}

.vue-autocomplete-component__input-wrapper button svg path {
  fill: #5f6368;
}

.vue-autocomplete-component__autocomplete-results {
  padding: 0;
  margin: 0;
  height: 100%;
  overflow: auto;
  width: 100%;
  background: white;
  box-shadow: 0 1px 3px 0 rgba(60, 64, 67, 0.3),
    0 4px 8px 3px rgba(60, 64, 67, 0.15);
}

.vue-autocomplete-component__autocomplete-result {
  list-style: none;
  align-items: center;
  cursor: pointer;
  display: flex;
  height: 48px;
  padding-left: 16px;
  color: #3c4043;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.vue-autocomplete-component__autocomplete-result.is-active,
.vue-autocomplete-component__autocomplete-result:hover {
  background-color: #f8f9fa;
}

.vue-autocomplete-component__loading {
  padding-left: 16px;
  align-items: center;
  cursor: default;
  display: flex;
  height: 48px;
  pointer-events: none;
}

@media only screen and (max-width: 768px) {
  .vue-autocomplete-component__autocomplete.is-open {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    z-index: 1000;
    border-top-left-radius: 0;
    border-top-right-radius: 0;
  }

  .vue-autocomplete-component__autocomplete.is-open
    .vue-autocomplete-component__input-wrapper
    button {
    display: block;
  }

  .vue-autocomplete-component__autocomplete.is-open
    .vue-autocomplete-component__input-wrapper
    input {
    padding-left: 0;
  }

  .vue-autocomplete-component__autocomplete-results {
    max-height: 100%;
    height: 100% !important;
    box-shadow: none;
  }
}
</style>
