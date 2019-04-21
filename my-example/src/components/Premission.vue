<template>
  <div class="todos">
    <p>
      <el-button class="el-icon-minus" @click="menuData()"></el-button>
    </p>
    <el-table :data="todos">
      <el-table-column prop="mname"></el-table-column>
      <el-table-column prop="murl"></el-table-column>
      <el-table-column>
        <template slot-scope="scope">
          <el-button
            size="small"
            type="danger"
            icon="delete"
            @click="delData(scope.$index, todos[scope.$index])"
          >shanchu</el-button>
          <el-button
            size="small"
            type="danger"
            icon="edit"
            @click="editData(scope.$index, todos[scope.$index])"
          >bianji</el-button>
          <el-dialog title="收货地址" :visible.sync="dialogFormVisible">
            <el-form :model="modifyTodo">
              <el-form-item label="菜单名称" :label-width="formLabelWidth">
                <el-input v-model="modifyTodo.name" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="url地址" :label-width="formLabelWidth">
                <el-input v-model="modifyTodo.url" placeholder="请填写url地址"></el-input>
              </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogFormVisible = false">取 消</el-button>
              <el-button type="primary" @click="confirmSend()">确 定</el-button>
            </div>
          </el-dialog>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      todos: [],
      menus: [],
      modifyTodo: {
        alias: "",
        description: "",
        isDefaultRouter: 1,
        name: "",
        url: ""
      },
      dialogFormVisible: false,
      formLabelWidth: "120px"
    };
  },
  methods: {
    menuData() {
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
        url: "http://192.168.0.102:54321/api/v1/rbac/menu/list",
        headers: {
          Authorization: "Bearer " + window.localStorage.getItem("mytoken")
        },
        data: query
      })
        .then(data => {
          if ((data.data.code = 200)) {
            console.log(data);
            this.todos = [];

            var temp = [];
            temp.forEach((curMM, index, qq) => {});
            data.data.data.forEach((element, index, arr) => {
              console.log(element);
              console.log(index);
              console.log(arr);
              var curItem = {
                mname: element.name,
                murl: element.url,
                mguid: element.guid
              };
              temp.push(curItem);
            });

            this.todos = temp;
            this.menus = data.data.data; //用一个变量，把获取的服务端数据都保存起来
          }
        })
        .catch(reason => {
          console.log(reason + "denglushibai");
        });
    },
    delData(index, list) {
      var curCode = this.todos[index].mguid;

      axios({
        method: "get",
        url: "http://192.168.0.102:54321/api/v1/rbac/menu/delete/" + curCode,
        headers: {
          Authorization: "Bearer " + window.localStorage.getItem("mytoken")
        }
      })
        .then(data => {
          if ((data.data.code = 200)) {
            console.log(data);
            this.todos.splice(index, 1);
          }
        })
        .catch(reason => {
          console.log(reason + "denglushibai");
        });
    },
    confirmSend() {
      //保存编辑数据，并发送给后端保存，才去调用webapi
      axios({
        method: "post",
        url: "http://192.168.0.102:54321/api/v1/rbac/menu/edit/",
        headers: {
          Authorization: "Bearer " + window.localStorage.getItem("mytoken")
        },
        data: this.modifyTodo
      })
        .then(data => {
          if ((data.data.code = 200)) {
            console.log(data);
            console.log("更改成功");
          } else {
            console.log(data.data);
            console.log("没有成功");
          }

          this.dialogFormVisible = false;
        })
        .catch(reason => {
          console.log(reason + "denglushibai");

          this.dialogFormVisible = false;
        });
    },
    editData(index, list) {
      //打开对话框
      this.dialogFormVisible = true;
      var curCode = this.menus[index];
      this.modifyTodo = curCode;
    }
  }
};
</script>

<style>
</style>
