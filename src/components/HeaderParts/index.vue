<template>
  <div class="header">
    <div class="header-cont">
      <div class="menuList">
        <div class="appLogo" @click="routePush('home')">
          <img :src="imgsrc" alt="logo">  
        </div>
        <el-dropdown>
          <span class="el-dropdown-link item-tab" @click="getColumnAll()">
            视频专区<i class="el-icon-arrow-down el-icon--right"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item v-for="(item, index) in typeList" :key="index">
              <div class="dropdown_column" @click="getColumn(item)">{{item.cateName}}</div>
            </el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
        <div class="item-tab" @click="routePush('linepaly')">直播专区</div>
        <el-input 
          :placeholder="searchPlaceholder"
          id="searchtype"
          class="search-type" 
          v-model="searchVal" >
          <i
            class="el-icon-search el-input__icon"
            slot="suffix"
            @click="searchAll(searchVal)">
          </i>
        </el-input>
      </div>
      <div class="control-user">
        <el-dropdown v-if="islogin">
          <span class="el-dropdown-link">
            <el-avatar class="el-avatar" :src="avatarImg" icon="el-icon-user-solid"></el-avatar>
            <i class="el-icon-caret-bottom el-icon--right"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item v-for="(item, index) in userList" :key="index">
              <div class="dropdown_column" @click="routePush(item.pathName)">
                {{item.name}}
              </div>
            </el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
        <div class="loginbtngurop" v-else>
          <span class="sign" @click="signClick('login')">注册登录</span> 
          <!-- <span class="register" @click="signClick('register')">注册</span> -->
        </div>
      </div>
      <Login :login="loginMsg" v-if="visibleState"
        @changeState="getStateVal">
      </Login>
      <PopupWin :popData="userOut" @onFromPopData="outuser" v-if="userOut.showType"></PopupWin>
    </div>
  </div>
</template>

<script>
import Login from "@/components/Login";
import PopupWin from "@/components/PopupWin";
export default {
  name: "header-cont",
  components: {
    Login,
    PopupWin,
  },
  props: [
    'typeList'
  ],
  data() { 
    return {
      searchPlaceholder: '搜索视频',
      userOut: {
        type: 3,
        docs: '确定要退出当前用户?',
        showType: false,
      },
      imgsrc: require('@/assets/img/toSeeBlackLogo.png'),
      avatarImg: '',
      searchVal: '',
      loginMsg: '',
      user: {
        name: "你大爷干啥"
      },
      userList: [
        {name: '个人',pathName: 'personal'},
        {name: '历史',pathName: 'history'},
        {name: '上传',pathName: 'upload'},
        {name: '观众',pathName: 'audience'},
        {name: '兴趣',pathName: 'myhobby'},
        {name: '消息',pathName: 'messages'},
        {name: '退出',pathName: 'outUsers'},
      ],
      islogin: 0, // 用户未登陆状态
      visibleState: false, // 显示登陆弹窗
    }
  },
  mounted() {
    if (!!localStorage.loginUserInfo) {
      this.avatarImg = JSON.parse(localStorage.loginUserInfo).avatar;
      this.islogin = 1;
    }
    else{
      // 用户未登陆
      this.islogin = 0;
    }
  },
  methods: {
    // 用户登录注册弹窗
    signClick(name){
      this.loginMsg = name;
      this.visibleState = true;
    },
    // 路由跳转页面
    routePush(name){
      if (name === "outUsers"){ // 用户退出弹框提示
        this.userOut.showType = true;
      } else {
        this.$router.push({name: name});
      }
    },
    // 登录子组件传值
    getStateVal(val){
      // console.log(val, 776)
      if (val) {
        this.visibleState = val.isShow;
        this.islogin = val.isLogin;
        localStorage.loginUserInfo = JSON.stringify(val.userData);
        this.avatarImg = val.userData.avatar;
      } else {
        // 窗口关闭
        this.visibleState = val;
        // 清空
        // this.loginMsg = '清空';
      }
    },
    // 退出登录子组件传值
    outuser(val){
      console.log(val, '退出');
      this.userOut.showType = val.showType; // 弹窗的显示  
      this.islogin = val.islogin; // 头像的显示
    },
    // 视频全部栏目
    getColumnAll(){
      this.$router.push({name: 'video-zone'});
    },
    // 视频专区栏目单项
    getColumn(typeObj){
      this.$router.push({name: 'video-zone', query: typeObj});
    },
    searchAll(searchVal){
      this.$router.push({name: 'search-info-list', query: {searchVal: searchVal}});
    }
  },
  // watch: {
  //   loginMsg: {
  //     handler(nData, oData){
  //     },
  //     deep:true
  //   }
  // }
};
</script>

<style lang="scss">
  @import "./index.scss";
</style>
