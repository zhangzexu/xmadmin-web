<template>
  <div class="app-container">
    <Search :query="query" />
    <!--表格渲染-->
    <el-table v-loading="loading" :data="data" style="width: 100%;">
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="请求方法">
              <span>{{ props.row.method }}</span>
            </el-form-item>
            <el-form-item label="请求参数">
              <span>{{ props.row.params }}</span>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column prop="username" label="用户名" />
      <el-table-column prop="requestIp" label="IP" />
      <el-table-column :show-overflow-tooltip="true" prop="address" label="IP来源" />
      <el-table-column prop="description" label="描述" />
      <el-table-column prop="browser" label="浏览器" />
      <el-table-column prop="createTime" label="创建日期">
        <template slot-scope="scope">
          <span>{{ parseTime(scope.row.createTime) }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="createTime" label="异常详情" width="100px">
        <template slot-scope="scope">
          <el-button size="mini" type="text" @click="info(scope.row.id)">查看详情</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog :visible.sync="dialog" title="异常详情" append-to-body top="30px" width="85%">
      <pre v-highlightjs="errorInfo"><code class="java" /></pre>
    </el-dialog>
    <!--分页组件-->
    <el-pagination
      :total="total"
      :current-page="page + 1"
      style="margin-top: 8px;"
      layout="total, prev, pager, next, sizes"
      @size-change="sizeChange"
      @current-change="pageChange"
    />
  </div>
</template>

<script>
import crud from '@/mixins/crud'
import { getErrDetail } from '@/api/monitor/log'
import Search from './search'
export default {
  name: 'ErrorLog',
  components: { Search },
  mixins: [crud],
  data() {
    return {
      errorInfo: '', dialog: false
    }
  },
  created() {
    this.$nextTick(() => {
      this.init()
    })
  },
  methods: {
    // 获取数据前设置好接口地址
    beforeInit() {
      this.url = 'api/logs/error'
      this.params['logType'] = 'ERROR'
      return true
    },
    // 获取异常详情
    info(id) {
      this.dialog = true
      getErrDetail(id).then(res => {
        this.errorInfo = res.exception
      })
    }
  }
}
</script>

<style scoped>
  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 70px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 100%;
  }
  .demo-table-expand .el-form-item__content {
    font-size: 12px;

  }
  /deep/ .el-dialog__body{
    padding: 0 20px 10px 20px !important;
  }
  .java.hljs{
    color: #444;
    background: #ffffff !important;
    height: 630px !important;
  }
</style>
