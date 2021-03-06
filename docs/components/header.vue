<template>
  <header class="navbar flex-column" :class="[theme]">
    <div class="container-fluid justify-content-start">
      <router-link class="navbar-brand mr-0" to="/" exact>
        <img class="d-block logo" src="../assets/logo.png" alt="">
      </router-link>

      <docsearch-box
        v-if="$route.path.indexOf('/doc') > -1"
        :options="searchBoxOptions"
      />

      <ul class="navbar-nav flex-row ml-3">
        <li class="nav-item">
          <router-link to="/doc/v2_0/index">文档</router-link>
        </li>
        <li class="nav-item">
          <router-link to="/doc/changelog">变更记录</router-link>
        </li>
        <li class="nav-item">
          <a href="https://github.com/tianyong90/we-vue" target="new">GitHub</a>
        </li>
      </ul>

      <div
        class="dropdown col-1 ml-3 version-picker"
        @mouseenter="dropDownVisible = true"
        @mouseleave="dropDownVisible = false"
        v-if="$route.name !== 'home'"
      >

        <span class="version-number">{{ version | versionText }}</span>
        <div class="dropdown-menu" :class="{ 'd-block': dropDownVisible }">
          <div class="dropdown-item" @click="changeVersion('v1_6')">v1.6</div>
          <div class="dropdown-item" @click="changeVersion('v2_0')">v2.0</div>
        </div>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  name: 'doc-header',

  components: {
    'docsearch-box': () => import('./docsearch-box.vue')
  },

  props: {
    versionPickerVisible: Boolean,
    theme: {
      type: String,
      default: 'light'
    }
  },

  data () {
    return {
      dropDownVisible: false,
      version: this.$route.meta.version
    }
  },

  computed: {
    searchBoxOptions () {
      return {
        algoliaOptions: { 'facetFilters': [`version:${this.version}`] }
      }
    }
  },

  methods: {
    changeVersion (version) {
      this.version = version
      this.$router.push(`/doc/${version}/index`)
    }
  },

  beforeRouteUpdate (to, from, next) {
    this.version = to.meta.version

    next()
  },

  filters: {
    versionText: (value) => {
      return value.replace('_', '.')
    }
  }
}
</script>

<style scoped lang="scss">
  $header-height: 70px;
  $header-background-color: #fff;
  $header-home-background-color: #040f23;

  body {
    position: relative;
  }

  .navbar {
    height: $header-height;
    background-color: $header-background-color;
    z-index: 100;
    border-bottom: 1px solid #dfdfdf;

    .logo {
      width: 60px;
      height: 60px;
    }

    .navbar-nav {
      a {
        color: #333;
        text-decoration: none;
        display: inline-block;
        padding: 0 1em;

        &:hover {
          color: #41b883;
        }

        &.router-link-active {
          color: #41b883;
        }
      }
    }

    &.dark {
      background-color: #01061a;
      border-bottom: none;

      a {
        color: #fff !important;

        &:hover {
          color: #41b883 !important;
        }

        &.router-link-active {
          color: #41b883 !important;
        }
      }
    }

    .version-picker {
      text-align: right;
      position: relative;
      cursor: pointer;
      padding: 10px 30px 10px 0;

      .version-number::after {
        content: '';
        width: 0;
        height: 0;
        position: absolute;
        right: 0;
        top: 50%;
        border: 7px solid;
        border-color:  #888 #fff #fff #fff;
      }

      &:hover {
        color: #41b883;

        .version-number::after {
          top: calc(50% - 7px);
          border-color:  #fff #fff #888 #fff;
        }
      }

      .dropdown-menu {
        top: 90%;
      }
    }
  }
</style>
