<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图 -->
    <el-card class="box-card">
      <!-- 按钮 -->
      <el-row>
        <el-col>
          <el-button type="primary">添加角色</el-button>
        </el-col>
      </el-row>
      <!-- 内容部分 -->
       <el-table
         border
         stripe
        :data="roleList"
        style="width: 100%">
        <!-- 下拉展示 -->
        <el-table-column type="expand">
          <template slot-scope="scope">
            <el-row :class="['bdbottom', i1 === 0 ? 'bdtop' : '','vcenter']" :key="item1.id" v-for="(item1, i1) in scope.row.children">
              <!-- 渲染一级权限 -->
              <el-col  :span="5">
                <el-tag closable @close="removeRightById(scope.row,item1.id)">{{item1.authName}}</el-tag>
                <i class="el-icon-caret-right"></i>
              </el-col>
              <!-- 渲染二级三级权限 -->
              <el-col :span="19">
                <!-- 通过for循环 嵌套渲染二级权限 -->
                <el-row :class="[i2 === 0 ? '' : 'bdtop','vcenter']" v-for="(item2, i2) in item1.children" :key="item2.id">
                  <el-col :span="6">
                    <el-tag type="success" closable @close="removeRightById(scope.row,item2.id)">{{item2.authName}}</el-tag>
                    <i class="el-icon-caret-right"></i>
                  </el-col>
                  <el-col :span="18">
                    <el-tag @close="removeRightById(scope.row,item3.id)" closable v-for="(item3) in item2.children" :key="item3.id" type="warning">{{item3.authName}}</el-tag>
                  </el-col>
                </el-row>
              </el-col>
            </el-row>
          </template>
        </el-table-column>
        <el-table-column label="#" type="index"></el-table-column>
        <el-table-column
          prop="roleName"
          label="角色名称">
        </el-table-column>
         <el-table-column
          prop="roleDesc"
          label="角色描述">
        </el-table-column>
        <el-table-column label="操作" width="300px">
          <template slot-scope="scope">
            <el-button size="mini" type="primary" icon="el-icon-edit">编辑</el-button>
            <el-button size="mini" type="danger" icon="el-icon-delete">删除</el-button>
            <el-button size="mini" type="warning" icon="el-icon-setting" @click="showSetRolesDialog(scope.row)">分配权限</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
    <!-- 分配角色对话框 -->
    <el-dialog
      @closed="clearSetRolesDialog"
      title="分配角色"
      :visible.sync="setRolesDialogVisible"
      width="50%">
      <el-tree
      show-checkbox
      :default-checked-keys="defaultKeys"
      default-expand-all
      node-key="id"
      :data="rights"
      ref="treeRef"
      :props="defaultProps"></el-tree>
      <span slot="footer" class="dialog-footer">
        <el-button @click="setRolesDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="setRolesRight()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data () {
    return {
      roleList: [],
      setRolesDialogVisible: false,
      rights: [],
      defaultKeys: [],
      defaultProps: {
        children: 'children',
        label: 'authName'
      },
      roleId: ''
    }
  },

  created () {
    this.getRolesList()
  },

  methods: {
    // 获取所有角色列表
    async getRolesList () {
      const { data: res } = await this.$http.get('roles')
      if (res.meta.status !== 200) return this.$message.error('获取角色列表失败')
      this.roleList = res.data
      // console.log(res.data)
    },
    // 根据ID删除功能
    async removeRightById (role, rightId) {
      // 根据弹框
      const confirmResult = await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      if (confirmResult !== 'confirm') return this.$message.info('取消删除')
      const { data: res } = await this.$http.delete(`roles/${role.id}/rights/${rightId}`)
      if (res.meta.status !== 200) return this.$message.info('操作失败')
      this.$message.success('取消权限成功')
      // console.log('确认删除')
      role.children = res.data
    },
    showSetRolesDialog (row) {
      this.setRolesDialogVisible = true
      this.getRightsList()
      // 得到所选需要渲染的数组
      this.getAllKeys(row)
    },
    getAllKeys (row) {
      if (!row.children) return this.defaultKeys.push(row.id)
      row.children.forEach(item => {
        this.getAllKeys(item)
      })
      this.roleId = row.id
    },
    // 权限列表获取
    async getRightsList () {
      const { data: res } = await this.$http.get('rights/tree')
      if (res.meta.status !== 200) return this.$message.error('权限列表获取失败')
      this.rights = res.data
      // console.log(this.rights)
    },
    async setRolesRight () {
      const checkedKeys = this.$refs.treeRef.getCheckedKeys()
      const halfCheckedKeys = this.$refs.treeRef.getHalfCheckedKeys()
      const allKeys = [...checkedKeys, ...halfCheckedKeys]
      const { data: res } = await this.$http.post(`roles/${this.roleId}/rights`, {
        rids: allKeys.join(',')
      })
      if (res.meta.status !== 200) return this.$message.error('权限分配失败')
      this.$message.success('权限分配成功')
      this.setRolesDialogVisible = false
      this.getRolesList()
    },
    clearSetRolesDialog () {
      this.defaultKeys = []
    }
  }
}
</script>

<style scoped lang='less'>
.el-tag {
  margin: 7px;
}
.bdtop {
  border-top: 1px solid #eee;
}
.bdbottom {
  border-bottom: 1px solid #eee;
}
.vcenter {
  display: flex;
  align-items: center;
}
</style>
