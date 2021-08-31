<template>
  <el-tree
    :data="menus"
    :props="defaultProps"
    :expand-on-click-node="false"
    show-checkbox
    node-key="catId"
    :default-expanded-keys="defaultExpandedKeys"
  >
    <span class="custom-tree-node" slot-scope="{ node, data }">
      <span>{{ node.label }}</span>
      <span>
        <el-button
          v-if="node.level <= 2"
          type="text"
          size="mini"
          @click="() => append(data)"
          >Append</el-button
        >
        <el-button
          v-if="node.childNodes.length == 0"
          type="text"
          size="mini"
          @click="() => remove(node, data)"
          >Delete</el-button
        >
      </span>
    </span>
  </el-tree>
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
      defaultExpandedKeys: [],
      menus: [],
      defaultProps: {
        children: "child",
        label: "name",
      },
    };
  },
  methods: {
    //添加按钮的方法
    append(data) {
      console.log("append", data);
    },
    //删除按钮的方法
    remove(node, data) {
      var ids = [data.catId];
      this.$confirm(`确定对【${data.name}】菜单?`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      ).then(() => {
        console.log("remove", node, data);
        this.$http({
          url: this.$http.adornUrl("/gmallproduct/category/delete"),
          method: "post",
          data: this.$http.adornData(ids, false),
        })
          .then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.getmenus();
                  //设置需要默认展开的菜单
                  this.defaultExpandedKeys=[node.parent.data.catId]
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          })
      }).catch(() => {});;
    },
    //获取菜单
    getmenus() {
      this.$http({
        url: this.$http.adornUrl("/gmallproduct/category/list/tree"),
        method: "get",
      }).then(({ data }) => {
        this.menus = data.data;
        console.log("成功获取到数据", data.data);
      });
    },
  },
  //计算机属性 类似data概念
  computed: {},
  //监控data中的数据变化
  watch: {},
  //创建完成
  created() {
    this.getmenus();
  },
};
</script>

<style>
</style>