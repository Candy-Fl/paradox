<template>
  <div class="document-page">
    <div class="document-page__head">
      <h1 class="document-page__title">Документы</h1>
      <div class="document-page__buttons-container">
        <DocumentsButton
          class="document-page__button"
          :view="ButtonView.ICON_FAVORITE"
        />
        <DocumentsButton
          class="documents-page__button"
          @click="openModal(ModalView.ADD_GROUP)"
        >
          Новый тип
        </DocumentsButton>
        <DocumentsButton
          class="documents-page__button"
          @click="openModal(ModalView.ADD_DOCUMENT)"
        >
          Новый документ
        </DocumentsButton>
      </div>
    </div>

    <DocumentSearch
      class="document-page__search"
      @search="search => searchItems(search)"
    />

    <DocumentsList
      class="document-page__list"
      :groupsList="groups"
      :itemsList="documents"
      @deleteGroup="group => deleteGroup(group)"
      @deleteItem="item => deleteItem(item)"
      @editGroup ="group => editGroup(group)"
      @editItem="item => editItem(item)"
      @editDocumentInGroup="(document, list) => editDocumentInGroup(document,list)"
    />

    <transition name="slide-fade">
      <DocumentsModal
        v-if="isModalOpen"
        :view="modalType"
        @close="isModalOpen = false"
        @addNewGroup = "group => addGroup(group)"
        @addNewItem = "item => addItem(item)"
        @editGroup = "group => changeGroup(groupToEdit, group)"
        @editDocument = "document => changeDocument(documentToEdit, document)"
        :group="groupToEdit"
        :document="documentToEdit"
      />
    </transition>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import DocumentsButton, { ButtonView } from "@/components/Documents/DocumentsButton.vue";
import DocumentSearch from "@/components/Documents/DocumentSearch.vue";
import DocumentsList from "@/components/Documents/DocumentsList.vue";
import DocumentsModal, { ModalView } from "@/components/Documents/DocumentsModal.vue";
import type { DocumentsItemType } from "@/components/Documents/DocumentsItem.vue";
import type { DocumentsGroupType } from "@/components/Documents/DocumentsGroup.vue";

export default defineComponent({
  name: "App",
  components: {
    DocumentsButton,
    DocumentSearch,
    DocumentsList,
    DocumentsModal,
  },
  data: () => {
    return {
      helloText: "Hello world",
      show: false,
      isModalOpen: false,
      modalType: ModalView.ADD_DOCUMENT,
      groupToEdit: {} as DocumentsGroupType,
      documentToEdit: {} as DocumentsItemType,
      searchText: '',
      groups: [] as Array<DocumentsGroupType>,
      documents: [] as Array<DocumentsItemType>,
      groupsList: [
        {
          title: 'Обязательные для всех',
          description: 'Документы, обязательные для всех сотрудников без исключения',
          children: [
            {
              title: 'Паспорт',
              description: 'Для всех',
            },
            {
              title: 'ИНН',
              description: 'Для всех',
            }
          ],
        },
        {
          title: 'Обязательные для трудоустройства',
          description: 'Документы, без которых невозможно трудоустройство человека на какую бы то ни было должность в компании вне зависимости от гражданства',
          children: [],
        },
        {
          title: 'Специальные',
          description: '',
          children: [],
        },
      ] as Array<DocumentsGroupType>,
      itemsList: [
        {
          title: 'Тестовое задание кандидата',
          description: 'Россия, Белоруссия, Украина, администратор филиала, повар-сушист, повар-пиццмейкер, повар горячего цеха',
        },
        {
          title: 'Трудовой договор',
          description: '',
        },
        {
          title: 'Мед. книжка',
          description: '',
        },
      ],
    };
  },
  methods: {
    openModal (type: ModalView) {
      this.modalType = type;
      this.isModalOpen = true;
    },
    addItem (item: DocumentsItemType) {
      this.isModalOpen = false;
      this.itemsList.push(item);
    },
    addGroup (group: DocumentsGroupType) {
      this.isModalOpen = false;
      this.groupsList.push(group);
    },
    deleteGroup (group: DocumentsGroupType) {
      this.groups = this.groups.filter(list => list !==group);
    },
    deleteItem (itemDelete: DocumentsItemType) {
      this.documents = this.documents.filter(item => item !==itemDelete);
    },
    editGroup(group: DocumentsGroupType) {
      this.groupToEdit = group;
      this.modalType = ModalView.EDIT_GROUP;
      this.isModalOpen = true;
    },
    editItem(document: DocumentsItemType) {
      this.documentToEdit = document;
      this.modalType = ModalView.EDIT_DOCUMENT;
      this.isModalOpen = true;
    },
    editDocumentInGroup (item: DocumentsItemType, list: DocumentsGroupType) {
      this.documentToEdit = item;
      this.groupToEdit = list;
      this.modalType = ModalView.EDIT_ITEM_IN_GROUP;
      this.isModalOpen = true;
    },
    changeGroup (oldVal: DocumentsGroupType, newVal: DocumentsGroupType) {
      this.isModalOpen = false;
      const index = this.groupsList.indexOf(oldVal);
      return this.groupsList[index] = newVal;
    },
    changeDocument (oldVal: DocumentsItemType, newVal: DocumentsItemType) {
      this.isModalOpen = false;
      const index = this.itemsList.indexOf(oldVal);
      return this.itemsList[index] = newVal;
    },
    findSearchInChildren (group: DocumentsGroupType, search: string) {
      let hasInChild = false;
      if (group.children) {
        group.children.forEach(child => {
          if (child.title.toLowerCase().includes(search)) {
            hasInChild = true;
          }
        })
      }
      return hasInChild;
    },
    searchItems (searchText: string) {
      this.searchText = searchText;
      this.documents = this.itemsList.filter(group => {
        return group.title.toLowerCase().includes(this.searchText) || group.description.toLowerCase().includes(this.searchText)
      });
      this.groups = this.groupsList.filter(group => {
        return group.title.toLowerCase().includes(this.searchText) ||
          group.description.toLowerCase().includes(this.searchText) ||
          this.findSearchInChildren(group, this.searchText);
      })
    },
  },
  mounted () {
    this.documents = this.itemsList;
    this.groups = this.groupsList;
  },
  computed: {
    ButtonView () {
      return ButtonView;
    },
    ModalView () {
      return ModalView;
    },
  },
});
</script>
<style lang="scss">
.document-page {
  &__head {
    display: flex;
    justify-content: space-between;
  }

  &__title {
    font-size: 22px;
  }

  &__buttons-container {
    display: flex;
    column-gap: 10px;
    margin-bottom: 23px;
  }

  .slide-fade-enter-active {
    animation: slide-fade .2s;
  }

  .slide-fade-leave-active {
    animation: slide-fade .2s reverse;
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
