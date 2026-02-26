<template>
  <div class="page-container">
    <div class="sidebar-menu toggle-others fixed">
      <div class="sidebar-menu-inner">
        <header class="logo-env">
          <div class="logo">
            <a href="javascript:void(0)" class="logo-expanded">
              <img src="../assets/images/logo@2x.png" width="100%" alt="" />
            </a>
            <a href="javascript:void(0)" class="logo-collapsed">
              <img
                src="../assets/images/logo-collapsed@2x.png"
                width="40"
                alt=""
              />
            </a>
          </div>
          <div class="mobile-menu-toggle visible-xs">
            <a href="javascript:void(0)" data-toggle="user-info-menu">
              <i class="linecons-cog"></i>
            </a>
            <a href="javascript:void(0)" data-toggle="mobile-menu">
              <i class="fa-bars"></i>
            </a>
          </div>
        </header>
        <ul id="main-menu" class="main-menu">
          <li v-for="(menu, idx) in filteredItems" :key="idx" :class="{ active: isCategoryActive(menu) }">
            <a :href="'#' + transName(menu)" class="smooth">
              <i :class="menu.icon"></i>
              <span class="title">{{ transName(menu) }}</span>
            </a>
            <ul v-if="menu.children">
              <li v-for="(submenu, idx) in menu.children" :key="idx" :class="{ active: isSubCategoryActive(submenu) }">
                <a :href="'#' + transName(submenu)" class="smooth">
                  <span class="title">{{ transName(submenu) }}</span>
                  <span
                    v-show="submenu.is_hot"
                    class="label label-pink pull-right hidden-collapsed"
                    >Hot</span
                  >
                </a>
              </li>
            </ul>
          </li>
          <li class="submit-tag">
            <router-link to="/about">
              <i class="linecons-heart"></i>
              <span class="tooltip-blue">关于本站</span>
              <span class="label label-Primary pull-right hidden-collapsed"
                >♥︎</span
              >
            </router-link>
          </li>
        </ul>
      </div>
    </div>

    <div class="main-content">
      <nav class="navbar user-info-navbar" role="navigation">
        <ul class="user-info-menu left-links list-inline list-unstyled">
          <li class="hidden-sm hidden-xs">
            <a href="javascript:void(0)" data-toggle="sidebar"><i class="fa-bars"></i></a>
          </li>
          <li class="dropdown hover-line language-switcher">
            <a href="javascript:void(0)" class="dropdown-toggle" data-toggle="dropdown">
              <img :src="lang.flag" /> {{ lang.name }}
            </a>
            <ul class="dropdown-menu languages">
              <li
                :class="{ active: langItem.key === lang.key }"
                v-for="langItem in langList"
                :key="langItem.key"
              >
                <a href="javascript:void(0)" @click="lang = langItem">
                  <img :src="langItem.flag" /> {{ langItem.name }}
                </a>
              </li>
            </ul>
          </li>
          <li class="search-toggle">
            <a href="javascript:void(0)" @click="toggleSearch">
              <i class="fa-search"></i>
            </a>
          </li>
        </ul>
        <ul class="user-info-menu right-links list-inline list-unstyled">
          <li class="hidden-sm hidden-xs search-box-li" v-show="showSearch">
            <div class="search-box">
              <input 
                type="text" 
                v-model="searchQuery" 
                placeholder="搜索网站..." 
                class="search-input"
                ref="searchInput"
              />
              <a href="javascript:void(0)" class="search-clear" @click="clearSearch" v-show="searchQuery">
                <i class="fa-times"></i>
              </a>
            </div>
          </li>
          <li class="view-switcher hidden-sm hidden-xs">
            <a href="javascript:void(0)" 
               :class="{ active: viewMode === 'card' }" 
               @click="viewMode = 'card'"
               title="卡片视图">
              <i class="fa-th-large"></i>
            </a>
            <a href="javascript:void(0)" 
               :class="{ active: viewMode === 'list' }" 
               @click="viewMode = 'list'"
               title="列表视图">
              <i class="fa-list"></i>
            </a>
          </li>
          <li class="hidden-sm hidden-xs">
            <a href="https://github.com/Anjaxs/WebStack-vue" target="_blank">
              <i class="fa-github"></i> GitHub
            </a>
          </li>
        </ul>
      </nav>

      <div v-if="searchQuery && filteredItems.length === 0" class="no-results">
        <div class="no-results-content">
          <i class="fa-search"></i>
          <p>未找到相关网站</p>
          <span>尝试其他关键词</span>
        </div>
      </div>

      <div v-for="(item, idx) in filteredItems" :key="idx">
        <div v-if="item.web && getFilteredWebs(item).length > 0">
          <WebItem :item="item" :transName="transName" :filteredWebs="getFilteredWebs(item)" :viewMode="viewMode" />
        </div>
        <div v-else v-for="(subItem, idx) in item.children" :key="idx">
          <WebItem v-if="getFilteredWebs(subItem).length > 0" :item="subItem" :transName="transName" :filteredWebs="getFilteredWebs(subItem)" :viewMode="viewMode" />
        </div>
      </div>

      <Footer />
    </div>
  </div>
</template>

<script>
import WebItem from "../components/WebItem.vue";
import Footer from "../components/Footer.vue";
import itemsData from "../assets/data.json";
import { loadJs } from '../assets/js/app.js'

export default {
  name: "Index",
  components: {
    WebItem,
    Footer,
  },
  data() {
    return {
      items: itemsData,
      lang: {},
      langList: [
        {
          key: "zh",
          name: "简体中文",
          flag: "./assets/images/flags/flag-cn.png",
        },
        {
          key: "en",
          name: "English",
          flag: "./assets/images/flags/flag-us.png",
        },
      ],
      showSearch: false,
      searchQuery: '',
      viewMode: 'card'
    };
  },
  computed: {
    filteredItems() {
      if (!this.searchQuery.trim()) {
        return this.items;
      }
      const query = this.searchQuery.toLowerCase();
      return this.items.filter(item => {
        if (item.web) {
          return this.matchWebs(item.web, query);
        }
        if (item.children) {
          return item.children.some(child => this.matchWebs(child.web, query));
        }
        return false;
      });
    }
  },
  created() {
    this.lang = this.langList[0];
    loadJs();
  },
  methods: {
    transName(webItem) {
      return this.lang.key === "en" ? webItem.en_name : webItem.name;
    },
    toggleSearch() {
      this.showSearch = !this.showSearch;
      if (this.showSearch) {
        this.$nextTick(() => {
          if (this.$refs.searchInput) {
            this.$refs.searchInput.focus();
          }
        });
      } else {
        this.searchQuery = '';
      }
    },
    clearSearch() {
      this.searchQuery = '';
    },
    matchWebs(webs, query) {
      if (!webs) return false;
      return webs.some(web => 
        (web.title && web.title.toLowerCase().includes(query)) ||
        (web.desc && web.desc.toLowerCase().includes(query)) ||
        (web.url && web.url.toLowerCase().includes(query))
      );
    },
    getFilteredWebs(item) {
      if (!item.web) return [];
      if (!this.searchQuery.trim()) {
        return item.web;
      }
      const query = this.searchQuery.toLowerCase();
      return item.web.filter(web => 
        (web.title && web.title.toLowerCase().includes(query)) ||
        (web.desc && web.desc.toLowerCase().includes(query)) ||
        (web.url && web.url.toLowerCase().includes(query))
      );
    },
    isCategoryActive(menu) {
      if (!this.searchQuery.trim()) return false;
      if (menu.web) {
        return this.matchWebs(menu.web, this.searchQuery.toLowerCase());
      }
      if (menu.children) {
        return menu.children.some(child => this.matchWebs(child.web, this.searchQuery.toLowerCase()));
      }
      return false;
    },
    isSubCategoryActive(submenu) {
      if (!this.searchQuery.trim()) return false;
      return this.matchWebs(submenu.web, this.searchQuery.toLowerCase());
    }
  },
};
</script>

<style scoped>
.search-toggle a {
  cursor: pointer;
}

.search-box-li {
  display: inline-block !important;
  float: none !important;
  vertical-align: middle;
}

.search-box {
  position: relative;
  display: flex;
  align-items: center;
}

.search-input {
  width: 200px;
  padding: 8px 30px 8px 12px;
  border: 1px solid #e4ecf3;
  border-radius: 4px;
  font-size: 13px;
  outline: none;
  transition: all 0.3s;
}

.search-input:focus {
  border-color: #2c2e2f;
  box-shadow: 0 0 5px rgba(44, 46, 47, 0.1);
}

.search-clear {
  position: absolute;
  right: 8px;
  color: #999;
  cursor: pointer;
  padding: 4px;
}

.search-clear:hover {
  color: #2c2e2f;
}

.view-switcher {
  display: inline-block !important;
  float: left !important;
}

.view-switcher a {
  display: inline-block !important;
  float: none !important;
  padding: 30px 12px !important;
  margin: 0;
  color: #979898;
  transition: all 0.3s;
}

.view-switcher a:hover {
  color: #606161;
  background: transparent;
}

.view-switcher a.active {
  color: #2c2e2f;
  background: transparent;
  border-bottom: 2px solid #2c2e2f;
}

.no-results {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 400px;
  text-align: center;
}

.no-results-content {
  color: #999;
}

.no-results-content i {
  font-size: 48px;
  margin-bottom: 20px;
  display: block;
}

.no-results-content p {
  font-size: 18px;
  margin-bottom: 10px;
  color: #666;
}

.no-results-content span {
  font-size: 14px;
}
</style>
