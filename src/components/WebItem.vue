<template>
  <div>
    <h4 class="text-gray">
      <i class="linecons-tag" :id="transName(item)"></i>{{transName(item)}}
    </h4>
    <div class="row" v-if="viewMode === 'grid'">
      <div class="col-sm-3" v-for="(web, idx) in item.web" :key="idx">
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
    <div class="list-view" v-else>
      <div class="list-item" v-for="(web, idx) in item.web" :key="idx" @click="openweb(web.url)">
        <div class="list-item-logo">
          <img :src="web.logo" class="lozad">
        </div>
        <div class="list-item-content">
          <strong class="list-item-title">{{web.title}}</strong>
          <span class="list-item-url">{{web.url}}</span>
          <p class="list-item-desc">{{web.desc}}</p>
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
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.list-item {
  display: flex;
  align-items: center;
  padding: 12px 16px;
  background: #fff;
  border: 1px solid #e8e8e8;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.list-item:hover {
  border-color: #ccc;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

.list-item-logo {
  flex-shrink: 0;
  width: 32px;
  height: 32px;
  margin-right: 16px;
}

.list-item-logo img {
  width: 100%;
  height: 100%;
  border-radius: 4px;
  object-fit: contain;
}

.list-item-content {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.list-item-title {
  font-size: 14px;
  color: #37363e;
}

.list-item-url {
  font-size: 12px;
  color: #979898;
}

.list-item-desc {
  font-size: 12px;
  color: #666;
  margin: 0;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
</style>
