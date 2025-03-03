<script setup lang="ts">
import { ref } from 'vue';
import SelectableList from './components/SelectableList.vue';

// Example data
const items = ref([
  { id: 1, name: 'Item 1', description: 'Description for item 1' },
  { id: 2, name: 'Item 2', description: 'Description for item 2' },
  { id: 3, name: 'Item 3', description: 'Description for item 3' },
  { id: 4, name: 'Item 4', description: 'Description for item 4' },
  { id: 5, name: 'Item 5', description: 'Description for item 5' },
  { id: 6, name: 'Item 6', description: 'Description for item 6' },
  { id: 7, name: 'Item 7', description: 'Description for item 7' },
  { id: 8, name: 'Item 8', description: 'Description for item 8' },
]);

const selectionInfo = ref({
  selectedIds: [],
  selectedItems: [],
  count: 0,
  allSelected: false
});

const handleSelectionChange = (selection) => {
  selectionInfo.value = selection;
};
</script>

<template>
  <div class="min-h-screen bg-gray-100 py-12 px-4 sm:px-6 lg:px-8">
    <div class="max-w-3xl mx-auto">
      <div class="text-center mb-8">
        <h1 class="text-3xl font-extrabold text-gray-900 mb-2">Selectable List Demo</h1>
        <p class="text-lg text-gray-600">A reusable component with multiple selection modes</p>
      </div>
      
      <div class="bg-white p-6 rounded-lg shadow-md mb-8">
        <h2 class="text-lg font-medium text-gray-900 mb-3">Instructions</h2>
        <ul class="space-y-2 text-gray-600 mb-4">
          <li class="flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-blue-500 mr-2" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
            </svg>
            Click items to select them individually
          </li>
          <li class="flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-blue-500 mr-2" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
            </svg>
            Hold Shift and click to select a range of items
          </li>
          <li class="flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-blue-500 mr-2" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
            </svg>
            Use the checkbox in the header to select/deselect all items
          </li>
        </ul>
      </div>
      
      <SelectableList 
        :items="items" 
        idKey="id" 
        displayKey="name"
        @selection-change="handleSelectionChange"
      />
      
      <div v-if="selectionInfo.count > 0" class="mt-8 bg-blue-50 border border-blue-200 rounded-lg p-6">
        <h2 class="text-lg font-medium text-blue-900 mb-4">Selection Information</h2>
        <div class="space-y-2">
          <p class="text-blue-800">
            <span class="font-medium">Selected count:</span> {{ selectionInfo.count }}
          </p>
          <p class="text-blue-800">
            <span class="font-medium">All selected:</span> {{ selectionInfo.allSelected ? 'Yes' : 'No' }}
          </p>
          <div class="text-blue-800">
            <span class="font-medium">Selected IDs:</span> 
            <div class="mt-2 flex flex-wrap gap-2">
              <span 
                v-for="id in selectionInfo.selectedIds" 
                :key="id"
                class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-blue-100 text-blue-800"
              >
                {{ id }}
              </span>
            </div>
          </div>
        </div>
      </div>
      
      <!-- Debug Box -->
      <div class="mt-8 bg-gray-800 text-white rounded-lg p-6 overflow-auto">
        <div class="flex items-center justify-between mb-4">
          <h2 class="text-lg font-medium text-white">Debug Information</h2>
          <div class="text-xs px-2 py-1 bg-gray-700 rounded">selectionInfo object</div>
        </div>
        <pre class="text-xs text-green-400 font-mono overflow-x-auto">{{ JSON.stringify(selectionInfo, null, 2) }}</pre>
      </div>
    </div>
  </div>
</template>