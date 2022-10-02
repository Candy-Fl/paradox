<template>
  <div :class="`documents-button_${view}`">
    <button :class="`documents-button__button`" @click="click">
      <img
        :class="`documents-button__icon ${iconClass}`"
        :src="imageUrl"
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

export enum ButtonView {
  // Кнопка "избранное"
  ICON_FAVORITE = "icon-favorite",
  // Кнопка с текстом
  BUTTON = "button",
  // Кнопка "Удалить текст"
  ICON_DELETE = "icon-delete",
  //  Кнопка удалить документ
  ICON_DELETE_DOC = "icon-delete-doc",
  // Кнопка перемещения документа
  ICON_REPLACE = "icon-replace",
  // Кнопка редактировать документ
  ICON_EDIT = "icon-edit",
  // Кнопка раскрытия списка
  ICON_ARROW = 'icon-arrow',
}
export default defineComponent({
  name: "DocumentsButton",
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
    imageUrl () {
      const imageUrl = new URL(`../../assets/icons/${this.component}.svg`, import.meta.url).href;
      return imageUrl;
    },

    ButtonView () {
      return ButtonView;
    },

    component () {
      if (this.view === ButtonView.BUTTON) {
        return 'icon-plus'
      }
      return this.view;
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

  &_icon-favorite {
    .documents-button__button {
      padding: 9px 10px 8px 10px;
    }

    .documents-button__text {
      display: none;
    }
  }

  &_icon-delete,
  &_icon-replace,
  &_icon-delete-doc,
  &_icon-edit {
    .documents-button__button {
      border: none;
      padding: 0;
    }

    .documents-button__text {
      display: none;
    }
  }

  &_icon-arrow {
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
