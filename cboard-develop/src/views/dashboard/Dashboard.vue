<template>
  <div>
    <component :is="currentComponent"></component>

    <el-dialog
      :title="dialogName"
      :visible.sync="visible"
      :append-to-body="true"
      @close="handleDialogClose"
      width="80%"
      center>
      <component :is="dialogComponent" :widget="dialogWidget" :filters="dialogFilters" :key="dialogName"></component>
    </el-dialog>

  </div>
</template>

<script>
export default {
  name: 'Dashboard',
  created () {
    this.$store.dispatch('dashboard/getBoardData', this.$route.params.id)
    this.visible = this.$store.state.widget.open
  },
  components: {
    Grid: () => import('./Grid'),
    Timeline: () => import('./Timeline'),
    Gridster: () => import('./Gridster')
  },
  watch: {
    '$route' (to, from) {
      this.$store.dispatch('dashboard/getBoardData', this.$route.params.id)
    },
    dialogVisible (newValue, b) {
      this.visible = newValue
    }
  },
  computed: {
  	currentComponent: function () {
      if (!this.$store.state.dashboard.complete) return null
      const type = this.$store.state.dashboard.type
      if (type === 'timeline') {
        return 'Timeline'
      } else if (type === 'gridster') {
        return 'gridster'
      } else {
        return 'Grid'
      }
  	},
    type () {
      return this.$store.state.dashboard.type
    },
    dialogVisible () {
      return this.$store.state.widget.open
    },
    dialogWidget () {
      return this.$store.state.widget.widget
    },
    dialogFilters () {
      return this.$store.state.widget.filters
    },
    dialogName () {
      return this.$store.state.widget.name
    },
    dialogComponent () {
      return this.$store.state.widget.componentOptions
    }
  },
  methods: {
    handleDialogClose () {
      this.$store.commit('widget/closeWidget')
    }
  },
  data () {
    return {
      loading: true,
      visible: false
    }
  },
  beforeRouteUpdate (to, from, next) {
    this.$store.commit('dashboard/reset') // 将 complete 置为 false，不这样做的话 vuex 会缓存数据，也就是说会一直保持 true
    this.$store.commit('params/resetFilters') // 清除 filters
    next()
  },
  destroyed () {
    this.$store.commit('dashboard/reset') // 将 complete 置为 false，不这样做的话 vuex 会缓存数据，也就是说会一直保持 true
    this.$store.commit('params/resetFilters') // 清除 filters
  }
}
</script>
