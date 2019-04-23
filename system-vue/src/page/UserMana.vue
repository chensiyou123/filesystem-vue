<template>
  <div v-loading="loading">
    <div style="margin-top: 10px;display: flex;justify-content: center">
      <el-input placeholder="默认展示部分用户，可以通过用户名搜索用户..." prefix-icon="el-icon-search" v-model="keywords" style="width: 400px" size="small"></el-input>
      <el-button type="primary" icon="el-icon-search" size="small" style="margin-left: 3px" @click="searchClick">搜索</el-button>
    </div>
    <div style="display: flex;justify-content: space-around;flex-wrap: wrap">
      <el-card style="width:330px;margin-top: 10px;" v-for="(user,index) in users" :key="index" v-loading="cardloading[index]">
        <div slot="header" style="text-align: left"></div>
        <span>{{user.nickname}}</span>
        <el-button style="float: right; padding: 3px 0;color: #ff0509" type="text" icon="el-icon-delete" @click="deleteUser(user.id)">删除</el-button>
      </el-card>
    </div>
  </div>

</template>

<script>
  import {getRequest} from '../utils/api'
  import {deleteRequest} from '../utils/api'

  export default {
    name: "UserMana",
    data(){
      return{
        loading:false,
        keywords:'',
        users:[],//用户数据
        cardloading: [],//加载
      }
    },
    methods:{
      searchClick(){
        this.loadUserList();
      },
      //查询用户信息
      loadUserList(){
        var vm = this;
        vm.loading = true;
        getRequest("/users/?nickname="+vm.keywords).then(resp=> {
          vm.loading = false;
          if (resp.status == 200) {
            vm.users = resp.data;
          } else {
            vm.$message({type: 'error', message: '数据加载失败!'});
          }
        }, resp=> {
          vm.loading = false;
          if (resp.response.status == 403) {
            var data = resp.response.data;
            vm.$message({type: 'error', message: data});
          }
        });
      },
      //删除用户
      deleteUser(id){
        let vm = this;
        this.$confirm('删除该用户, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          deleteRequest("/users/" + id).then(resp=> {
            if (resp.status == 200 && resp.data.status == 'success') {
              vm.$message({type: 'success', message: '删除成功!'})
              vm.loadUserList();
              return;
            }
          })
        })
      },
    }
  }
</script>

<style scoped>

</style>
