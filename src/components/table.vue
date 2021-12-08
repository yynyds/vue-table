<template>
  <div class="container">
    <div class="block_row">
      <div class="btn" @click="getData" :class="{'disabled': loading}">
        <div>Get data</div>
      </div>
    </div>
    <div v-if="loading" style="padding: 20px;font-size: 16px;">loading...</div>
    <div class="block_row" v-if="data">
      <div class="div_table">
        <div class="div_table_row" style="background-color: #f7f7f7;">
          <div class="div_table_col header" @click="sortingByColumn('stock')">Stocks</div>
          <div  class="div_table_col header" @click="sortingByColumn('current')">Current</div>
          <div  class="div_table_col header" @click="sortingByColumn('change')">Change</div>
        </div>
        <div class="div_table_row" v-for="(item, index) in tableData" :key="index">
          <div class="div_table_col">{{item.stock}}</div>
          <div class="div_table_col">{{parseFloat(item.current).toFixed(2)}}</div>
          <div class="div_table_col" :class="item.colorType">{{parseFloat(item.change).toFixed(2)}}</div>
        </div>
      </div>
    </div>
    <div v-show="errorMsg" style="padding: 20px;font-size: 16px;">
      no data. try again
    </div>
  </div>
</template>

<script>

import { payload } from '../mocData/index'
import simulateAsyncReq from '../plugins/getDataFunc'

export default {
 data () {
   return {
     data: null,
     loading: false,
     errorMsg: false,
     sortBy: 'ASC'
   }
 },
  methods: {
    getData () {
      if (!this.loading) {
        this.resetFields()
        simulateAsyncReq(payload).then(res => {
          Object.keys(res).forEach((key) => {
            this.data = res[key].map((item, index) => {
              return {
                stock: res['stocks'][index],
                change: res['change'][index],
                current: res['current'][index],
                colorType: res['change'][index] > 0 ? 'green' : 'red'
              }
            })
          })
          this.data.sort(function (a, b) {
            if (a.stock > b.stock) return 1
            if (b.stock > a.stock) return -1

            return 0
          })
          this.loading = false
        }).catch(() => {
          this.loading = false
          this.errorMsg = true
        })
      }
    },
    resetFields () {
      this.data = null
      this.loading = true
      this.errorMsg = false
    },
    sortingByColumn (column) {
      if (this.sortBy === 'ASC') {
        this.data.sort(function (a, b) {
          if (a[column] > b[column]) return 1
          if (b[column] > a[column]) return -1

          return 0
        })
        this.sortBy = 'DESC'
        return
      }
      if (this.sortBy === 'DESC') {
        this.data.sort(function (a, b) {
          if (a[column] < b[column]) return 1
          if (b[column] < a[column]) return -1

          return 0
        })
        this.sortBy = 'ASC'
        return
      }
    }
  },
  computed: {
   tableData () {
     return this.data
   }
  }
}
</script>

<style scoped lang="scss">
.block_row {
  display: flex;
  justify-content:center;
  flex-wrap:wrap;
}
.btn {
  width: 130px;
  background-color: #f7f7f7;
  border: 1px solid #dedede;
  text-align: center;
  vertical-align: text-top;
  display: table-cell;
  cursor: pointer;
  div {
    font-weight: bold;
    padding: 3px 5px 3px 5px;
  }
}
.btn:hover {
  background-color: #e8e8e8;
}
.btn.disabled {
  color: lightgray;
  background-color: #e8e8e8;
}
.div_table {
  margin-top: 20px;
  display: table;
  width: auto;
  border: 1px solid #dedede;
  border-bottom: none;
}
.div_table_row {
  display: table-row;
  width: auto;
  clear: both;
  .header {
    font-weight: 900;
    cursor: pointer;
  }
}
.div_table_col {
  float: left;
  display: table-column;
  width: 135px;
  padding: 3px 0 3px 0;
  background-color: transparent;
  border-bottom: 1px solid #dedede;
}
.green {
  color: green;
}
.red {
  color: red;
}
</style>