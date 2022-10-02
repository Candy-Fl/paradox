<template>
  <div class="document-search">
    <IconSearch class="document-search__icon" />
    <input class="document-search__input" v-model.trim="searchText" type="text">
    <DocumentsButton v-if="searchText.length" @pressed="clearSearch" :view="ButtonView.ICON_DELETE"/>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import IconSearch from "@/assets/icons/icon-search.svg?component"
import DocumentsButton, { ButtonView } from "@/components/Documents/DocumentsButton.vue";

export default defineComponent({
  name: "DocumentSearch",
  data: () => {
    return {
      searchText: '',
    };
  },
  components: {
    IconSearch,
    DocumentsButton,
  },
  computed: {
    ButtonView () {
      return ButtonView;
    }
  },
  methods: {
    clearSearch () {
      this.searchText = '';
    }
  },
  watch: {
    searchText (text) {
      this.$emit('search', text.toLowerCase());
    }
  }
});
</script>

<style lang="scss">
.document-search {
  display: flex;
  justify-content: space-between;
  border-bottom: 1px solid var(--color-blue);
  width: 564px;
  padding-bottom: 12px;

  &__input {
    width: 537px;
    border: none;
    margin-left: 12px;
    font-family: 'Firasans-Regular', Roboto, sans-serif;
    font-weight: 400;
    line-height: 108%;
    font-size: 15px;

    &:focus-visible {
      outline: none;
    }
  }
}
</style>
