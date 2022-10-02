<template>
  <div class="documents-group">
    <div class="documents-group__wrapper">
      <DocumentsButton
        class="documents-group__button-show"
        :view="ButtonView.ICON_ARROW"
        @click="toggleChildren"
      />

      <div class="documents-group__info">
        <span class="documents-group__title">{{ item.title }}</span>
        <span class="documents-group__description">{{item.description}}</span>
      </div>

      <div class="documents-group__controls">
        <DocumentsButton
          class="documents-group__edit"
          :view="ButtonView.ICON_EDIT"
          @click="editGroup"
        />

        <DocumentsButton
          class="documents-group__delete"
          :view="ButtonView.ICON_DELETE_DOC"
          @click="deleteItem"
        />

        <DocumentsButton
          class="documents-group__replace"
          :view="ButtonView.ICON_REPLACE"
        />
      </div>
    </div>

    <transition name="slide-fade">
      <div
        v-if="showChildren"
        class="documents-group__children"
      >
        <slot/>
      </div>
    </transition>
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
import type { DocumentsItemType } from "./DocumentsItem.vue";

export type DocumentsGroupType = {
  title: string,
  description: string,
  children?: DocumentsItemType[]| [],
}

export default defineComponent({
  name: "DocumentsGroup",
  components: {
    DocumentsButton,
  },
  data: () => {
    return {
      showChildren : true,
    }
  },
  props: {
    item: {
      type: Object as PropType<DocumentsGroupType>,
      required: true,
    },
  },
  computed: {
    ButtonView () {
      return ButtonView;
    }
  },
  methods: {
    toggleChildren () {
      if (this.item.children) {
        this.showChildren = !this.showChildren;
      }
    },
    deleteItem () {
      this.$emit('deleteGroup', this.item);
    },
    editGroup () {
      this.$emit('editGroup', this.item);
    }
  }
});
</script>

<style lang="scss">
.documents-group {

  &__wrapper {
    align-items: center;
    display: flex;
    padding: 13px 16px;
    border: 1px solid var(--color-border);
    border-collapse: collapse;
  }

  &__title {
    font-size: 15px;
    font-weight: 500;
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

  &__children {
    margin-left: 16px;
  }

  .slide-fade-enter-active {
    animation: slide-fade .1s;
  }

  .slide-fade-leave-active {
    animation: slide-fade .1s reverse;
  }

  @keyframes slide-fade {
    0% {
      opacity: 0;
    }
    100% {
      opacity: 1;
    }
  }
}
</style>
