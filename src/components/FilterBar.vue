<template>
  <div class="filter-bar pull-right">
    <div class="form-inline">
      <div class="form-group">
        <label>Pesquisar: </label>
        <input type="text" v-model="filterText" class="form-control" @keyup.enter="doFilter" placeholder="">
        <button class="btn btn-primary" v-on:change="doFilter" @click="doFilter">Ok</button>
        <button class="btn btn-default" @click="resetFilter">Resetar</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      filterText: ''
    }
  },
  methods: {
    doFilter () {
      this.$events.fire('filter-set', this.filterText)
    },
    resetFilter () {
      this.filterText = ''  // clear the text in text input
      this.$events.fire('filter-reset')
    }
  }
}
</script>
<style>
.filter-bar {
  margin-bottom: 10px;
}
</style>