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
          <li v-for="(menu, idx) in items" :key="idx" :class="{ active: isCategoryHighlighted(menu) }">
            <a :href="'#' + transName(menu)" class="smooth">
              <i :class="menu.icon"></i>
              <span class="title">{{ transName(menu) }}</span>
            </a>
            <ul v-if="menu.children">
              <li v-for="(submenu, idx) in menu.children" :key="idx" :class="{ active: isCategoryHighlighted(submenu) }">
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
      <nav class="navbar user-info-navbar navbar-fixed-top" role="navigation">
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
            <a href="javascript:void(0)" @click="toggleSearchBox" v-if="!showSearchBox">
              <i class="fa-search"></i>
            </a>
          </li>
          <transition name="fade">
            <li v-if="showSearchBox" class="search-box-wrapper">
              <div class="search-box">
                <input
                  ref="searchInput"
                  type="text"
                  v-model="searchQuery"
                  placeholder="搜索网站..."
                  class="search-input"
                  @blur="handleSearchBlur"
                />
                <button class="search-clear" @mousedown.prevent="clearSearch" v-if="searchQuery">
                  <i class="fa fa-times"></i>
                </button>
              </div>
            </li>
          </transition>
        </ul>
        <ul class="user-info-menu right-links list-inline list-unstyled">
          <li class="view-toggle-group">
            <a
              href="javascript:void(0)"
              :class="{ active: viewMode === 'grid' }"
              @click="viewMode = 'grid'"
              title="卡片视图"
            >
              <i class="fa-th-large"></i>
            </a>
            <a
              href="javascript:void(0)"
              :class="{ active: viewMode === 'list' }"
              @click="viewMode = 'list'"
              title="列表视图"
            >
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

      <div v-if="hasSearchResult">
        <div v-for="(item, idx) in filteredItems" :key="idx">
          <template v-if="item.filteredWeb && item.filteredWeb.length > 0">
            <WebItem :item="item" :transName="transName" :viewMode="viewMode" :filteredWeb="item.filteredWeb" />
          </template>
          <template v-else-if="item.children">
            <div v-for="(subItem, subIdx) in item.children" :key="subIdx">
              <WebItem
                v-if="subItem.filteredWeb && subItem.filteredWeb.length > 0"
                :item="subItem"
                :transName="transName"
                :viewMode="viewMode"
                :filteredWeb="subItem.filteredWeb"
              />
            </div>
          </template>
        </div>
      </div>
      <div v-else class="no-result">
        <i class="fa-search"></i>
        <p>未找到相关网站</p>
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
      searchQuery: "",
      viewMode: "grid",
      showSearchBox: false,
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
    };
  },
  created() {
    this.lang = this.langList[0];
    loadJs();
  },
  watch: {
    showSearchBox(val) {
      if (val) {
        this.$nextTick(() => {
          this.$refs.searchInput && this.$refs.searchInput.focus();
        });
      }
    }
  },
  computed: {
    filteredItems() {
      const query = this.searchQuery.toLowerCase().trim();
      if (!query) {
        return this.items.map(item => ({
          ...item,
          filteredWeb: item.web || null,
          children: item.children ? item.children.map(child => ({
            ...child,
            filteredWeb: child.web || null
          })) : null
        }));
      }
      
      return this.items.map(item => {
        const newItem = { ...item };
        
        if (item.web) {
          newItem.filteredWeb = item.web.filter(web => 
            web.title.toLowerCase().includes(query) ||
            web.desc.toLowerCase().includes(query) ||
            web.url.toLowerCase().includes(query)
          );
        } else {
          newItem.children = item.children.map(child => ({
            ...child,
            filteredWeb: child.web.filter(web =>
              web.title.toLowerCase().includes(query) ||
              web.desc.toLowerCase().includes(query) ||
              web.url.toLowerCase().includes(query)
            )
          }));
        }
        
        return newItem;
      });
    },
    hasSearchResult() {
      const query = this.searchQuery.toLowerCase().trim();
      if (!query) return true;
      
      return this.filteredItems.some(item => {
        if (item.filteredWeb && item.filteredWeb.length > 0) return true;
        if (item.children) {
          return item.children.some(child => child.filteredWeb && child.filteredWeb.length > 0);
        }
        return false;
      });
    }
  },
  methods: {
    transName(webItem) {
      return this.lang.key === "en" ? webItem.en_name : webItem.name;
    },
    clearSearch() {
      this.searchQuery = "";
    },
    toggleSearchBox() {
      this.showSearchBox = !this.showSearchBox;
      if (this.showSearchBox) {
        this.$nextTick(() => {
          this.$refs.searchInput && this.$refs.searchInput.focus();
        });
      }
    },
    handleSearchBlur() {
      setTimeout(() => {
        if (!this.searchQuery) {
          this.showSearchBox = false;
        }
      }, 200);
    },
    isCategoryHighlighted(category) {
      const query = this.searchQuery.toLowerCase().trim();
      if (!query) return false;
      
      const matchWeb = (web) => 
        web.title.toLowerCase().includes(query) ||
        web.desc.toLowerCase().includes(query) ||
        web.url.toLowerCase().includes(query);
      
      if (category.web) {
        return category.web.some(matchWeb);
      }
      if (category.children) {
        return category.children.some(child => 
          child.web && child.web.some(matchWeb)
        );
      }
      return false;
    }
  },
};
</script>

<style>
.sidebar-menu.fixed .sidebar-menu-inner {
  z-index: 1000;
}
.page-container > .main-content {
  padding-top: 62px;
}
.navbar.user-info-navbar.navbar-fixed-top {
  position: fixed;
  top: 0;
  right: 0;
  left: 280px;
  z-index: 999;
  margin: 0;
  border-bottom: 1px solid #e8e8e8;
  height: 61px;
}
.sidebar-menu.collapsed + .main-content .navbar.user-info-navbar.navbar-fixed-top {
  left: 80px;
}
.right-links .view-toggle-group {
  display: inline-flex;
  align-items: center;
  vertical-align: middle;
  border: 1px solid #ddd;
  border-radius: 4px;
  overflow: hidden;
  margin: 0 10px 0 0;
  height: 30px;
  line-height: 30px;
}
.right-links .view-toggle-group a {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0 12px;
  color: #999;
  transition: all 0.2s;
  text-decoration: none;
  background: #fff;
  height: 30px;
}
.right-links .view-toggle-group a:first-child {
  border-right: 1px solid #ddd;
}
.right-links .view-toggle-group a:hover {
  color: #00b39b;
  background: #f5f5f5;
}
.right-links .view-toggle-group a.active {
  color: #fff;
  background: #00b39b;
}
.search-box-wrapper {
  display: inline-block;
  vertical-align: top;
  margin-left: 15px;
}
.search-box-wrapper .search-box {
  position: relative;
  display: inline-block;
  padding: 18px 0;
}
.search-input {
  padding: 6px 30px 6px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  width: 200px;
  font-size: 13px;
  outline: none;
  transition: border-color 0.3s, width 0.3s;
}
.search-input:focus {
  border-color: #00b39b;
  width: 250px;
}
.search-clear {
  position: absolute;
  right: 8px;
  top: 50%;
  transform: translateY(-50%);
  background: transparent;
  border: none;
  color: #999;
  cursor: pointer;
  padding: 0;
  width: 20px;
  height: 20px;
  line-height: 20px;
  text-align: center;
  z-index: 10;
  margin: 0;
}
.search-clear:hover {
  color: #666;
}
.no-result {
  text-align: center;
  padding: 80px 20px;
  color: #999;
}
.no-result i {
  font-size: 48px;
  margin-bottom: 15px;
  display: block;
}
.no-result p {
  font-size: 16px;
  margin: 0;
}
.search-toggle {
  cursor: pointer;
}
.search-toggle a {
  font-size: 16px;
  color: #999;
  transition: color 0.2s;
}
.search-toggle a:hover {
  color: #00b39b;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.2s, width 0.2s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
  width: 0;
}
</style>
