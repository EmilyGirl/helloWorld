步骤条
<template>
 <el-steps :active="active" finish-status="success" align-center>
              <el-step
                v-for="(item, index) in handle_list"
                :key="index"
                :title="item.title"
                :description="item.content==='null'?'':item.content"
                @click.native="step(item,index)"
              ></el-step>
            </el-steps>
  </template>

  <script>
    export default {
      data() {
        return {
         active:0,
         handle_list:[
             {
                 title:"11",
                 content:'全额委屈额为全额',
                 status:'03'
             },
              {
                 title:"12",
                 content:'全额委屈额为全额',
                 status:'03'
             },
              {
                 title:"13",
                 content:'全额委屈额为全额',
                 status:'03'
             }
             , {
                 title:"14",
                 content:'全额委屈额为全额',
                 status:'03'
             },
              {
                 title:"15",
                 content:'全额委屈额为全额',
                 status:'03'
             },
              {
                 title:"16",
                 content:'全额委屈额为全额',
                 status:'03'
             }
         ]
        }
      },
      methods:{
       step(item,index){
        
       },
       getIndexByValue(value,arr){
            for(let i=0;i<arr.length;i++){
              let item=arr[i]
              let a=-1
              if(item.status===value){
                  a= i
              }
              return a
              
          }
       }
      },
      created(){
          console.log('后他返回步骤数组',this.handle_list)
          let dataArray=this.handle_list
          let index=this.getIndexByValue('02',dataArray)
          console.log('index',index)
          if(index===-1){
            this.active=dataArray.length
          }else{
              this.active=index
          }
          
      }
    }
  </script>

表格
 <template>
    <div>
        <div class='table-box'  :class="{'scaleTable':!showTable} " @click='onShowTable()'>
        <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
        prop="date"
        label="日期"
        width="180">
      </el-table-column>
      <el-table-column
        prop="name"
        label="姓名"
        width="180">
      </el-table-column>
      <el-table-column
        prop="address"
        label="地址">
      </el-table-column>
    </el-table>
    <el-button @click.stop='onCloseTableBtnClick()'>关闭</el-button>
    </div>
            <div class='table-box'  :class="{'scaleTable':!showTable} " @click='onShowTable()'>
        <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
        prop="date"
        label="日期"
        width="180">
      </el-table-column>
      <el-table-column
        prop="name"
        label="姓名"
        width="180">
      </el-table-column>
      <el-table-column
        prop="address"
        label="地址">
      </el-table-column>
    </el-table>
    <el-button @click.stop='onCloseTableBtnClick()'>关闭</el-button>
    </div>
    </div>

  </template>

  <script>
    export default {
      data() {
        return {
          tableData: [{
            date: '2016-05-02',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1518 弄'
          }, {
            date: '2016-05-04',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1517 弄'
          }, {
            date: '2016-05-01',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1519 弄'
          }, {
            date: '2016-05-03',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1516 弄'
          }],
          showTable:true
        }
      },
      methods:{
          onCloseTableBtnClick(){
            this.showTable=false
          },
          onShowTable(){
              this.showTable=!this.showTable
               console.log('hide',this.showTable)
          }
      }
    }
  </script>
  <style>
  
  .table-box{
      width:700px;
      height:300px;
      padding:10px;
      box-shadow: 0 0 4px #ccc;
      cursor: pointer;
      /* transition: all .5s; */
      overflow: hidden;
      box-sizing: border-box;
  }
  .scaleTable{
      width:50px;
      height:50px;
      position: fixed;
      bottom: 20px;
      left:10px;
      border-radius: 100%;
      background-color: #ccc;
      box-shadow: 0 0 4px #ccc;
  }
.scaleTable::after{
    content: '打开';
    display: inline-block;
    width:50px;
    height:50px;
    position: absolute;
    bottom: 0;
    left:0;
    border-radius: 100%;
    background-color: #ccc;
    z-index: 9;
    line-height: 50px;
    text-align: center;
}
  </style>
  
