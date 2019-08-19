<template>
<div
  class="demo-infinite-container"
  v-infinite-scroll="handleInfiniteOnLoad"
  :infinite-scroll-disabled="busy"
  :infinite-scroll-distance="10"
  style="width:40%;margin:0.5% auto"
>
  <a-list
    :dataSource="showList"
  >
    <a-list-item :id="item.id" slot="renderItem" slot-scope="item, index" @dblclick="doubleClick(item)" @mouseover="divFocus()" @mouseout="divNotFocus()">
        <a-icon v-if="!item.isDoubleClick" v-show="showDelete" slot="actions" type="delete" @click="deleteTodo(item)"/>
        <a-checkbox v-if="!item.isDoubleClick" @change="onChange(item)" :checked="item.isFinish" style="font-family: '楷体';font-size: 25px;">{{item.text}}</a-checkbox>

        <a-input class="edit" v-if="item.isDoubleClick" v-model="item.text" placeholder="请输入内容" @blur="away(item)"></a-input>
    </a-list-item>
    <div v-if="loading && !busy" class="demo-loading-container">
      <a-spin />
    </div>
  </a-list>
</div>
</template>
<script>

import infiniteScroll from 'vue-infinite-scroll'

export default {
  directives: { infiniteScroll },
  props:{
      all:{
        type:Array,
        required:true
      },
      todos:{
        type:Array,
        required:true
      },
      completed:{
        type:Array,
        required:true
      }
  },
  data () {
    return {
      showList: [],
      loading: false,
      busy: false,
      showDelete:false,
      isDoubleClick:true,
    }
  },
  methods: {
    onChange(item){
      item.isFinish=!item.isFinish;
      if(item.isFinish){
          this.completed.push(item);
          this.deleteEleFromArray(this.todos,item);
      }else{
          this.deleteEleFromArray(this.completed,item);
          this.todos.push(item);
      }
      this.save();
    },
    deleteEleFromArray(arr,ele){
      if(!(arr instanceof Array)){
          return;
      }
      var index=this.findEleIndexFromArray(arr,ele);
      if(index>-1){
        arr.splice(index,1);
      }
    },
    findEleIndexFromArray(arr,ele){
      if(!(arr instanceof Array)){
        return;
      }
      for(var i=0;i<arr.length;i++){
        if(ele.text==arr[i].text){
          return i;
        }
      }
      return -1;
    },
    deleteTodo(item){
      this.deleteEleFromArray(this.all,item);
      this.deleteEleFromArray(this.todos,item);
      this.deleteEleFromArray(this.completed,item);
      this.deleteEleFromArray(this.showList,item);
      this.save();
    },
    save(){
      window.localStorage.setItem("allList",JSON.stringify(this.all));
      window.localStorage.setItem("todoList",JSON.stringify(this.todos));
      window.localStorage.setItem("completedList",JSON.stringify(this.completed));
    },
    show(arr){
      if(arr instanceof Array){
        this.showList=arr;
        // console.log(arr);
      }
    },
    doubleClick(item){
      var index=this.findEleIndexFromArray(this.showList,item);
      item.isDoubleClick=true;
      this.showList.splice(index,1,item);
      this.$nextTick(() => {
        let editInput=document.querySelector(".edit");
        editInput.focus();
      });
     
      /*var ele=document.getElementById(id);
      var childrens=ele.childNodes;
      //保存原有子节点
      var divNode=childrens[0];
      var deleteNode=childrens[1];

      var eleSpan=ele.getElementsByTagName("span");
      var value=eleSpan[2].innerHTML;

      eleSpan[2].innerHTML="";
      var newinput=document.createElement("input");
      newinput.setAttribute("type","text");
      newinput.setAttribute("class","editInput");
      newinput.value=value;
      //移除子节点
      ele.removeChild(childrens[0]);
      ele.removeChild(childrens[0]);
      //添加新节点
      ele.appendChild(newinput);
      newinput.focus=true;
      var _this=this;
      newinput.onblur=function(){
        //恢复原有子节点
        ele.appendChild(divNode);
        ele.appendChild(deleteNode);
        eleSpan[2].innerHTML=newinput.value==value?value:newinput.value;
        _this.updateTodoFromArray(_this.completed,value,newinput.value);
        _this.updateTodoFromArray(_this.all,value,newinput.value);
        _this.updateTodoFromArray(_this.todos,value,newinput.value);
        _this.updateTodoFromArray(_this.showList,value,newinput.value);
        _this.save();
        //移除新增子节点
        ele.removeChild(newinput);*/
       
      // }
    },
    away(item){
      console.log(item.text);
      var index=this.findEleIndexFromArray(this.showList,item);
      item.isDoubleClick=false;
      this.updateTodoFromArray(this.all,item);
      this.updateTodoFromArray(this.completed,item);
      this.updateTodoFromArray(this.todos,item);
      this.updateTodoFromArray(this.showList,item);
      this.showList.splice(index,1,item);
      this.save();
    },
    findEleIndexFromId(arr,item){
      if(!(arr instanceof Array)){
        return;
      }
      for(var i=0;i<arr.length;i++){
        if(item.id==arr[i].id){
          return i;
        }
      }
      return -1;
    },
    updateTodoFromArray(arr,newValue){
       var index=this.findEleIndexFromId(arr,newValue);
       if(index>-1){
          arr.splice(index,1,newValue);
       }
    },
    findTodoFromValue(arr,value){
      if(arr instanceof Array){
        for(var i=0;i<arr.length;i++){
          if(arr[i].text==value){
            return arr[i];
          }
        }
      }
    },
    divFocus(){
      this.showDelete=true;
    },
    divNotFocus(){
      this.showDelete=false;
    },
    fetchData (callback) {
      
    },
    handleInfiniteOnLoad  () {
     
    },
  }
}
</script>
<style>
.demo-infinite-container {
  border-radius: 4px;
  overflow: auto;
  padding: 8px 24px;
  height: 400px;
}
.demo-loading-container {
  position: absolute;
  bottom: 40px;
  width: 100%;
  text-align: center;
}
.editInput{
   padding:10px 0px;
   width: 100%;
   outline-style: none;
   border: 1px solid #ccc;
   border-radius: 5px;
   font-size: 20px;
   font-family: "楷体";
}
</style>
