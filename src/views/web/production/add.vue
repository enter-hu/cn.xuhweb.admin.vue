<template>
  <div class="execution">
    <basic-container>
      <avue-form
        ref="form"
        v-model="production"
        :option="option"
        @submit="submit"
      >
        <template
          slot-scope="scope"
          slot="content"
        >
          <avue-ueditor
            v-model="production.content"
            :upload="upload"
          ></avue-ueditor>
        </template>

        <template
          slot="prodType"
          slot-scope="scope"
        >
          <avue-crud-select
            v-model="production.prodType"
            placeholder="请选择产品类型"
            :dic="dicData"
            :props="typeProps"
          ></avue-crud-select>
        </template>
        <!-- <template slot="prodTypeForm">

        </template> -->
      </avue-form>
    </basic-container>
  </div>
</template>

<script>
import { addObj, putObj } from '@/api/web/production'
import { tableOption } from '@/const/crud/web/productionAdd'
import { fetchList } from '@/api/web/typeinfo'
import { mapGetters } from 'vuex'

export default {
  name: 'production',
  data() {
    return {
      tableData: [],
      production: {},
      tableOption: tableOption,
      option: tableOption,
      upload: {
        action: "/admin/file/upload/file",
        props: {
          // res: "url", 
          url: "url"
        },
      },
      web_production_add: false,
      page: {
        total: 0, // 总页数
        currentPage: 1, // 当前页数
        pageSize: 30 // 每页显示多少条
      },
      typeProps: {
        label: "title",
        value: 'id'
      },
      dicData: [],
      username: '',
    }
  },
  created() {
    this.web_production_add = this.permissions.web_production_edit;
    this.username = this.userInfo.username;
    this.getList(this.page);
    // this.$set(this.production, 'updateTime', null);
    // this.$set(this.production, 'author', this.username);
    this.id = this.$route.query.id;
    this.getProduction();
  },
  mounted: function () {
  },
  computed: {
    ...mapGetters(['permissions']),
    ...mapGetters(['userInfo']),
  },
  methods: {
    getList(page) {
      this.tableLoading = true
      fetchList(Object.assign({
        current: page.currentPage,
        size: page.pageSize
      }, { type: 1 })).then(response => {
        this.dicData = response.data.data.records;
      })
    },
    /**
     * @title 数据添加
     * @param row 为当前的数据
     * @param done 为表单关闭函数
     *
     **/
    submit() {
      this.$set(this.production, 'updateTime', null);
      this.handleSave(this.production);
    },
    handleSave: function (row, done) {
      addObj(row).then(data => {
        this.tableData.push(Object.assign({}, row))
        this.$message({
          showClose: true,
          message: '添加成功',
          type: 'success'
        })
        done()
        this.getList(this.page)
      })
    },

  }
}
</script>

<style lang="scss" scoped>
</style>
