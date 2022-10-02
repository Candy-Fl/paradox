<template>
  <div :class="`documents-button_${view}`">
    <button :class="`documents-button__button`" @click="click">
      <component
        :class="`documents-button__icon ${iconClass}`"
        :is="component"
      />
      <span class="documents-button__text">
        <slot/>
      </span>
    </button>
  </div>
</template>

<script lang="ts">
/**
 * Компонент кнопки на странице документов
 *
 * @module DocumentsButton
 *
 * @params {String} title - текст внутри кнопки
 * @params {String} view - вариант отображения кнопки
 */
import { defineComponent } from "vue";
import IconPlus from "@/assets/icons/icon-plus.svg?component"
import IconFavorite from "@/assets/icons/icon-favorite.svg?component"
import IconDelete from "@/assets/icons/icon-delete.svg?component"
import IconReplace from "@/assets/icons/icon-replace.svg?component"
import IconEdit from "@/assets/icons/icon-edit.svg?component"
import IconDeleteDoc from "@/assets/icons/icon-delete-doc.svg?component"
import IconArrow from "@/assets/icons/icon-arrow.svg?component"

export enum ButtonView {
  // Кнопка "избранное"
  ICON_FAVORITE = "iconFavorite",
  // Кнопка с текстом
  BUTTON = "button",
  // Кнопка "Удалить текст"
  ICON_DELETE = "iconDelete",
  //  Кнопка удалить документ
  ICON_DELETE_DOC = "iconDeleteDoc",
  // Кнопка перемещения документа
  ICON_REPLACE = "iconReplace",
  // Кнопка редактировать документ
  ICON_EDIT = "iconEdit",
  // Кнопка раскрытия списка
  ICON_ARROW = 'iconArrow',
}
export default defineComponent({
  name: "DocumentsButton",
  components: {
    IconPlus,
    IconFavorite,
    IconDelete,
    IconReplace,
    IconEdit,
    IconDeleteDoc,
    IconArrow,
  },
  data: () => {
    return {
      isGroupOpened: false,
    }
  },
  props: {
    title: {
      type: String,
      default: "",
    },
    view: {
      type: String,
      default: ButtonView.BUTTON,
    },
  },
  computed: {
    iconClass () {
      return this.isGroupOpened ? '_switch-down': '';
    },

    ButtonView () {
      return ButtonView;
    },

    component () {
      switch (this.view) {
        case ButtonView.BUTTON:
          return 'IconPlus';
        case ButtonView.ICON_FAVORITE:
          return 'IconFavorite';
        case ButtonView.ICON_DELETE:
          return 'IconDelete';
        case ButtonView.ICON_DELETE_DOC:
          return 'IconDeleteDoc';
        case ButtonView.ICON_EDIT:
          return 'IconEdit';
        case ButtonView.ICON_REPLACE:
          return 'IconReplace';
        case ButtonView.ICON_ARROW:
          return 'IconArrow';
      }
    }
  },
  methods: {
    click () {
      this.isGroupOpened = !this.isGroupOpened;
      this.$emit('pressed');
    },
  }

});
</script>

<style lang="scss">
.documents-button {
  &__button {
    display: flex;
    align-items: center;
    column-gap: 15px;
    padding: 8px 20px 8px 10px;
    border-radius: 50px;
    outline: none;
    background-color: var(--vt-c-white);
    border: 1px solid var(--color-gray);
    cursor: pointer;
  }

  &__text {
    font-family: 'Firasans-Medium', Roboto, sans-serif;
    font-weight: 500;
    line-height: 108%;
    font-size: 12px;
  }

  &_iconFavorite {
    .documents-button__button {
      padding: 9px 10px 8px 10px;
    }

    .documents-button__text {
      display: none;
    }
  }

  &_iconDelete,
  &_iconReplace,
  &_iconDeleteDoc,
  &_iconEdit {
    .documents-button__button {
      border: none;
      padding: 0;
    }

    .documents-button__text {
      display: none;
    }
  }

  &_iconArrow {
    .documents-button__button {
      padding: 8px 7px;
    }

    .documents-button__text {
      display: none;
    }

    .documents-button__icon {
      transition: all 0.1s linear;

      &._switch-down {
        transform: rotate(180deg);
      }
    }
  }
}
</style>
