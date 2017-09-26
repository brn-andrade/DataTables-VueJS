<template xmlns:>
  <div class="container">
    <filter-bar></filter-bar>
    <vuetable ref="vuetable"
      api-url="https://vuetable.ratiw.net/api/users"
      :fields="fields"
      :css="css"
      pagination-path=""
      :per-page="10"
      :multi-sort="true"
      multi-sort-key="ctrl"
      :sort-order="sortOrder"
      :append-params="moreParams"
      :render-icon="renderIcon"
      @vuetable:pagination-data="onPaginationData"
    >
      <template slot="actions" scope="props">
        <div class="custom-actions">
          <button class="btn btn-default btn-sm"
            @click="onAction('view-item', props.rowData, props.rowIndex)">
            <span class="glyphicon glyphicon-zoom-in"></span>
          </button>
          <button class="btn btn-default btn-sm"
            @click="onAction('edit-item', props.rowData, props.rowIndex)">
            <span class="glyphicon glyphicon-pencil"></span>
          </button>
          <button class="btn btn-default btn-sm"
            @click="onAction('delete-item', props.rowData, props.rowIndex)">
            <span class="glyphicon glyphicon-remove"></span>
          </button>
        </div>
      </template>
    </vuetable>
    <div>
      <vuetable-pagination-info ref="paginationInfo"
        :css="css.pagination"
        info-class="pull-left"
      ></vuetable-pagination-info>
      <vuetable-pagination ref="pagination"
        :css="css.pagination"
        @vuetable-pagination:change-page="onChangePage"
      ></vuetable-pagination>
    </div>
  </div>
</template>

<script>
import Vuetable from 'vuetable-2/src/components/Vuetable'
import VuetablePagination from 'vuetable-2/src/components/VuetablePagination'
import VuetablePaginationInfo from 'vuetable-2/src/components/VuetablePaginationInfo'
import accounting from 'accounting'
import moment from 'moment'
import Vue from 'vue'
import BootstrapStyle from './bootstrap-css.js'
import FilterBar from './FilterBar'
import VueEvents from 'vue-events'
import VueSweetAlert from 'vue-sweetalert'

Vue.use(VueEvents);
Vue.use(VueSweetAlert);

export default {
  components: {
    Vuetable,
    VuetablePagination,
    VuetablePaginationInfo,
    FilterBar
  },
  data () {
  	return {
      css: BootstrapStyle,
      fields: [
          {
              name: 'id',
              title: '<span class="glyphicon glyphicon-tag"></span> #',
              sortField: 'id'
          },
          {
              name: 'name',
              title: '<span class="glyphicon glyphicon-user"></span> Name',
              sortField: 'name'
          },
          {
              name: 'email',
              title: '<span class="glyphicon glyphicon-envelope"></span> Email Address',
              sortField: 'email'
          },
          {
              name: 'age',
              sortField: 'birthdate',
              dataClass: 'text-center'
          },
          {
              name: 'birthdate',
              title: '<span class="glyphicon glyphicon-gift"></span> Birthdate',
              sortField: 'birthdate',
              titleClass: 'text-center',
              dataClass: 'text-center',
              callback: 'formatDate|DD-MM-YYYY'
          },
          {
              name: 'nickname',
              title: '<span class="glyphicon glyphicon-tag"></span> Nickname',
              sortField: 'nickname',
              callback: 'allcap'
          },
          {
              name: 'gender',
              title: '<span class="glyphicon glyphicon-asterisk"></span> Gender',
              sortField: 'gender',
              titleClass: 'text-center',
              dataClass: 'text-center',
              callback: 'genderLabel'
          },
          {
              name: 'salary',
              title: '<span class="glyphicon glyphicon-usd"></span> Salary',
              sortField: 'salary',
              titleClass: 'text-center',
              dataClass: 'text-right',
              callback: 'formatNumber',
          },
          {
              name: '__slot:actions',
              title: 'Slot Actions',
              titleClass: 'text-center',
              dataClass: 'text-center'
          },
      ],
      sortOrder: [
        {
          field: 'email',
          sortField: 'email',
          direction: 'asc'
        }
      ],
      moreParams: {}
  	}
  },
  mounted () {
    this.$events.listen('filter-set', filterText => this.onFilterSet(filterText))
    this.$events.listen('filter-reset', () => this.onFilterReset())
  },
  methods: {
    renderIcon (classes, options) {
      return `<span class="${classes.join(' ')}"></span>`
    },
    allcap (value) {
      return value.toUpperCase()
    },
    genderLabel (value) {
      return value === 'M'
        ? '<span class="label label-warning"><span class="glyphicon glyphicon-star"></span> Male</span>'
        : '<span class="label label-info"><span class="glyphicon glyphicon-heart"></span> Female</span>'
    },
    formatNumber (value) {
      return accounting.formatNumber(value, 2)
    },
    formatDate (value, fmt = 'D MMM YYYY') {
      return (value == null)
        ? ''
        : moment(value, 'YYYY-MM-DD').format(fmt)
    },
    onPaginationData (paginationData) {
      this.$refs.pagination.setPaginationData(paginationData)
      this.$refs.paginationInfo.setPaginationData(paginationData)
    },
    onChangePage (page) {
      this.$refs.vuetable.changePage(page)
    },
    onAction (action, data, index) {
        console.log('custom-actions: ' + action, data.name, index);
        this.$swal('Sucesso!','Sucesso...','success')
    },
    onFilterSet (filterText) {
      this.moreParams = {
        'filter': filterText
      }
      Vue.nextTick( () => this.$refs.vuetable.refresh())
    },
    onFilterReset () {
      this.moreParams = {}
      this.$refs.vuetable.refresh()
      Vue.nextTick( () => this.$refs.vuetable.refresh())
    }
  },
}
</script>
<style>
.pagination {
  margin-top: 0;
}
.btn.btn-border {
  border: 1px solid;
  margin-right: 2px;
}
.vuetable-pagination-info {
  margin-top: 8px !important;
}
span.sort-icon {
  float: right;
  color: #ff9100;
}
</style>
