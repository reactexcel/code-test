<template>
  <div class="hierarchical-checkbox">
    <div v-for="(node, index) in treeData" :key="index" class="node">
      <input
        type="checkbox"
        :checked="isSelected(node)"
        @change="toggleSelection(node)"
      />
      <span>{{ node.label }}</span>
      <HierarchicalCheckbox
        v-if="node.children && node.children.length && node.expanded"
        :tree-data="node.children"
        :selected-ids.sync="selectedIds"
        :style="{ 'margin-left': '25px' }" 
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType, ref, watch } from 'vue';

interface TreeNode {
  id: number;
  label: string;
  expanded?: boolean;
  children?: TreeNode[];
}

export default defineComponent({
  name: 'HierarchicalCheckbox',
  props: {
    treeData: {
      type: Array as PropType<TreeNode[]>,
      required: true,
    },
    selectedIds: {
      type: Array as PropType<number[]>,
      required: true,
    },
  },
  setup(props, { emit }) {
    const localSelectedIds = ref([...props.selectedIds]);
    const localStorageKey = 'selectedIds';

    const isSelected = (node: TreeNode) => {
      return localSelectedIds.value.includes(node.id);
    };

    const toggleSelection = (node: TreeNode) => {
      if (isSelected(node)) {
        deselectNode(node);
      } else {
        selectNode(node);
      }
      saveSelection();
      emit('update:selectedIds', localSelectedIds.value);
    };

    const selectNode = (node: TreeNode) => {
      localSelectedIds.value.push(node.id);
      node.expanded = true;
    };

    const deselectNode = (node: TreeNode) => {
      localSelectedIds.value = localSelectedIds.value.filter((id) => id !== node.id);
      if (node.children) {
        node.children.forEach(deselectNode);
      }
      node.expanded = false;
    };

    const saveSelection = () => {
      localStorage.setItem(localStorageKey, JSON.stringify(localSelectedIds.value));
    };

    const loadSelection = () => {
      const savedSelection = localStorage.getItem(localStorageKey);
      if (savedSelection) {
        const selectedIds: number[] = JSON.parse(savedSelection);
        selectedIds.forEach((id) => {
          const node = findNodeById(id, props.treeData);
          if (node) {
            selectNode(node);
          }
        });
      }
    };

    const findNodeById = (id: number, nodes: TreeNode[]): TreeNode | null => {
      for (const node of nodes) {
        if (node.id === id) {
          return node;
        }
        if (node.children) {
          const foundNode = findNodeById(id, node.children);
          if (foundNode) {
            return foundNode;
          }
        }
      }
      return null;
    };

    watch(
      () => props.treeData,
      () => {
        loadSelection();
      },
      { immediate: true }
    );

    watch(
      () => props.selectedIds,
      (newSelectedIds) => {
        localSelectedIds.value = [...newSelectedIds];
      }
    );

    return {
      isSelected,
      toggleSelection,
    };
  },
});
</script>

<style scoped>
.hierarchical-checkbox {
  text-align: center;
}
.node {
  margin-left: 20px;
  margin-bottom: 10px; 
}
.node > .node {
  margin-left: 0px;
  margin-bottom: 10px; 
}
</style> 

