<template>
  <div class="page-container">
    <div class="sidebar-menu toggle-others fixed">
      <div class="sidebar-menu-inner">
        <header class="logo-env">
          <!-- logo -->
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
        <!-- 侧边栏 -->
        <ul id="main-menu" class="main-menu">
          <li v-for="(menu, idx) in items" :key="idx" :class="{ 'has-results': hasSearchResults(menu) }">
            <a :href="'#' + transName(menu)" class="smooth">
              <i :class="menu.icon"></i>
              <span class="title">{{ transName(menu) }}</span>
            </a>
            <ul v-if="menu.children">
              <li v-for="(submenu, idx) in menu.children" :key="idx" :class="{ 'has-results': hasSearchResults(submenu) }">
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
          <!-- 关于本站 -->
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
      <nav class="navbar user-info-navbar navbar-fixed-custom" role="navigation">
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
          <li class="search-trigger">
            <a href="javascript:void(0)" @click="toggleSearch">
              <i class="fa-search"></i>
            </a>
          </li>
          <li class="search-box-wrapper" :class="{ 'search-visible': showSearch }">
            <div class="search-box">
              <input
                type="text"
                v-model="searchQuery"
                placeholder="搜索网站名称、描述或网址..."
                @input="handleSearch"
                ref="searchInput"
              />
              <a href="javascript:void(0)" class="search-clear" @click="clearSearch" v-if="searchQuery">
                <i class="fa-times"></i>
              </a>
            </div>
          </li>
        </ul>
        <ul class="user-info-menu right-links list-inline list-unstyled">
          <li class="view-toggle">
            <a href="javascript:void(0)" 
              :class="{ active: viewMode === 'grid' }" 
              @click="viewMode = 'grid'"
              title="卡片视图">
              <i class="fa-th-large"></i>
            </a>
          </li>
          <li class="view-toggle">
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

      <div class="content-wrapper" :class="{ 'searching': isSearching }">
        <div class="no-results" v-if="isSearching && totalResults === 0">
          <i class="fa-search"></i>
          <p>未找到相关网站</p>
        </div>
        
        <div v-for="(item, idx) in filteredItems" :key="idx">
          <div v-if="item.web">
            <WebItem :item="getFilteredItem(item)" :transName="transName" :viewMode="viewMode" />
          </div>
          <div v-else v-for="(subItem, idx) in getFilteredChildren(item)" :key="idx">
            <WebItem :item="subItem" :transName="transName" :viewMode="viewMode" />
          </div>
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
      searchQuery: "",
      showSearch: false,
      viewMode: "grid",
    };
  },
  computed: {
    isSearching() {
      return this.searchQuery.trim().length > 0;
    },
    filteredItems() {
      if (!this.isSearching) {
        return this.items;
      }
      const query = this.searchQuery.toLowerCase().trim();
      return this.items.filter(item => {
        if (item.web) {
          return item.web.some(web => this.matchWeb(web, query));
        }
        if (item.children) {
          return item.children.some(child => 
            child.web && child.web.some(web => this.matchWeb(web, query))
          );
        }
        return false;
      });
    },
    totalResults() {
      if (!this.isSearching) return 0;
      let count = 0;
      const query = this.searchQuery.toLowerCase().trim();
      this.items.forEach(item => {
        if (item.web) {
          count += item.web.filter(web => this.matchWeb(web, query)).length;
        }
        if (item.children) {
          item.children.forEach(child => {
            if (child.web) {
              count += child.web.filter(web => this.matchWeb(web, query)).length;
            }
          });
        }
      });
      return count;
    },
  },
  created() {
    this.lang = this.langList[0];
    loadJs();
  },
  methods: {
    transName(webItem) {
      return this.lang.key === "en" ? webItem.en_name : webItem.name;
    },
    matchWeb(web, query) {
      return (
        web.title.toLowerCase().includes(query) ||
        web.desc.toLowerCase().includes(query) ||
        web.url.toLowerCase().includes(query)
      );
    },
    getFilteredChildren(item) {
      if (!this.isSearching) {
        return item.children || [];
      }
      const query = this.searchQuery.toLowerCase().trim();
      return (item.children || []).filter(child => 
        child.web && child.web.some(web => this.matchWeb(web, query))
      ).map(child => ({
        ...child,
        web: child.web.filter(web => this.matchWeb(web, query))
      }));
    },
    getFilteredItem(item) {
      if (!this.isSearching) {
        return item;
      }
      const query = this.searchQuery.toLowerCase().trim();
      return {
        ...item,
        web: item.web.filter(web => this.matchWeb(web, query))
      };
    },
    hasSearchResults(item) {
      if (!this.isSearching) return false;
      const query = this.searchQuery.toLowerCase().trim();
      if (item.web) {
        return item.web.some(web => this.matchWeb(web, query));
      }
      if (item.children) {
        return item.children.some(child => 
          child.web && child.web.some(web => this.matchWeb(web, query))
        );
      }
      return false;
    },
    toggleSearch() {
      this.showSearch = !this.showSearch;
      if (this.showSearch) {
        this.$nextTick(() => {
          this.$refs.searchInput && this.$refs.searchInput.focus();
        });
      } else {
        this.clearSearch();
      }
    },
    handleSearch() {
    },
    clearSearch() {
      this.searchQuery = "";
    },
  },
};
</script>

<style>
.navbar.navbar-fixed-custom {
  position: fixed !important;
  top: 0 !important;
  left: 280px !important;
  right: 0 !important;
  z-index: 99 !important;
  background: #fff !important;
  border-bottom: 1px solid #e8e8e8 !important;
  margin: 0 !important;
  height: 55px !important;
  min-height: 55px !important;
}

.navbar.navbar-fixed-custom .user-info-menu > li > a {
  padding: 19px 20px !important;
}

.navbar.navbar-fixed-custom .left-links li.language-switcher a img {
  margin-top: -2px;
}

.page-container.sidebar-collapsed .navbar.navbar-fixed-custom {
  left: 60px !important;
}

@media (max-width: 991px) {
  .navbar.navbar-fixed-custom {
    left: 0 !important;
  }
}

.main-content {
  padding-top: 55px !important;
  margin-left: 0 !important;
}

.sidebar-menu.fixed {
  top: 0 !important;
  padding-top: 0 !important;
  z-index: 100 !important;
}

.sidebar-menu-inner {
  padding-top: 0 !important;
}

.search-trigger {
  cursor: pointer;
}

.search-trigger a:hover {
  color: #37363e !important;
}

li.search-box-wrapper {
  display: inline-block;
  overflow: hidden;
  width: 0;
  max-width: 0;
  transition: all 0.3s ease;
  vertical-align: middle;
  float: none !important;
  border: none !important;
}

li.search-box-wrapper.search-visible {
  width: 280px !important;
  max-width: 280px !important;
  padding: 10px 0 !important;
}

.search-box {
  position: relative;
  display: flex;
  align-items: center;
  margin-left: 0;
  margin-right: 10px;
}

.search-box input {
  width: 100%;
  padding: 6px 30px 6px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 13px;
  outline: none;
  background: #fafafa;
}

.search-box input:focus {
  border-color: #66afe9;
  background: #fff;
}

.search-clear {
  position: absolute;
  right: 8px;
  top: 50%;
  transform: translateY(-50%);
  color: #999;
  cursor: pointer;
  font-size: 12px;
}

.search-clear:hover {
  color: #666;
}

.view-toggle {
  float: left;
  border: none !important;
}

.view-toggle a {
  padding: 19px 4px !important;
  color: #979898 !important;
  transition: color 0.2s;
}

.view-toggle:first-of-type a {
  padding-right: 2px !important;
}

.view-toggle:last-of-type a {
  padding-left: 2px !important;
}

.view-toggle a:hover,
.view-toggle a.active {
  color: #37363e !important;
}

.content-wrapper.searching .no-results {
  display: block;
}

.no-results {
  display: none;
  text-align: center;
  padding: 80px 20px;
  color: #999;
}

.no-results i {
  font-size: 48px;
  margin-bottom: 20px;
}

.no-results p {
  font-size: 16px;
}

#main-menu li.has-results > a,
#main-menu li.has-results > a i,
#main-menu li.has-results > a .title {
  color: #00b393 !important;
}

#main-menu ul li.has-results > a {
  color: #00b393 !important;
}
</style>
