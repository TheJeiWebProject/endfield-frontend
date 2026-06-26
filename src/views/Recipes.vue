<template>
  <div class="recipes-container">
    <div class="section-header">
      <h2>{{ i18n.t('recipes.title') }}</h2>
      <p>{{ i18n.t('recipes.description') }}</p>
    </div>

    <div class="search-with-filter">
      <input
        v-model="searchQuery"
        type="text"
        class="search-box"
        :placeholder="i18n.t('recipes.search.placeholder')"
      >
      <select
        v-model="selectedCategory"
        class="category-dropdown"
      >
        <option :value="null">
          {{ i18n.t('recipes.category.all') }}
        </option>
        <option
          v-for="category in displayCategories"
          :key="category.key"
          :value="category.key"
        >
          {{ category.label }}
        </option>
      </select>
    </div>

    <div
      v-if="filteredRecipes.length === 0"
      class="empty-state"
    >
      {{ i18n.t('recipes.empty') }}
    </div>

    <div
      v-else
      class="recipe-list"
    >
      <div
        v-for="recipe in filteredRecipes"
        :key="recipe.id"
        class="recipe-card"
      >
            <h4>{{ recipe.name }}</h4>

            <div class="recipe-flow">
              <div class="flow-section inputs">
                <span class="flow-label">{{ i18n.t('recipes.ingredients') }}</span>
                <div class="flow-items">
                  <div
                    v-for="(amount, itemId) in recipe.in"
                    :key="itemId"
                    class="flow-item"
                  >
                    <img
                      :src="dataStore.getIconUrl(getItemIcon(itemId))"
                      :alt="getItemName(itemId)"
                      loading="lazy"
                      :style="{ width: iconSize + 'px', height: iconSize + 'px' }"
                    >
                    <span>{{ getItemName(itemId) }} x{{ amount }}</span>
                  </div>
                </div>
              </div>

              <div class="flow-arrow">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M5 12h14M12 5l7 7-7 7"/>
                </svg>
              </div>

              <div class="flow-section outputs">
                <span class="flow-label">{{ i18n.t('recipes.products') }}</span>
                <div class="flow-items">
                  <div
                    v-for="(amount, itemId) in recipe.out"
                    :key="itemId"
                    class="flow-item output"
                  >
                    <img
                      :src="dataStore.getIconUrl(getItemIcon(itemId))"
                      :alt="getItemName(itemId)"
                      loading="lazy"
                      :style="{ width: iconSize + 'px', height: iconSize + 'px' }"
                    >
                    <span>{{ getItemName(itemId) }} x{{ amount }}</span>
                  </div>
                </div>
              </div>
            </div>

            <div class="recipe-info">
              <span class="recipe-time">
                  {{ recipe.time }}{{ i18n.t('recipes.time') }}
              </span>
            </div>
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
const iconSize = ref(32)

const displayCategories = computed(() => {
  const categoryMap = {
    'material': i18n.t('recipes.category.material'),
    'product': i18n.t('recipes.category.product'),
    'gen-power': i18n.t('recipes.category.gen-power'),
    'settlement': i18n.t('recipes.category.settlement'),
    'machine': i18n.t('recipes.category.machine'),
    'belt-and-pipe': i18n.t('recipes.category.belt-and-pipe'),
    'logistics': i18n.t('recipes.category.logistics'),
    'other': i18n.t('recipes.category.other')
  }
  
  const cats = new Set()
  dataStore.recipes.forEach(recipe => {
    if (recipe.category) {
      cats.add(recipe.category)
    }
  })
  
  return Array.from(cats).sort().map(cat => ({
    key: cat,
    label: categoryMap[cat] || cat
  }))
})

const filteredRecipes = computed(() => {
  let recipes = dataStore.recipes

  if (selectedCategory.value) {
    recipes = recipes.filter(r => r.category === selectedCategory.value)
  }

  if (searchQuery.value.trim()) {
    const query = searchQuery.value.toLowerCase()
    recipes = recipes.filter(recipe =>
      recipe.name.toLowerCase().includes(query)
    )
  }

  return recipes
})



function getItemName(itemId) {
  const item = dataStore.itemsMap[itemId]
  return item?.name || itemId
}

function getItemIcon(itemId) {
  const item = dataStore.itemsMap[itemId]
  return item?.icon || ''
}
</script>

<style scoped>
.recipes-container {
  max-width: 1200px;
  margin: 0 auto;
}

.search-with-filter {
  display: flex;
  gap: 0.75rem;
  margin-bottom: 1rem;
  flex-wrap: wrap;
}

.search-with-filter .search-box {
  flex: 1;
  min-width: 200px;
  margin-bottom: 0;
}

.category-dropdown {
  padding: 0.875rem 1.25rem;
  background: var(--bg-card);
  border: 2px solid rgba(0, 212, 255, 0.2);
  clip-path: polygon(0 0, calc(100% - 8px) 0, 100% 8px, 100% 100%, 8px 100%, 0 calc(100% - 8px));
  color: #f0f0f0;
  font-size: 0.95rem;
  cursor: pointer;
  transition: all 0.3s;
  min-width: 140px;
}

.category-dropdown:hover {
  border-color: rgba(0, 212, 255, 0.5);
}

.category-dropdown:focus {
  outline: none;
  border-color: rgba(0, 212, 255, 0.5);
  box-shadow: 0 0 20px rgba(0, 212, 255, 0.2);
}

.category-dropdown option {
  background: var(--bg-card);
  color: #f0f0f0;
  padding: 0.5rem;
}

.recipe-list {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
}

@media (max-width: 768px) {
  .recipe-list {
    grid-template-columns: 1fr;
  }
}

.recipe-card {
  background: var(--bg-card);
  border: 1px solid rgba(0, 212, 255, 0.15);
  border-radius: 10px;
  padding: 1rem;
  transition: all 0.3s;
  min-height: auto;
  height: auto;
}

.recipe-card:hover {
  border-color: rgba(0, 212, 255, 0.3);
  background: rgba(0, 212, 255, 0.05);
}

.recipe-card h4 {
  color: #00D4FF;
  margin: 0 0 1rem;
  font-size: 1rem;
  font-weight: 600;
  word-break: break-word;
  line-height: 1.3;
}

.recipe-flow {
  display: flex;
  align-items: center;
  gap: 1rem;
  flex-wrap: wrap;
}

.flow-section {
  flex: 1;
  min-width: 120px;
}

.flow-label {
  display: block;
  font-size: 0.75rem;
  color: #888;
  margin-bottom: 0.5rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.flow-items {
  display: flex;
  flex-wrap: wrap;
  gap: 0.625rem;
}

.flow-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.25rem;
  padding: 0.5rem;
  background: var(--bg-card);
  clip-path: polygon(0 0, calc(100% - 6px) 0, 100% 6px, 100% 100%, 6px 100%, 0 calc(100% - 6px));
  transition: all 0.2s;
}

.flow-item:hover {
  background: rgba(0, 212, 255, 0.1);
}

.flow-item.output {
  background: rgba(0, 212, 255, 0.08);
}

.flow-item img {
  filter: drop-shadow(0 2px 8px rgba(255, 255, 255, 0.2));
}

.flow-item span {
  font-size: 0.8rem;
  color: #ccc;
  text-align: center;
  max-width: 80px;
  word-break: break-word;
}

.flow-arrow {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #00D4FF;
  flex-shrink: 0;
}

.flow-arrow svg {
  width: 24px;
  height: 24px;
}

.recipe-info {
  display: flex;
  gap: 1.5rem;
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid rgba(255, 255, 255, 0.05);
  font-size: 0.85rem;
  color: #888;
}

.recipe-time {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

/* 移动端适配 */
@media (max-width: 768px) {
  .recipes-container {
    padding: 0 0.5rem;
  }

  .page-title {
    font-size: 1.3rem;
    margin-bottom: 1rem;
  }

  .settings-panel {
    flex-direction: column;
    gap: 0.5rem;
    padding: 0.8rem;
    margin-bottom: 1rem;
  }

  .setting-item {
    width: 100%;
    justify-content: space-between;
  }

  .setting-item label {
    font-size: 0.9rem;
  }

  .icon-size-slider {
    flex: 1;
    max-width: 150px;
  }

  .category-select {
    font-size: 16px;
    padding: 0.5rem;
  }

  .search-box {
    font-size: 16px;
    padding: 0.8rem 1rem;
    margin-bottom: 1rem;
  }

  .filter-tabs {
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
    gap: 0.4rem;
    margin-bottom: 1rem;
  }

  .filter-tab {
    padding: 0.4rem 0.8rem;
    font-size: 0.85rem;
    white-space: nowrap;
  }

  .recipe-group {
    margin-bottom: 1rem;
  }

  .group-header {
    padding: 0.6rem;
  }

  .group-name {
    font-size: 1rem;
  }

  .group-content {
    padding: 0.8rem;
    gap: 0.8rem;
  }

  .recipe-card {
    padding: 0.8rem;
  }

  .recipe-card h3 {
    font-size: 0.95rem;
  }

  .recipe-ingredients {
    flex-direction: column;
    align-items: flex-start;
    gap: 0.5rem;
  }

  .recipe-ingredient {
    width: 100%;
    padding: 0.4rem 0.6rem;
  }

  .recipe-ingredient img {
    width: 20px;
    height: 20px;
  }

  .recipe-info {
    flex-direction: column;
    gap: 0.5rem;
    margin-top: 0.5rem;
    padding-top: 0.5rem;
  }
}

@media (max-width: 480px) {
  .recipe-card {
    padding: 0.6rem;
  }

  .recipe-ingredient {
    padding: 0.3rem 0.5rem;
    font-size: 0.85rem;
  }
}
</style>