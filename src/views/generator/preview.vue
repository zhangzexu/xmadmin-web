<template>
  <el-tabs v-if="!error" v-model="activeName" type="card">
    <el-tab-pane v-for="item in data" :key="item.name" :lazy="true" :label="item.name" :name="item.name">
      <Java :value="item.content" :height="height" />
    </el-tab-pane>
  </el-tabs>
  <div v-else class="app-container">
    <el-alert
      :title="error"
      type="error"
    />
  </div>
</template>

<script>
import Java from '@/components/JavaEdit/index'
import { generator } from '@/api/generator/generator'
export default {
  name: 'Preview',
  components: { Java },
  data() {
    return {
      data: null, error: null, height: '', activeName: 'Entity'
    }
  },
  created() {
    this.height = document.documentElement.clientHeight - 180 + 'px'
    const tableName = this.$route.params.tableName
    this.$nextTick(() => {
      generator(tableName, 1).then(data => {
        this.data = data
      }).catch(err => {
        this.error = err.response.data.message
      })
    })
  }
}
</script>
