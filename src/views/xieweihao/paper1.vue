<template>
  <div class="app-container">
    <!-- Note that row-key is necessary to get a correct row order. -->
    <el-input placeholder="请输入关键字搜索" style="width:280px;" prefix-icon="el-icon-document" />
    <el-button :loading="downloadLoading" style="margin-bottom:20px;" type="primary" icon="el-icon-document">
      搜索
    </el-button>
    <el-table ref="dragTable" v-loading="listLoading" :data="list" row-key="id" border fit highlight-current-row style="width: 100%">
      <el-table-column align="center" label="序号" width="65">
        <template slot-scope="{row}">
          <span>{{ row.id.counter }}</span>
        </template>
      </el-table-column>

      <el-table-column width="180px" align="center" label="论文出版时间">
        <template slot-scope="{row}">
          <!-- <span>{{ row.timestamp | parseTime('{y}-{m}-{d} {h}:{i}') }}</span> -->
          <span>{{ row.date }}</span>
        </template>
      </el-table-column>

      <el-table-column min-width="300px" label="论文题目">
        <template slot-scope="{row}">
          <span>{{ row.title }}</span>
        </template>
      </el-table-column>

      <el-table-column width="110px" align="center" label="作者">
        <template slot-scope="{row}">
          <span>{{ row.author }}</span>
        </template>
      </el-table-column>

      <!-- <el-table-column width="100px" label="Importance">
        <template slot-scope="{row}">
          <svg-icon v-for="n in + row.importance" :key="n" icon-class="star" class="icon-star" />
        </template>
      </el-table-column> -->

      <!-- <el-table-column align="center" label="Readings" width="95">
        <template slot-scope="{row}">
          <span>{{ row.pageviews }}</span>
        </template>
      </el-table-column> -->

      <el-table-column class-name="status-col" label="标注情况" width="110">
        <template slot-scope="{row}">
          <el-tag :type="row.is_marked | statusFilter">
            <span v-if="row.is_marked == true">已标注</span>
            <span v-else>未标注</span>
          </el-tag>
        </template>
      </el-table-column>

      <el-table-column align="center" label="操作" width="120">
        <span>
          <el-button type="success" style="width:100px;">开始操作</el-button>
        </span>
      </el-table-column>
    </el-table>
    <!-- <div class="show-d">
      <el-tag>The default order :</el-tag> {{ oldList }}
    </div>
    <div class="show-d">
      <el-tag>The after dragging order :</el-tag> {{ newList }}
    </div> -->
  </div>
</template>

<script>
import { fetchList } from '@/api/article'
import Sortable from 'sortablejs'
import axios from 'axios'

export default {
  name: 'DragTable',
  filters: {
    statusFilter(status) {
      const statusMap = {
        true: 'success',
        false: 'info'
        // false: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
      count: 1,
      list: null,
      total: null,
      listLoading: true,
      listQuery: {
        page: 1,
        limit: 10
      },
      sortable: null,
      oldList: [],
      newList: []
    }
  },
  created() {
    // this.getList()
    this.getReference()
  },
  methods: {
    async getList() {
      this.listLoading = true
      const { data } = await fetchList(this.listQuery)
      this.list = data.items
      console.log(777777777)// test
      console.log(this.list)// test
      // this.total = data.total
      this.listLoading = false
      this.oldList = this.list.map(v => v.id)
      this.newList = this.oldList.slice()
      this.$nextTick(() => {
        this.setSort()
      })
    },
    getReference() {
      const url = 'http://localhost:10088/reference/getAll'
      this.listLoading = true
      axios.get(url)
        .then((response) => {
          console.log(response)
          const { data } = response
          this.list = data
          console.log(888888)// test
          console.log(this.list)// test
          this.listLoading = false
          this.oldList = this.list.map(v => v.id)
          this.newList = this.oldList.slice()
          this.$nextTick(() => {
            this.setSort()
          })
        })
    },
    setSort() {
      const el = this.$refs.dragTable.$el.querySelectorAll('.el-table__body-wrapper > table > tbody')[0]
      this.sortable = Sortable.create(el, {
        ghostClass: 'sortable-ghost', // Class name for the drop placeholder,
        setData: function(dataTransfer) {
          // to avoid Firefox bug
          // Detail see : https://github.com/RubaXa/Sortable/issues/1012
          dataTransfer.setData('Text', '')
        },
        onEnd: evt => {
          const targetRow = this.list.splice(evt.oldIndex, 1)[0]
          this.list.splice(evt.newIndex, 0, targetRow)

          // for show the changes, you can delete in you code
          const tempIndex = this.newList.splice(evt.oldIndex, 1)[0]
          this.newList.splice(evt.newIndex, 0, tempIndex)
        }
      })
    }
  }
}
</script>

<style>
.sortable-ghost{
  opacity: .8;
  color: #fff!important;
  background: #42b983!important;
}
</style>

<style scoped>
.icon-star{
  margin-right:2px;
}
.drag-handler{
  width: 20px;
  height: 20px;
  cursor: pointer;
}
.show-d{
  margin-top: 15px;
}
</style>
