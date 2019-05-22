<template>
  <header
    :class="[
      'header',
      { 'header--hide': shouldHideHeader }
    ]"
  >
    <div class="header__wrapper">
      <router-link
        class="header__logo"
        to="/"
      >
        <img
          src="/public/2.0/logos/readr-logo-light.svg"
          alt=""
        >
      </router-link>
      <NavsDefault
        v-show="layout === 'default'"
        class="header__navs"
      />
      <NavsSeries
        v-show="layout === 'series'"
        class="header__navs"
        @series="toggleNavSeries('seriesContents')"
        @comment="toggleNavSeries('comment')"
      />
    </div>
    <Sidebar
      v-if="layout === 'series'"
      :show-sidebar.sync="showSidebar"
      class="header__sidebar"
    >
      <SidebarSeriesContents v-show="currentSidebarSlot === 'seriesContents'" />
      <SidebarComment v-show="currentSidebarSlot === 'comment'" />
    </Sidebar>
  </header>
</template>

<script>
import { mapState, mapMutations, mapGetters } from 'vuex'

import NavsDefault from './NavsDefault.vue'
import NavsSeries from './NavsSeries.vue'
import Sidebar from './Sidebar.vue'
import SidebarSeriesContents from './SidebarSeriesContents.vue'
import SidebarComment from './SidebarComment.vue'

export default {
  components: {
    NavsDefault,
    NavsSeries,
    Sidebar,
    SidebarSeriesContents,
    SidebarComment
  },
  data () {
    return {
      showSidebar: false,
      currentSidebarSlot: 'seriesContents'
    }
  },
  computed: {
    ...mapState({
      shouldHideHeader: state => state.UIAppHeader.shouldHide
    }),
    ...mapGetters({
      layout: 'UIAppHeader/layout'
    })
  },
  watch: {
    layout () {
      if (this.layout === 'default' && this.showSidebar) {
        this.showSidebar = false
        this.currentSidebarSlot = 'seriesContents'
      }
    }
  },
  mounted () {
    window.addEventListener('message', e => {
      // if (e.origin !== window.origin) { return }
      const { data = '' } = e
      switch (data) {
        case 'scrolldown':
          this.SET_HIDE_HEADER(true)
          break
        case 'scrollup':
          this.SET_HIDE_HEADER(false)
          break
        default:
          break
      }
    })
  },
  methods: {
    toggleNavSeries (navClicked) {
      if (this.currentSidebarSlot === navClicked && this.showSidebar) {
        this.showSidebar = false
        return
      }

      this.currentSidebarSlot = navClicked
      if (!this.showSidebar) {
        this.showSidebar = true
      }
    },
    toggleSidebar () {
      this.showSidebar = !this.showSidebar
    },

    ...mapMutations({
      SET_HIDE_HEADER: 'UIAppHeader/SET_HIDE'
    })
  }
}
</script>

<style lang="stylus" scoped>
.header
  width 100%
  height 50px
  background-color #444746
  z-index 1000
  transition transform .25s ease-out
  &--hide
    transform translateY(-70px)
  &__wrapper
    max-width 1400px
    margin 0 auto
    display flex
    justify-content space-between
    align-items center
  &__logo
    display block
    width 60px
    position relative
    top 10px
    z-index 1000
    img
      width 100%
  &__navs
    position relative
    top -5px
  &__sidebar
    z-index 999
</style>