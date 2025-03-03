<script setup lang="ts">
import { ref, computed, watch } from "vue";

interface Props {
  items: any[];
  idKey?: string;
  displayKey?: string;
}

const props = withDefaults(defineProps<Props>(), {
  idKey: "id",
  displayKey: "name",
});

const emit = defineEmits(["selectionChange"]);

const selectedItems = ref<Set<any>>(new Set());
const lastSelectedIndex = ref<number | null>(null);
const allSelected = ref(false);

const selectedCount = computed(() => selectedItems.value.size);
const isAllSelected = computed(
  () => selectedCount.value === props.items.length
);
const isPartiallySelected = computed(
  () => selectedCount.value > 0 && !isAllSelected.value
);

// Handle selection of an item
const handleSelection = (item: any, index: number, event: MouseEvent) => {
  event.stopPropagation();
  const itemId = item[props.idKey];

  // Handle shift key for range selection
  if (event.shiftKey && lastSelectedIndex.value !== null) {
    const start = Math.min(lastSelectedIndex.value, index);
    const end = Math.max(lastSelectedIndex.value, index);

    // Select all items in the range
    for (let i = start; i <= end; i++) {
      selectedItems.value.add(props.items[i][props.idKey]);
    }
  } else {
    // Toggle single item
    if (selectedItems.value.has(itemId)) {
      selectedItems.value.delete(itemId);
    } else {
      selectedItems.value.add(itemId);
    }
    // Update last selected index
    lastSelectedIndex.value = index;
  }

  updateSelectionState();
};

// Toggle all items
const toggleAll = () => {
  if (isAllSelected.value) {
    selectedItems.value.clear();
  } else {
    props.items.forEach((item) => {
      selectedItems.value.add(item[props.idKey]);
    });
  }

  updateSelectionState();
};

// Check if an item is selected
const isSelected = (item: any) => {
  return selectedItems.value.has(item[props.idKey]);
};

// Update selection state and emit changes
const updateSelectionState = () => {
  allSelected.value = isAllSelected.value;

  // Convert Set to Array for the emit
  const selectedArray = Array.from(selectedItems.value);
  const selectedObjects = props.items.filter((item) =>
    selectedItems.value.has(item[props.idKey])
  );

  emit("selectionChange", {
    selectedIds: selectedArray,
    selectedItems: selectedObjects,
    count: selectedCount.value,
    allSelected: isAllSelected.value,
  });
};

// Reset selection
const resetSelection = () => {
  selectedItems.value.clear();
  lastSelectedIndex.value = null;
  updateSelectionState();
};

// Watch for changes in items to reset selection if needed
watch(
  () => props.items,
  () => {
    // Optional: reset selection when items change
    // resetSelection();
  },
  { deep: true }
);

// Expose methods to parent components
defineExpose({
  resetSelection,
  selectAll: () => {
    props.items.forEach((item) => {
      selectedItems.value.add(item[props.idKey]);
    });
    updateSelectionState();
  },
  deselectAll: () => {
    selectedItems.value.clear();
    updateSelectionState();
  },
  getSelectedItems: () => {
    return props.items.filter((item) =>
      selectedItems.value.has(item[props.idKey])
    );
  },
});
</script>

<template>
  <div
    class="overflow-hidden w-full bg-white rounded-lg border border-gray-200 shadow-md"
  >
    <div class="px-4 py-3 bg-gray-50 border-b border-gray-200">
      <label class="flex items-center space-x-3 cursor-pointer">
        <input
          type="checkbox"
          :checked="isAllSelected"
          :indeterminate="isPartiallySelected"
          @click="toggleAll"
          class="w-4 h-4 text-blue-600 rounded focus:ring-blue-500"
        />
        <span
          v-if="selectedCount > 0"
          class="text-sm font-medium text-gray-700"
        >
          {{ selectedCount }} selected
        </span>
        <span v-else class="text-sm font-medium text-gray-700">
          Select All
        </span>
      </label>
    </div>

    <ul class="divide-y divide-gray-200">
      <li
        v-for="(item, index) in items"
        :key="item[idKey]"
        :class="[
          'px-4 py-3 transition-colors duration-150 hover:bg-gray-50',
          isSelected(item) ? 'bg-blue-50' : '',
        ]"
      >
        <label class="flex items-center space-x-3 w-full cursor-pointer">
          <input
            type="checkbox"
            :checked="isSelected(item)"
            @click="(event) => handleSelection(item, index, event)"
            class="w-4 h-4 text-blue-600 rounded focus:ring-blue-500"
          />
          <span class="text-sm font-medium text-gray-800">{{
            item[displayKey]
          }}</span>
        </label>
      </li>
    </ul>
  </div>
</template>
