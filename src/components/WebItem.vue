<template>
  <div>
    <h4 class="text-gray">
      <i class="linecons-tag" :id="transName(item)"></i>{{transName(item)}}
    </h4>
    <div v-if="viewMode === 'grid'" class="row">
      <div class="col-sm-3" v-for="(web, idx) in websites" :key="idx">
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
    <div v-else class="list-view-container">
      <div class="list-item" v-for="(web, idx) in websites" :key="idx" @click="openweb(web.url)">
        <img :src="web.logo" class="lozad list-logo">
        <div class="list-content">
          <span class="list-title">{{web.title}}</span>
          <span class="list-desc">{{web.desc}}</span>
        </div>
        <span class="list-url">{{web.url}}</span>
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
    filteredWeb: {
      type: Array,
      default: null
    }
  },
  computed: {
    websites() {
      return this.filteredWeb || this.item.web || [];
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
.list-view-container {
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.list-item {
  display: flex;
  align-items: center;
  padding: 10px 15px;
  background: #fff;
  border: 1px solid #eee;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s;
}
.list-item:hover {
  background: #fafafa;
  border-color: #00b39b;
}
.list-logo {
  width: 28px;
  height: 28px;
  border-radius: 3px;
  margin-right: 12px;
  flex-shrink: 0;
}
.list-content {
  flex: 1;
  min-width: 0;
  margin-right: 15px;
}
.list-title {
  font-size: 14px;
  color: #333;
  font-weight: 500;
  display: block;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.list-desc {
  font-size: 12px;
  color: #999;
  display: block;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  margin-top: 2px;
}
.list-url {
  font-size: 12px;
  color: #bbb;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 200px;
  flex-shrink: 0;
}
</style>
