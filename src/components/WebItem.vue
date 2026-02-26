<template>
  <div>
    <h4 class="text-gray">
      <i class="linecons-tag" :id="transName(item)"></i>{{transName(item)}}
    </h4>
    <div v-if="viewMode === 'card'" class="row">
      <div class="col-sm-3" v-for="(web, idx) in displayWebs" :key="idx">
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
                <strong>{{web.title}}</strong>
              </a>
              <p class="overflowClip_2">{{web.desc}}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-else class="list-view">
      <div class="list-item" v-for="(web, idx) in displayWebs" :key="idx"
        @click="openweb(web.url)"
        :title="web.url">
        <div class="list-item-logo">
          <img :src="web.logo" class="img-circle" width="32">
        </div>
        <div class="list-item-content">
          <div class="list-item-title">{{web.title}}</div>
          <div class="list-item-desc">{{web.desc}}</div>
        </div>
        <div class="list-item-url">
          <span class="url-text">{{web.url}}</span>
          <i class="fa-external-link"></i>
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
    filteredWebs: {
      type: Array,
      default: null
    },
    viewMode: {
      type: String,
      default: 'card'
    }
  },
  computed: {
    displayWebs() {
      return this.filteredWebs || this.item.web || [];
    }
  },
  methods: {
    openweb(url) {
      window.open(url, '_blank');
    }
  }
}
</script>

<style scoped>
i {
  margin-right: 7px;
}

.list-view {
  background: #fff;
  border-radius: 4px;
  border: 1px solid #e4ecf3;
  overflow: hidden;
}

.list-item {
  display: flex;
  align-items: center;
  padding: 12px 20px;
  border-bottom: 1px solid #e4ecf3;
  cursor: pointer;
  transition: all 0.3s ease;
}

.list-item:last-child {
  border-bottom: none;
}

.list-item:hover {
  background: #f8f9fa;
  transform: translateX(3px);
}

.list-item-logo {
  flex-shrink: 0;
  margin-right: 15px;
}

.list-item-logo img {
  display: block;
}

.list-item-content {
  flex: 1;
  min-width: 0;
  margin-right: 20px;
}

.list-item-title {
  font-weight: 600;
  color: #2c2e2f;
  margin-bottom: 4px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.list-item-desc {
  font-size: 13px;
  color: #888;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.list-item-url {
  flex-shrink: 0;
  display: flex;
  align-items: center;
  color: #aaa;
  font-size: 12px;
}

.list-item-url .url-text {
  max-width: 200px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  margin-right: 8px;
}

.list-item-url i {
  font-size: 12px;
  opacity: 0;
  transition: opacity 0.3s;
}

.list-item:hover .list-item-url i {
  opacity: 1;
}
</style>
