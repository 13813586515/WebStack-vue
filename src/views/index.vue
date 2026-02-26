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
          <li v-for="(menu, idx) in filteredItems" :key="idx" :class="{ 'has-result': isSearching && hasResults(menu) }">
            <a :href="'#' + transName(menu)" class="smooth">
              <i :class="menu.icon"></i>
              <span class="title">{{ transName(menu) }}</span>
            </a>
            <ul v-if="menu.children">
              <li v-for="(submenu, subIdx) in menu.children" :key="subIdx" :class="{ 'has-result': isSearching && hasResults(submenu) }">
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
      <nav class="navbar user-info-navbar fixed" role="navigation">
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
          <!-- 搜索按钮 -->
          <li class="search-toggle">
            <a href="javascript:void(0)" @click="toggleSearch">
              <i class="fa-search"></i>
            </a>
          </li>
          <!-- 搜索框 -->
          <li class="search-box" v-show="showSearch">
            <div class="search-input-wrapper">
              <input
                type="text"
                class="search-input"
                v-model="searchQuery"
                @input="onSearchInput"
                placeholder="搜索网站..."
                ref="searchInput"
              />
              <a href="javascript:void(0)" class="search-clear" @click="clearSearch" v-show="searchQuery">
                <i class="fa-close"></i>
              </a>
            </div>
          </li>
        </ul>
        <ul class="user-info-menu right-links list-inline list-unstyled">
          <!-- 视图切换按钮 -->
          <li class="view-toggle">
            <a href="javascript:void(0)" @click="setViewMode('grid')" :class="{ active: viewMode === 'grid' }" title="卡片视图">
              <i class="fa-th-large"></i>
            </a>
          </li>
          <li class="view-toggle">
            <a href="javascript:void(0)" @click="setViewMode('list')" :class="{ active: viewMode === 'list' }" title="列表视图">
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

      <!-- 空结果提示 -->
      <div v-if="isSearching && noResults" class="empty-results">
        <div class="empty-results-content">
          <i class="fa-search"></i>
          <p>未找到相关网站</p>
          <span>请尝试其他关键词</span>
        </div>
      </div>

      <div v-for="(item, idx) in filteredItems" :key="idx" v-show="!noResults">
        <div v-if="item.web && hasResults(item)">
          <WebItem :item="item" :transName="transName" :viewMode="viewMode" :searchQuery="searchQuery" />
        </div>
        <div v-else v-for="(subItem, subIdx) in item.children" :key="subIdx">
          <WebItem v-if="hasResults(subItem)" :item="subItem" :transName="transName" :viewMode="viewMode" :searchQuery="searchQuery" />
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
      viewMode: "grid", // 'grid' 或 'list'
    };
  },
  created() {
    this.lang = this.langList[0];
    loadJs();
  },
  computed: {
    isSearching() {
      return this.searchQuery.trim() !== "";
    },
    filteredItems() {
      if (!this.isSearching) {
        return this.items;
      }
      const query = this.searchQuery.toLowerCase().trim();
      return this.items.map(item => {
        if (item.web) {
          const filteredWeb = item.web.filter(web =>
            web.title.toLowerCase().includes(query) ||
            web.desc.toLowerCase().includes(query) ||
            web.url.toLowerCase().includes(query)
          );
          if (filteredWeb.length > 0) {
            return { ...item, web: filteredWeb };
          }
          return null;
        } else if (item.children) {
          const filteredChildren = item.children.map(child => {
            if (child.web) {
              const filteredWeb = child.web.filter(web =>
                web.title.toLowerCase().includes(query) ||
                web.desc.toLowerCase().includes(query) ||
                web.url.toLowerCase().includes(query)
              );
              if (filteredWeb.length > 0) {
                return { ...child, web: filteredWeb };
              }
            }
            return null;
          }).filter(child => child !== null);
          if (filteredChildren.length > 0) {
            return { ...item, children: filteredChildren };
          }
          return null;
        }
        return null;
      }).filter(item => item !== null);
    },
    noResults() {
      return this.isSearching && this.filteredItems.length === 0;
    },
  },
  methods: {
    transName(webItem) {
      return this.lang.key === "en" ? webItem.en_name : webItem.name;
    },
    toggleSearch() {
      this.showSearch = !this.showSearch;
      if (this.showSearch) {
        this.$nextTick(() => {
          this.$refs.searchInput.focus();
        });
      }
    },
    onSearchInput() {
      // 实时过滤已在 computed 中处理
    },
    clearSearch() {
      this.searchQuery = "";
      this.showSearch = false;
    },
    setViewMode(mode) {
      this.viewMode = mode;
      localStorage.setItem("viewMode", mode);
    },
    hasResults(item) {
      // 只有在搜索状态下才高亮有结果的分类
      if (!this.isSearching) return false;
      if (item.web) {
        return item.web.length > 0;
      }
      if (item.children) {
        return item.children.some(child => child.web && child.web.length > 0);
      }
      return false;
    },
  },
  mounted() {
    const savedViewMode = localStorage.getItem("viewMode");
    if (savedViewMode) {
      this.viewMode = savedViewMode;
    }
  },
};
</script>

<style scoped>
/* 搜索框样式 */
.search-box {
  padding: 0 15px;
  display: flex;
  align-items: center;
  height: 100%;
}

.search-input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.search-input {
  width: 200px;
  height: 36px;
  padding: 0 30px 0 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
  outline: none;
  transition: all 0.3s ease;
}

.search-input:focus {
  border-color: #68b828;
  box-shadow: 0 0 0 2px rgba(104, 184, 40, 0.2);
}

.search-clear {
  position: absolute;
  right: 8px;
  color: #979898;
  font-size: 12px;
  text-decoration: none;
}

.search-clear:hover {
  color: #606161;
}

/* 视图切换按钮样式 */
.view-toggle a {
  padding: 30px 15px !important;
}

.view-toggle a.active {
  color: #68b828 !important;
  background-color: #f8f8f8;
}

.view-toggle a.active i {
  color: #68b828;
}

/* 空结果提示样式 */
.empty-results {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 400px;
  padding: 40px;
}

.empty-results-content {
  text-align: center;
  color: #979898;
}

.empty-results-content i {
  font-size: 64px;
  margin-bottom: 20px;
  display: block;
}

.empty-results-content p {
  font-size: 18px;
  margin-bottom: 8px;
  color: #606161;
}

.empty-results-content span {
  font-size: 14px;
}

/* 侧边栏高亮 */
.main-menu li.has-result > a {
  color: #68b828;
}

.main-menu li.has-result > a .title {
  font-weight: 600;
}

/* 固定导航栏 */
.user-info-navbar.fixed {
  position: fixed;
  top: 0;
  right: 0;
  left: 280px;
  z-index: 1000;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

@media screen and (max-width: 768px) {
  .user-info-navbar.fixed {
    left: 0;
  }
}

/* 为固定导航栏调整内容区域 */
.main-content {
  padding-top: 90px;
}
</style>
