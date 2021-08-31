<template>
  <el-tree
    :data="menus"
    :props="defaultProps"
    @node-click="handleNodeClick"
  ></el-tree>
</template>

<script>
//这里可以导入其他的文件 比如：组件 工具js 第三方插件js json文件 图片文件等等
//例如 import 组件名称 from 组件路径
export default {
  //import 引入组件需要引入到对象才能使用
  components: {},
  props: {},
  data() {
    return {
      menus: [],
      defaultProps: {
        children: "child",
        label: "name",
      },
    };
  },
  methods: {
    handleNodeClick(data) {
      console.log(data);
    },
    getmenus() {
      this.$http({
        url: this.$http.adornUrl("/gmallproduct/category/list/tree"),
        method: "get",
      }).then(({ data }) => {
        this.menus= data.data;
        console.log("成功获取到数据",data.data)
      });
    },
  },
  //计算机属性 类似data概念
  computed: {},
  //监控data中的数据变化
  watch: {},
  //创建完成
  created(){
      this.getmenus();
  }
};
</script>

<style>
</style>