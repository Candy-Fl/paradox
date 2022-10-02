<template>
  <div class="documents-modal">
    <div class="documents-modal__wrapper">
      <div class="documents-modal__header">
        <h2 class="documents-modal__title">{{ title }}</h2>

        <DocumentsButton
          :view="ButtonView.ICON_DELETE"
          @click="close"
        />
      </div>

      <div class="documents-modal__fields">
        <label
          class="documents-modal__field-label"
        >
          {{ inputTitleText }}

          <input
            class="documents-modal__field-input"
            v-model="titleValue"
          >
        </label>

        <label
          class="documents-modal__field-label"
        >
          {{ inputDescriptionText }}

          <input
            class="documents-modal__field-input"
            v-model="descriptionValue"
          >
        </label>
      </div>

      <div class="documents-modal__buttons-wrapper">
        <button
          class="documents-modal__button"
          @click="save"
        >
          Сохранить
        </button>
        <button
          class="documents-modal__button"
          @click="clearFields"
        >
          Очистить
        </button>
      </div>
    </div>

    <div
      class="documents-modal__overlay"
      @click="close"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import DocumentsButton, { ButtonView } from "@/components/Documents/DocumentsButton.vue";
import type { DocumentsGroupType } from "@/components/Documents/DocumentsGroup.vue";
import type { DocumentsItemType } from "@/components/Documents/DocumentsItem.vue";

export enum ModalView {
  ADD_GROUP = "addGroup",
  ADD_DOCUMENT = "addDocument",
  EDIT_GROUP = "editGroup",
  EDIT_DOCUMENT = "editItem",
  EDIT_ITEM_IN_GROUP = "editItemInGroup",
}
export default defineComponent({
  name: "DocumentsModal",
  components: {
    DocumentsButton,
  },
  props: {
    view: {
      type: String,
      default: ModalView.ADD_GROUP,
    },
    group: {
      type: Object,
      default: {},
    },
    document: {
      type: Object,
      default: {},
    }
  },
  data: () => {
    return {
      titleValue: '',
      descriptionValue: '',
    }
  },
  computed: {
    ButtonView () {
      return ButtonView;
    },
    title () {
      switch (this.view) {
        case ModalView.ADD_GROUP: return 'Добавить категорию';
        case ModalView.ADD_DOCUMENT: return 'Добавить документ';
      }
    },
    inputTitleText () {
      return  this.view === ModalView.ADD_GROUP ? 'Название группы' : 'Название документа';
    },
    inputDescriptionText () {
      return this.view === ModalView.ADD_GROUP ? 'Описание группы' : 'Описание документа';
    }
  },
  methods: {
    close () {
      this.$emit('close');
    },
    clearFields () {
      this.descriptionValue = '';
      this.titleValue = '';
    },
    save () {
      switch (this.view) {
        case ModalView.ADD_GROUP:
          const group: DocumentsGroupType = {
            title: this.titleValue,
            description: this.descriptionValue,
            children: [],
          }
          return this.$emit('addNewGroup', group);

        case ModalView.ADD_DOCUMENT:
          const item: DocumentsItemType = {
            title: this.titleValue,
            description: this.descriptionValue,
          }
          return this.$emit('addNewItem', item);

        case ModalView.EDIT_GROUP:
          const newGroup: DocumentsGroupType = {
            title: this.titleValue,
            description: this.descriptionValue,
            children: this.group.children,
          }
          return this.$emit('editGroup', newGroup);

        case ModalView.EDIT_DOCUMENT:
          const newDocument: DocumentsItemType = {
            title: this.titleValue,
            description: this.descriptionValue,
          }
          return this.$emit('editDocument', newDocument);

        case ModalView.EDIT_ITEM_IN_GROUP:
          const index = this.group.children.indexOf(this.document);
          let children = this.group.children;
          const newChild: DocumentsItemType = {
            title: this.titleValue,
            description: this.descriptionValue,
          };
          children[index] = newChild;
          const groupToEdit: DocumentsGroupType = {
            title: this.group.title,
            description: this.group.description,
            children: children,
          };
          return this.$emit('editGroup', groupToEdit);
      }
    }
  },
  mounted () {
    if (this.view === ModalView.EDIT_GROUP) {
      this.descriptionValue = this.group.description;
      this.titleValue = this.group.title;
    }
    if (this.view === ModalView.EDIT_DOCUMENT) {
      this.descriptionValue = this.document.description;
      this.titleValue = this.document.title;
    }
    if (this.view === ModalView.EDIT_ITEM_IN_GROUP) {
      this.descriptionValue = this.document.description;
      this.titleValue = this.document.title;
    }
  }
});
</script>

<style lang="scss">
.documents-modal {
  position: fixed;
  z-index: 99;
  top: 50px;
  bottom: 50px;
  width: 100%;

  &__wrapper {
    display: flex;
    flex-direction: column;
    row-gap: 48px;
    width: 750px;
    height: 500px;
    background-color: white;
    margin: 0 auto;
    border-radius: 10px;
    padding: 16px;
  }

  &__header {
    display: flex;
    justify-content: space-between;
  }

  &__fields {
    display: flex;
    flex-direction: column;
    row-gap: 16px;
  }

  &__field-label {
    font-size: 15px;
  }

  &__field-input {
    margin-top: 8px;
    width: 100%;
    display: block;
    border: none;
    border-bottom: 1px solid var(--color-blue);

    &:focus-visible {
      outline: none;
    }
  }

  &__buttons-wrapper {
    margin-top: auto;
    display: flex;
    column-gap: 12px;
  }

  &__button {
    font-family: 'Firasans-Medium', Roboto, sans-serif;
    font-weight: 500;
    line-height: 108%;
    font-size: 12px;
    background-color: var(--vt-c-white);
    border: 1px solid var(--color-gray);
    cursor: pointer;
    padding: 8px 10px;
    border-radius: 15px;

    &:hover {
      background-color: var(--color-gray);
    }

    &:active {
      background-color: var(--color-gray);
      opacity: 0.3;
    }
  }

  &__overlay {
    content: '';
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: var(--color-overlay);
    z-index: -1;
  }
}

</style>
