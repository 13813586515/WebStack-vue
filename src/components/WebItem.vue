<template>
  <div>
    <h4 class="text-gray">
      <i class="linecons-tag" :id="transName(item)"></i>{{transName(item)}}
    </h4>
    
    <!-- 卡片视图 -->
    <div v-if="viewMode === 'grid'" class="row">
      <div class="col-sm-3" v-for="(web, idx) in filteredWeb" :key="idx">
        <div class="xe-widget xe-conversations box2 label-info" title=""
          @click="openweb(web.url)"
          data-toggle="tooltip" 
          data-placement="bottom" 
          :data-original-title="web.url">

          <div class="xe-comment-entry">
            <a class="xe-user-img">
              <img :src="web.logo" class="lozad img-circle" width="40">
            </a>
            <div class="xe-comment">
              <a href="#" class="xe-user-name overflowClip_1">
                <strong v-html="highlightText(web.title)"></strong>
              </a>
              <p class="overflowClip_2" v-html="highlightText(web.desc)"></p>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- 列表视图 -->
    <div v-else class="list-view">
      <div v-for="(web, idx) in filteredWeb" :key="idx" class="list-item" @click="openweb(web.url)">
        <div class="list-item-content">
          <img :src="web.logo" class="list-item-logo" width="32" height="32">
          <div class="list-item-info">
            <div class="list-item-title" v-html="highlightText(web.title)"></div>
            <div class="list-item-desc" v-html="highlightText(web.desc)"></div>
          </div>
          <div class="list-item-url" v-html="highlightText(web.url)"></div>
        </div>
      </div>
    </div>
    
    <br />
  </div>
</template>

<script>
export default {
  name: 'WebItem',
  props: {
    item: Object,
    transName: Function,
    viewMode: {
      type: String,
      default: 'grid'
    },
    searchQuery: {
      type: String,
      default: ''
    }
  },
  computed: {
    filteredWeb() {
      if (!this.searchQuery || this.searchQuery.trim() === '') {
        return this.item.web;
      }
      const query = this.searchQuery.toLowerCase().trim();
      return this.item.web.filter(web =>
        web.title.toLowerCase().includes(query) ||
        web.desc.toLowerCase().includes(query) ||
        web.url.toLowerCase().includes(query)
      );
    }
  },
  methods: {
    openweb(url) {
      window.open(url, '_blank');
    },
    highlightText(text) {
      if (!this.searchQuery || this.searchQuery.trim() === '') {
        return text;
      }
      const query = this.searchQuery.trim();
      const regex = new RegExp(`(${this.escapeRegExp(query)})`, 'gi');
      return text.replace(regex, '<mark>$1</mark>');
    },
    escapeRegExp(string) {
      return string.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
    }
  }
}
</script>

<style scoped>
i {
  margin-right: 7px;
}

/* 列表视图样式 */
.list-view {
  background: #fff;
  border-radius: 4px;
  border: 1px solid #e4ecf3;
  overflow: hidden;
}

.list-item {
  padding: 12px 20px;
  border-bottom: 1px solid #f0f0f0;
  cursor: pointer;
  transition: all 0.2s ease;
}

.list-item:last-child {
  border-bottom: none;
}

.list-item:hover {
  background-color: #f8f9fa;
}

.list-item-content {
  display: flex;
  align-items: center;
  gap: 12px;
}

.list-item-logo {
  flex-shrink: 0;
  border-radius: 4px;
  object-fit: cover;
}

.list-item-info {
  flex: 1;
  min-width: 0;
}

.list-item-title {
  font-size: 14px;
  font-weight: 600;
  color: #333;
  margin-bottom: 2px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.list-item-desc {
  font-size: 12px;
  color: #666;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.list-item-url {
  flex-shrink: 0;
  font-size: 12px;
  color: #999;
  max-width: 200px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* 搜索高亮样式 */
:deep(mark) {
  background-color: #ffeb3b;
  color: #333;
  padding: 0 2px;
  border-radius: 2px;
  font-weight: 600;
}

@media screen and (max-width: 768px) {
  .list-item-url {
    display: none;
  }
}
</style>
