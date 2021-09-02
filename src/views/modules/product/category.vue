<template>
  <div>
    <el-tree
      :data="menus"
      :props="defaultProps"
      :expand-on-click-node="false"
      show-checkbox
      node-key="catId"
      :default-expanded-keys="defaultExpandedKeys"
    >
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

          <el-button type="text" size="mini" @click="() => edit(node, data)"
            >Update</el-button
          >
        </span>
      </span>
    </el-tree>

    <el-dialog :title="titil" :visible.sync="dialogVisible">
      <el-form :model="category">
        <el-form-item label="分类名称">
          <el-input v-model="category.name" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="图标">
          <el-input v-model="category.icon" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="计量单位">
          <el-input
            v-model="category.productUnit"
            autocomplete="off"
          ></el-input>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="appendCategory">确 定</el-button>
      </span>
    </el-dialog>
  </div>
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
      titil: "",
      category: {
        name: "",
        parentCid: "",
        catLevel: "",
        showStatus: "1",
        sort: "0",
        productCount: "0",
        icon: "",
        productUnit: "",
      },
      dialogVisible: false,
      defaultExpandedKeys: [],
      menus: [],
      defaultProps: {
        children: "child",
        label: "name",
      },
    };
  },
  methods: {
    //添加三级分类的方法
    appendCategory() {
      console.log("data", this.category);
      var data = this.category;
      this.$http({
        url: this.$http.adornUrl("/gmallproduct/category/edit"),
        method: "post",
        data: this.$http.adornData(data, false),
      })
        .then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
            });
            this.dialogVisible = false;
            this.getmenus();
            //设置需要默认展开的菜单
            this.defaultExpandedKeys = [node.parent.data.catId];
            this.category;
          } else {
            this.$message.error(data.msg);
          }
        })
        .catch(() => {});
    },
    //修改按钮的方法
    edit(node, data) {
      this.$http({
        url: this.$http.adornUrl("/gmallproduct/category/info/" + data.catId),
        method: "get",
        data: this.$http.adornData(),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.category.name = data.category.name;
          this.category.catId = data.category.catId;
          this.category.parentCid = data.category.parentCid;
          this.category.showStatus = data.category.showStatus;
          this.category.productCount = data.category.productCount;
          this.category.sort = data.category.sort;
          this.category.catLevel = data.category.catLevel;
          this.category.icon = data.category.icon;
          this.category.productUnit = data.category.productUnit;
        }
      });
      this.titil = "添加分类";
      this.dialogVisible = true;
    },
    //添加按钮的方法
    append(data) {
      console.log("append", data);
      this.titil = "添加分类";
      this.dialogVisible = true;
      this.category.name = "";
      this.category.sort = "0";
      this.category.catId = "";
      this.category.showStatus = "1";
      this.category.productCount = "0";
      this.category.icon = "";
      this.category.productUnit = "";
      this.category.parentCid = data.catId;
      this.category.catLevel = data.catLevel * 1 + 1;
    },
    //删除按钮的方法
    remove(node, data) {
      var ids = [data.catId];
      this.$confirm(`确定对【${data.name}】菜单?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          console.log("remove", node, data);
          this.$http({
            url: this.$http.adornUrl("/gmallproduct/category/delete"),
            method: "post",
            data: this.$http.adornData(ids, false),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
              });
              this.getmenus();
              //设置需要默认展开的菜单
              this.defaultExpandedKeys = [node.parent.data.catId];
            } else {
              this.$message.error(data.msg);
            }
          });
        })
        .catch(() => {});
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
    }
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