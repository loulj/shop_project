<template>
  <div class="home">
     <el-container>
        <!-- 页面头部 -->
        <el-header>
          <div class="left">
            <img src="../assets/logo.png">
            <span>前端测试-管理系统</span>
          </div>
          <el-button @click="logout">退出</el-button>
        </el-header>
        <!-- 页面主体 -->
        <el-container>
            <!-- 主体左侧导航栏 -->
            <el-aside :width="collapseFlag?'64px':'200px'" >
                <div class="toggleMenu" @click="collapseFlag=!collapseFlag">|||</div>
                <el-menu :default-active="defalutActive" router unique-opened :collapse="collapseFlag" :collapse-transition="false" background-color="#333744" text-color="#fff" active-text-color="#ffd04b">
                    <!-- 一级导航 -->
                    <el-submenu :index="item.id + ''" :key="item.id" v-for="item in menus">
                      <template slot="title">
                        <i :class="iconsList[item.id]"></i>
                        <span>{{ item.authName }}</span>
                      </template>
                      <!-- 二级导航 -->
                      <el-menu-item :index="'/' + item2.path" v-for="item2 in item.children" :key="item2.id">
                        <i class="el-icon-menu"></i>
                        <span>{{ item2.authName }}</span>
                      </el-menu-item>
                    </el-submenu>
                  </el-menu>
            </el-aside>
            <!-- 主体右侧内容 -->
            <el-main>
              <router-view>
              </router-view>
            </el-main>
        </el-container>
    </el-container>
  </div>
</template>

<script>
export default {
  data () {
    return {
      menus: [],
      iconsList: {
        125: 'iconfont icon-users',
        103: 'iconfont icon-tijikongjian',
        101: 'iconfont icon-shangpin',
        102: 'iconfont icon-danju',
        145: 'iconfont icon-baobiao'
      },
      collapseFlag: false,
      defalutActive: ''
    }
  },

  created () {
    this.getMenus()
    this.defalutActive = this.$route.path
  },

  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('login')
    },
    // 定义获取菜单数据
    async getMenus () {
      const { data: res } = await this.$http.get('menus')
      // console.log(res)
      if (res.meta.status !== 200) return this.$message.error('获取数据失败')
      this.menus = res.data
    }
  }
}
</script>

<style scoped lang='less'>
.el-container,.home{
    height: 100%;
}
.el-header{
    background: #373d41;
    display: flex;
    justify-content: space-between;
    padding: 0 20px;
    align-items: center;
    .left {
      display: flex;
      align-items: center;
      span {
        font-size: 32px;
        color: #fff;
        margin-left: 15px;
      }
    }
}
.el-aside{
    background: #333744;
    height: 100%;
    .iconfont {
      margin-right: 8px;
    }
}
.el-main{
    background: #eee;
    height: 100%;
}
.el-menu {
  border-right: none;
}
.toggleMenu {
      width: 100%;
      height: 40px;
      background: green;
      text-align: center;
      line-height: 40px;
      letter-spacing: 5px; // 文字间距
      cursor: pointer; // 鼠标经过
      user-select: none; // 无法选中文本
    }
</style>
