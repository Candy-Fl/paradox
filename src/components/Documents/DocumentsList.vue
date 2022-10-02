<template>
  <div class="documents-list">
    <draggable
      class="documents-list__draggable"
      :list="groupsList"
      group="list1"
    >
      <DocumentsGroup
        class="documents-list__group-list"
        v-for="(list, key) in groupsList"
        :key="`${list.title}-${key}`"
        :item="list"
        @deleteGroup="group => deleteGroup(group)"
        @editGroup="group => editGroup(group)"
      >
        <draggable
          class="documents-list__draggable"
          :list="list.children"
          group="list2"
        >
          <DocumentsItem
            class="documents-list__group-item"
            v-for="child in list.children"
            :item="child"
            @deleteItem="itemToDelete => deleteItemInGroup(itemToDelete, list)"
            @editItem="itemToEdit => editItemInGroup(itemToEdit, list)"
          />
        </draggable>
      </DocumentsGroup>
    </draggable>

    <draggable
      class="documents-list__item-list"
      :list="itemsList"
      group="list2"
    >
      <DocumentsItem
        v-for="item in itemsList"
        :item="item"
        @deleteItem="itemToDelete => deleteItem(itemToDelete)"
        @editItem="itemToEdit => editItem(itemToEdit)"
      />
    </draggable>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import type { DocumentsItemType } from "@/components/Documents/DocumentsItem.vue";
import DocumentsItem from "@/components/Documents/DocumentsItem.vue";
import { VueDraggableNext } from "vue-draggable-next";
import type { DocumentsGroupType } from "@/components/Documents/DocumentsGroup.vue";
import DocumentsGroup from "@/components/Documents/DocumentsGroup.vue";

export default defineComponent({
  name: "DocumentsList",
  props: {
    groupsList: {
      type: Array,
      default: () => [],
    },
    itemsList: {
      type: Array,
      default: () => [],
    },
  },
  components: {
    DocumentsItem,
    DocumentsGroup,
    draggable: VueDraggableNext,
  },
  methods: {
    deleteGroup (group: DocumentsGroupType) {
      this.$emit('deleteGroup', group);
    },
    deleteItem (item: DocumentsItemType) {
      this.$emit('deleteItem', item);
    },
    deleteItemInGroup (item: DocumentsItemType, list: DocumentsGroupType) {
      if (list.children) {
        const index = this.groupsList?.indexOf(list);
        (this.groupsList[index] as DocumentsGroupType).children = list.children.filter(i => i !== item);
      }
    },
    editItemInGroup (item: DocumentsItemType, list: DocumentsGroupType) {
        this.$emit('editDocumentInGroup', item, list);
    },
    editGroup(group: DocumentsGroupType) {
      this.$emit('editGroup', group);
    },
    editItem(item: DocumentsItemType) {
      this.$emit('editItem', item);
    },
  },
});
</script>

<style lang="scss">
.documents-list {
  margin-top: 19px;

  &__item-list {
    margin-top: 14px;
  }

  &__draggable {
    min-height: 2px;
    height: max-content;
  }
}
</style>
