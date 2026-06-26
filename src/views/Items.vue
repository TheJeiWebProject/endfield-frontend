<template>
  <div class="items-container">
    <div class="section-header">
      <h2>{{ i18n.t('items.title') }}</h2>
      <p>{{ i18n.t('items.description') }}</p>
    </div>

    <input
      v-model="searchQuery"
      type="text"
      class="search-box"
      :placeholder="i18n.t('items.search.placeholder')"
    >

    <div class="filter-tabs">
      <button
        class="filter-tab"
        :class="{ active: selectedCategory === null }"
        @click="selectedCategory = null"
      >
        {{ i18n.t('items.category.all') }}
      </button>
      <button
        v-for="category in dataStore.categories"
        :key="category.id"
        class="filter-tab"
        :class="{ active: selectedCategory === category.id }"
        @click="selectedCategory = category.id"
      >
        {{ getCategoryButtonName(category.id) }}
      </button>
    </div>

    <div
      v-if="filteredItems.length === 0"
      class="empty-state"
    >
      {{ i18n.t('items.empty') }}
    </div>

    <div
      v-else
      class="items-grid"
    >
      <div
        v-for="item in filteredItems"
        :key="item.id"
        class="item-card"
      >
        <img
          :src="dataStore.getIconUrl(item.icon)"
          :alt="getItemName(item.id)"
          loading="lazy"
        >
        <h4>{{ getItemName(item.id) }}</h4>
        <span class="category badge badge-primary">
          {{ getCategoryName(item.category) }}
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useDataStore } from '../stores/data'
import { useI18nStore } from '../stores/i18n'

const dataStore = useDataStore()
const i18n = useI18nStore()

const searchQuery = ref('')
const selectedCategory = ref(null)

const filteredItems = computed(() => {
  let items = dataStore.items

  if (selectedCategory.value) {
    items = items.filter(item => item.category === selectedCategory.value)
  }

  if (searchQuery.value.trim()) {
    const query = searchQuery.value.toLowerCase()
    items = items.filter(item =>
      item.name.toLowerCase().includes(query) ||
      item.id.toLowerCase().includes(query)
    )
  }

  return items
})

function getCategoryName(categoryId) {
  const categoryMap = {
    'material': i18n.t('items.category.material'),
    'product': i18n.t('items.category.product'),
    'gen-power': i18n.t('items.category.gen-power'),
    'settlement': i18n.t('items.category.settlement'),
    'machine': i18n.t('items.category.machine'),
    'belt-and-pipe': i18n.t('items.category.belt-and-pipe'),
    'logistics': i18n.t('items.category.logistics'),
    'other': i18n.t('items.category.other')
  }
  return categoryMap[categoryId] || categoryId
}

function getCategoryButtonName(categoryId) {
  return getCategoryName(categoryId)
}

function getItemName(itemId) {
  const itemKey = `item.${itemId}`
  const translatedName = i18n.t(itemKey)
  // 如果没有翻译，返回原始名称
  return translatedName === itemKey ? dataStore.itemsMap[itemId]?.name || itemId : translatedName
}
</script>

<style scoped>
.items-container {
  max-width: 1200px;
  margin: 0 auto;
}

.search-box {
  margin-bottom: 1rem;
}

.items-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 1.25rem;
}

.item-card {
  background: var(--bg-card);
  border: 1px solid rgba(0, 212, 255, 0.3);
  clip-path: polygon(0 0, calc(100% - 10px) 0, 100% 10px, 100% 100%, 10px 100%, 0 calc(100% - 10px));
  padding: 1.25rem;
  text-align: center;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.75rem;
}

.item-card:hover {
  border-color: rgba(0, 212, 255, 0.5);
  transform: translateY(-8px) scale(1.02);
  box-shadow: 0 20px 40px rgba(0, 212, 255, 0.15);
}

.item-card img {
  width: 56px;
  height: 56px;
  filter: drop-shadow(0 4px 12px rgba(0, 212, 255, 0.3));
  transition: all 0.3s;
}

.item-card:hover img {
  transform: scale(1.1);
  filter: drop-shadow(0 6px 16px rgba(0, 212, 255, 0.4));
}

.item-card h4 {
  font-size: 0.95rem;
  color: #f5f5f5;
  font-weight: 600;
  word-break: break-word;
  line-height: 1.3;
  text-align: center;
  margin: 0;
  min-height: 2.6rem;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.item-card .category {
  font-size: 0.8rem;
}

@media (max-width: 768px) {
  .items-grid {
    grid-template-columns: repeat(auto-fill, minmax(110px, 1fr));
    gap: 1rem;
  }

  .item-card {
    padding: 1rem;
  }

  .item-card img {
    width: 44px;
    height: 44px;
  }

  .item-card h4 {
    font-size: 0.85rem;
  }
}

@media (max-width: 480px) {
  .items-grid {
    grid-template-columns: repeat(auto-fill, minmax(90px, 1fr));
    gap: 0.75rem;
  }

  .item-card {
    padding: 0.8rem 0.5rem;
  }

  .item-card img {
    width: 36px;
    height: 36px;
  }

  .item-card h4 {
    font-size: 0.8rem;
  }
}
</style>