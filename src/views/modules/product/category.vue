<template>
  <el-tree :data="menus" :props="defaultProps" :expand-on-click-node="false" show-checkbox node-key="catId"
           :default-expanded-keys="expanded_k">
    <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            type="text"
            size="mini"
            v-if="node.level<=2"
            @click="() => append(data)">
            Append
          </el-button>
          <el-button
            type="text"
            size="mini"
            v-if="node.childNodes.length==0"
            @click="() => remove(node, data)">
            Delete
          </el-button>
        </span>
      </span>
  </el-tree>
</template>

<script>
  // 这里可以导入其他文件（比如：组件，工具 js，第三方插件 js，json

  // 例如：import 《组件名称》 from '《组件路径》';

  export default {
    // import 引入的组件需要注入到对象中才能使用
    components: {},
    props: {},
    created () {
      this.getMenus()
    },
    data () {
      return {
        expanded_k: [],
        menus: [],
        defaultProps: {
          children: 'children',
          label: 'name'
        }
      }
    },
    methods: {
      append (data) {
        console.log(data)
      },

      remove (node, data) {
        console.log(node, data)
        var ids = [data.catId]
        this.$confirm('是否删除【' + data.name + '】菜单？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/product/category/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            this.$message({
              type: 'success',
              message: '删除成功'
            })
            this.getMenus()
            // 设置父节点默认展开
            this.expanded_k = [node.parent.data.catId]
          })

        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
      },

      getMenus () {
        this.$http({
          url: this.$http.adornUrl('/product/category/list/tree'),
          method: 'get'
        }).then(({data}) => {
          // console.log('获取数据成功：' + data.page)
          this.menus = data.page
        })
      }
    }
  }
</script>
<style scoped>

</style>
