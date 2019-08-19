<template>
<div> 
    <div style="padding-top:2%;text-align:center">
        <div style="margin-top:0.5%" > 
             <a-input v-model="todo" placeholder="请输入内容" @keyup.enter="submit"/> 
             <u-data-list ref="list" :all="allList" :todos="todoList" :completed="completedList"></u-data-list>
        </div> 
        <div >
            <a-radio-group  v-model="defaultValue" style="width:100%">
                <a-badge  :count="allList.length" style="margin-right:3%">
                    <a-radio-button  @click="show(allList)" value="all">ALL</a-radio-button>
                </a-badge>
                <a-badge  :count="todoList.length" style="margin-right:3%">
                    <a-radio-button @click="show(todoList)" value="todo">Active</a-radio-button>
                </a-badge>
                <a-badge  :count="completedList.length" style="margin-right:3%">
                    <a-radio-button @click="show(completedList)" value="complete">Completed</a-radio-button>
                </a-badge>
                <a-badge >
                    <a-radio-button @click="deleteCompleted()" value="clearComplete">Clear Completed</a-radio-button>
                </a-badge>
            </a-radio-group>
        </div>
    </div>
</div>
</template>

<style scoped>
input{
    padding:30px 0px;
    width: 40%;
    outline-style: none;
    border: 1px solid #ccc;
    border-radius: 30px;
    font-size: 30px;
    font-family: "楷体";
}

</style>

<script>
import dataList from "./data.vue";
export default {
    data(){
        return{
            todo:'',
            allList:[],
            todoList:[],
            completedList:[],
            defaultValue:"todo",
        }
    },
    methods:{
        submit(){
            if(this.todo!=''){
                var data={"isFinish":false};
                data['text']=this.todo;
                data['id']=(new Date()).getTime();
                data["isDoubleClick"]=false;
                var index=this.$refs.list.findEleIndexFromArray(this.allList,data);
                if(index>-1){
                    var arr=[];
                    arr.push(this.allList[index]);
                    this.$refs.list.show(arr);
                    return;
                }
                this.allList.push(data);
                this.todoList.push(data);
                this.todo='';
                this.defaultValue="todo";
                this.$refs.list.showList=this.todoList;
            }
        },
        show(arr){
            if(arr instanceof Array){
                this.$refs.list.show(arr);
            }
        },
        deleteCompleted(){
            this.completedList.forEach(todo=>{
                this.$refs.list.deleteEleFromArray(this.allList,todo);
            });
            this.completedList.splice(0,this.completedList.length);
            this.$refs.list.show(this.todoList);
            this.$refs.list.save();
        },
        
    },
    watch:{
      "todo":function(value){
        if(value==''){
            this.$refs.list.showList=this.todoList;
        }
        this.$refs.list.save();
      }
    },
    components:{
        "u-data-list":dataList
    },
    mounted(){
        var temp=window.localStorage.getItem("allList");
        if(temp!=undefined){
            this.allList=JSON.parse(temp);
            console.log(this.allList);
        }
        temp=window.localStorage.getItem("todoList");
        if(temp!=undefined){
            this.todoList=JSON.parse(temp);
             console.log(this.todoList);
        }
        temp=window.localStorage.getItem("completedList");
        if(temp!=undefined){
            this.completedList=JSON.parse(temp);
             console.log(this.completedList);
        }
        this.$refs.list.showList=this.todoList;
    }
}
</script>

