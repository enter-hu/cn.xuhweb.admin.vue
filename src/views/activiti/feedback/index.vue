<template>
  <div class="execution">
    <basic-container>
      <avue-tabs
        :option="option"
        @change="change"
      >
        <template slot="1">
          <avue-crud
            :page="page"
            :option="item.option"
            :data="tableData"
            :permission="permissionList"
            :table-loading="tableLoading"
            @search-change="searchChange"
            @refresh-change="refreshChange"
            @row-update="handleUpdate"
            @row-save="handleSave"
          >
            <template
              slot="type"
              slot-scope="scope"
            >
              <el-tag>{{scope.label}}</el-tag>
            </template>
          </avue-crud>
        </template>
        <template slot="2">
          <avue-crud
            :page="page"
            :option="item.option"
            :data="tableData"
            :permission="permissionList"
            :table-loading="tableLoading"
            @search-change="searchChange"
            @refresh-change="refreshChange"
            @row-update="handleUpdate"
            @row-save="handleSave"
          >
            <template
              slot="type"
              slot-scope="scope"
            >
              <el-tag>{{scope.label}}</el-tag>
            </template>
          </avue-crud>
        </template>
        <template slot="3">
          <avue-crud
            :page="page"
            :option="item.option"
            :data="tableData"
            :permission="permissionList"
            :table-loading="tableLoading"
            @refresh-change="refreshChange"
            @row-update="handleUpdate"
            @row-save="handleSave"
          >
            <template
              slot="type"
              slot-scope="scope"
            >
              <el-tag>{{scope.label}}</el-tag>
            </template>


          </avue-crud>
        </template>
        <!-- <avue-crud
          ref="crud"
          :page="page"
          :data="tableData"
          :permission="permissionList"
          :table-loading="tableLoading"
          :option="tableOption"
          @on-load="getList"
          @refresh-change="refreshChange"
          @row-update="handleUpdate"
          @row-save="handleSave"
          @row-del="rowDel"
        >
        </avue-crud> -->
      </avue-tabs>
    </basic-container>
  </div>
</template>

<script>
import { fetchList, getObj, addObj, putObj, delObj } from '@/api/activiti/feedback'
import { tableOption } from '@/const/crud/activiti/feedback'
import { mapGetters } from 'vuex'

export default {
  name: 'feedback',
  data() {
    return {
      select: 1,
      item: {},
      tableData: [],
      page: {
        total: 0, // 总页数
        currentPage: 1, // 当前页数
        pageSize: 20 // 每页显示多少条
      },
      tableLoading: false,
      option: tableOption,
    }
  },
  created() {
  },
  mounted: function () {
  },
  computed: {
    ...mapGetters(['permissions']),
    permissionList() {
      return {
        editBtn: this.vaildData(this.permissions.web_feedback_edit, false)
      };
    }
  },
  methods: {
    getList(page, params) {
      this.tableLoading = true
      fetchList(Object.assign({
        current: page.currentPage,
        size: page.pageSize
      }, params)).then(response => {
        this.tableData = response.data.data.records
        this.page.total = response.data.data.total
        this.tableLoading = false
      })
    },
    rowDel: function (row, index) {
      var _this = this
      this.$confirm('是否确认删除ID为' + row.id, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(function () {
        return delObj(row.id)
      }).then(data => {
        _this.tableData.splice(index, 1)
        _this.$message({
          showClose: true,
          message: '删除成功',
          type: 'success'
        })
        this.getList(this.page, { type: this.select })
      }).catch(function (err) {
      })
    },
    /**
     * @title 数据更新
     * @param row 为当前的数据
     * @param index 为当前更新数据的行数
     * @param done 为表单关闭函数
     *
     **/
    handleUpdate: function (row, index, done) {
      putObj(row).then(data => {
        this.tableData.splice(index, 1, Object.assign({}, row))
        this.$message({
          showClose: true,
          message: '修改成功',
          type: 'success'
        })
        done()
        this.getList(this.page, { type: this.select })
      })
    },
    /**
     * @title 数据添加
     * @param row 为当前的数据
     * @param done 为表单关闭函数
     *
     **/
    handleSave: function (row, done) {
      addObj(row).then(data => {
        this.tableData.push(Object.assign({}, row))
        this.$message({
          showClose: true,
          message: '添加成功',
          type: 'success'
        })
        done()
        this.getList(this.page, { type: this.select })
      })
    },
    /**
     * 刷新回调
     */
    refreshChange() {
      this.getList(this.page, { type: this.select })
    },
    change(item) {
      if (item.prop == '1') {
        this.select = 1;
      } else if (item.prop == '2') {
        this.select = 2;
      } else {
        this.select = 3;
      }
      this.refreshChange();
      this.type = item.prop
      this.item = item;
    },
    searchChange(params) {
      this.$message.success('搜索数据' + JSON.stringify(params));
    },
  }
}
</script>

<style lang="scss" scoped>
</style>
