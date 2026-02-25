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
        <span class="list-title">{{web.title}}</span>
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
  flex-wrap: wrap;
  gap: 10px;
}
.list-item {
  display: flex;
  align-items: center;
  padding: 10px 15px;
  background: #fafafa;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.2s;
  width: calc(50% - 5px);
  box-sizing: border-box;
}
.list-item:hover {
  background: #f0f0f0;
}
.list-logo {
  width: 24px;
  height: 24px;
  border-radius: 3px;
  margin-right: 10px;
  flex-shrink: 0;
}
.list-title {
  font-size: 13px;
  color: #333;
  margin-right: 10px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  flex: 1;
  max-width: 180px;
}
.list-url {
  font-size: 11px;
  color: #999;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 150px;
}
</style>
