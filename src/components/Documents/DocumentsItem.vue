<template>
  <div
    class="documents-item"
    v-if="item"
  >
    <div class="documents-item__wrapper">
      <div class="documents-item__info">
        <span class="documents-item__title">{{ item.title }}</span>
        <span class="documents-item__description">{{item.description}}</span>
      </div>
      <div class="documents-item__controls">
        <DocumentsButton
          class="documents-item__edit"
          :view="ButtonView.ICON_EDIT"
          @click="editItem"
        />
        <DocumentsButton
          class="documents-item__delete"
          :view="ButtonView.ICON_DELETE_DOC"
          @click="deleteItem"
        />
        <DocumentsButton
          class="documents-item__replace"
          :view="ButtonView.ICON_REPLACE"
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
/**
 * Компонент рендера документа/группы
 *
 * @module DocumentsItem
 *
 * @params {String} item - документ или группа, который будут рендерится в компоненте
 */
import { defineComponent } from "vue";
import type { PropType } from "vue";
import DocumentsButton, { ButtonView } from "@/components/Documents/DocumentsButton.vue";

export type DocumentsItemType = {
  title: string,
  description: string,
}

export default defineComponent({
  name: "DocumentsItem",
  components: {
    DocumentsButton,
  },
  props: {
    item: {
      type: Object as PropType<DocumentsItemType>,
      default: () => {},
    },
  },
  computed: {
    ButtonView () {
      return ButtonView;
    }
  },
  methods: {
    deleteItem () {
      this.$emit('deleteItem', this.item);
    },
    editItem () {
      this.$emit('editItem', this.item);
    }
  }
});
</script>

<style lang="scss">
.documents-item {
  border: 1px solid var(--color-border);
  border-collapse: collapse;
  padding: 10px 16px;

  &__wrapper {
    align-items: center;
    display: flex;
  }

  &__button-show {
    margin-right: 14px;
  }

  &__info {
    display: flex;
    column-gap: 15px;
  }

  &__description {
    font-family: 'Firasans-Regular', Roboto, sans-serif;
    font-size: 11px;
    color: var(--color-text-gray);
  }

  &__controls {
    display: flex;
    column-gap: 22px;
    margin-left: auto;
  }
}
</style>
