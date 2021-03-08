<template>
  <el-form ref="form" :model="form" label-width="80px">
    <el-form-item label="项目名称">
      <el-input v-model="form.name"></el-input>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="onSubmit">立即创建</el-button>
      <el-button @click="cancel">取消</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  props: {
    info: {
      type: Object,
      default: () => {
        return {}
      }
    },
    layerid: {
      type: String,
      default: ''
    },
    lydata: {
      type: Object,
      default: () => {
        return {}
      }
    }
  },
  data() {
    return {
      form: {
        name: '',
        region: '',
        date1: '',
        date2: '',
        delivery: false,
        type: [],
        resource: '',
        desc: ''
      }
    }
  },
  methods: {
    onSubmit() {
      this.$layer.msg('提交成功', { icon: 1, time: 1 })
      this.lydata.info.name = this.form.name
      console.log(this.lydata.info.name)
      console.log(this.lydata.info.a)
      this.lydata.info.a = this.lydata.info.name
      console.log(this.lydata.info.a)
      console.log(this.layerid)
      this.$layer.close(this.layerid)
    },
    cancel() {
      this.$layer.close(this.layerid)
    }
  }
}
</script>

<style scoped>
</style>
