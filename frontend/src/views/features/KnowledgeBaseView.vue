<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { qaApi } from '@/services/api'
import type { KnowledgeBaseEntry } from '@/types'

const entries = ref<KnowledgeBaseEntry[]>([])
const loading = ref(true)
const searchQuery = ref('')

onMounted(async () => {
  try {
    entries.value = await qaApi.getKnowledgeBase()
  } finally {
    loading.value = false
  }
})

const filteredEntries = computed(() => {
  if (!searchQuery.value) return entries.value
  const query = searchQuery.value.toLowerCase()
  return entries.value.filter(e =>
    e.title.toLowerCase().includes(query) ||
    e.content.toLowerCase().includes(query) ||
    e.tags.some(t => t.toLowerCase().includes(query))
  )
})

import { computed } from 'vue'
</script>

<template>
  <div class="knowledge-base">
    <h2>知识库</h2>

    <el-input
      v-model="searchQuery"
      placeholder="搜索知识库..."
      :prefix-icon="'Search'"
      clearable
      class="search-input"
    />

    <el-card v-loading="loading" class="entries-card">
      <el-empty v-if="filteredEntries.length === 0" description="暂无内容" />

      <el-collapse v-else>
        <el-collapse-item
          v-for="entry in filteredEntries"
          :key="entry.id"
          :title="entry.title"
        >
          <p>{{ entry.content }}</p>
          <el-tag
            v-for="tag in entry.tags"
            :key="tag"
            size="small"
            class="tag"
          >
            {{ tag }}
          </el-tag>
        </el-collapse-item>
      </el-collapse>
    </el-card>
  </div>
</template>

<style scoped>
.knowledge-base {
  padding: 20px;
}

.search-input {
  margin-bottom: 20px;
  max-width: 400px;
}

.tag {
  margin-right: 8px;
  margin-top: 8px;
}
</style>
