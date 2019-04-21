<template>
  <div>
    <el-container>
      <el-header class="head">Header</el-header>
      <el-container>
        <el-aside width="200px">Aside</el-aside>
        <el-main>
          <el-row :gutter="20">
            <el-col :span="8">
              <div class="grid-content bg-purple">
                <img src="../assets/img/img.jpg" class="user-avator">
                <div class="user-in">
                  <h1>linfi</h1>
                  <p>普通用户</p>
                </div>
                <div>
                  <p>上次登陆时间：</p>
                  <p>上次登陆地点</p>
                </div>
              </div>
              <div>
                <h2>语言详细</h2>Vue
                <el-progress :percentage="71.3"></el-progress>JavaScript
                <el-progress :percentage="24.1"></el-progress>CSS
                <el-progress :percentage="3.7"></el-progress>HTML
                <el-progress :percentage="0.9"></el-progress>
              </div>
            </el-col>
            <el-col :span="16">
              <div class="grid-content bg-purple">
                <el-col :span="8">
                  <div class="grid-content bg-purple">
                    <i class="el-icon-location"></i>
                    <span class="number">1234</span>
                    <p>用户访问量</p>
                  </div>
                </el-col>
                <el-col :span="8">
                  <div class="grid-content bg-purple-light">
                    <i class="el-icon-location"></i>
                    <span class="number-one">134</span>
                    <p>系统消息</p>
                  </div>
                </el-col>
                <el-col :span="8">
                  <div class="grid-content bg-purple">
                    <i class="el-icon-location"></i>
                    <span class="number-two">51234</span>
                    <p>数量</p>
                  </div>
                </el-col>
              </div>
              <!-- <el-checkbox v-for="checkedtip in checkedtips" :key="title">{{checkedtip.title}}</el-checkbox> -->
              <div>
                <el-button @click="getDataFromWebApi()">dianwo</el-button>
                <el-table :data="tobinddata" border style="width: 100%" @on-delete>
                  <!-- <el-table-column fixed="left">
                    <template slot-scope="scope">
                      <el-checkbox v-bind:class="active" width="10px"></el-checkbox>
                    </template>
                  </el-table-column>-->
                  <el-table-column prop="name" width="600px"></el-table-column>
                  <el-table-column prop="age" width="600px"></el-table-column>
                  <el-table-column fixed="right" label="tianjia" width="100">
                    <template slot-scope="scope">
                      <el-button
                        @click.native.prevent="deleteItem(scope.$index, tobinddata[scope.$index])"
                        type="danger"
                        size="small"
                        icon="el-icon-delete"
                        circle
                      ></el-button>
                    </template>
                  </el-table-column>
                </el-table>
              </div>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="12">
              <schart
                :canvasId="canvasId"
                :type="type"
                :width="width"
                :height="height"
                :data="data"
                :options="options"
              ></schart>
            </el-col>
            <el-col :span="12">
              <schart
                :canvasId="canvasId"
                :type="type"
                :width="width"
                :height="height"
                :data="data"
                :options="options2"
              ></schart>
            </el-col>
          </el-row>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import Schart from "vue-schart";
import axios from "axios";
export default {
  data() {
    return {
      tobinddata: [],
      active: true,
      checkedtips: [
        {
          title: "今天要修复100个bug",
          status: false
        },
        {
          title: "今天要修复100个bug",
          status: false
        },
        {
          title: "今天要写100行代码加几个bug吧",
          status: false
        },
        {
          title: "今天要修复100个bug",
          status: false
        },
        {
          title: "今天要修复100个bug",
          status: true
        },
        {
          title: "今天要写100行代码加几个bug吧",
          status: true
        }
      ],
      canvasId: "myCanvas",
      type: "bar",
      width: 500,
      height: 400,
      data: [
        { name: "2014", value: 1342 },
        { name: "2015", value: 2123 },
        { name: "2016", value: 1654 },
        { name: "2017", value: 1795 }
      ],
      options: {
        title: "Total sales of stores in recent years"
      },
      canvasId: "myCanvas",
      type: "bar",
      width: 500,
      height: 400,
      data: [
        { name: "2014", value: 1342 },
        { name: "2015", value: 2123 },
        { name: "2016", value: 1654 },
        { name: "2017", value: 1795 }
      ],
      options2: {
        title: "Total sales of stores in recent years"
      }
    };
  },
  components: {
    Schart
  },
  methods: {
    getDataFromWebApi() {
      var query = {
        totalCount: 0,
        pageSize: 20,
        currentPage: 1,
        kw: "",
        isDeleted: 0,
        status: -1,
        menuGuid: "",
        menuName: "请选择...",
        sort: [
          {
            direct: "DESC",
            field: "CreatedOn"
          }
        ]
      };
      axios({
        method: "post",
        url: "http://192.168.0.102:54321/api/v1/rbac/permission/list",
        headers: {
          Authorization: "Bearer " + window.localStorage.getItem("mytoken")
        },
        data: query
      })
        .then(data => {
          console.log(data);
          if (data.data.code === 200) {
            console.log(data.data);
            this.tobinddata = [];
            var res = data.data.data;

            var temp = [];
            res.forEach(element => {
              var curItem = {
                name: element.menuName,
                age: element.name,
                item: element
              };
              temp.push(curItem);
            });

            this.tobinddata = temp;
          } else {
            console.log("caozuoshibai");
          }
        })
        .catch(function(reason) {
          console.log("shibai" + reason);
        });
    },
    deleteItem(index, list) {
      var curId = this.tobinddata[index].item.code;
      var curCode = list.item.code;
      var query = {
        totalCount: 0,
        pageSize: 20,
        currentPage: 1,
        kw: "",
        isDeleted: 0,
        status: -1,
        menuGuid: "",
        menuName: "请选择...",
        sort: [
          {
            direct: "DESC",
            field: "CreatedOn"
          }
        ]
      };
      axios({
        method: "get",
        url:
          "http://192.168.0.102:54321/api/v1/rbac/permission/delete/" + curCode,
        headers: {
          Authorization: "Bearer " + window.localStorage.getItem("mytoken")
        }
      })
        .then(data => {
          console.log(data);
          if (data.data.code === 200) {
            console.log(data.data);
            this.tobinddata.splice(index, 1);
          } else {
            console.log("caozuoshibai");
          }
        })
        .catch(function(reason) {
          console.log("shibai" + reason);
        });
    }
  }
};
</script>

<style></style>
