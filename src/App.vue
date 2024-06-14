<template>
  <h3>Hierarchical Checkbox</h3>
  <div id="app">
    <HierarchicalCheckbox :tree-data="treeData" v-model:selected-ids="selectedIds" />
  </div>
</template>

<script>
import { ref } from 'vue';
import HierarchicalCheckbox from './components/HierarchicalCheckbox.vue';

export default {
  name: 'App',
  components: {
    HierarchicalCheckbox,
  },
  setup() {
    const treeData = ref([
      {
        id: 1,
        label: 'Parent 1',
      },
      {
        id: 2,
        label: 'Parent 2',
      },
      {
        id: 3,
        label: 'Parent 3',
      },
      {
        id: 4,
        label: 'Parent 4',
        children: [
          {
            id: 5,
            label: 'Child 1',
          },
          {
            id: 6,
            label: 'Child 2',
          },
          {
            id: 10,
            label: 'Child 3',
            children: [
              {
                id: 7,
                label: 'Sub Child 1',
              },
              {
                id: 8,
                label: 'Sub Child 2',
              },
              {
                id: 9,
                label: 'Sub Child 3',
              },
            ],
          }, 
        ],
      },
     
      {
        id: 11,
        label: 'Parent 5',
      },
      {
        id: 12,
        label: 'Parent 6',
        children: [
          {
            id: 13,
            label: 'Child 1',
            children: [
              {
                id: 14,
                label: 'Sub Child 1',
              },
              {
                id: 15,
                label: 'Sub Child 2',
              },
            ],
          },
        ],
      },
    ]);

    const selectedIds = ref([]);

    return {
      treeData,
      selectedIds,
    };
  },
};
</script>

<style>
h3{
  text-align: center;
  font-size: 24px;
}
</style>


