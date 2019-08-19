<template>
    <div style="margin-top:50px">
        <div style="margin-top:10px;text-align:center">
            <span>当前记录共{{currentCount}}条</span>
            <span>总记录共{{count}}条</span>
            <div>
                    <a-button @click="show(allList)">ALL</a-button>
                    <a-button @click="show(todoList)">Active</a-button>
                    <a-button @click="show(completedList)">Completed</a-button>
                    <a-button @click="deleteCompleted()"> clear Completed</a-button>
            </div>
            <br/>
            <a-input placeholder="请输入内容"  @keyup.enter="submit"/>
            <!-- <input v-model="todoContent" type="text" placeholder="请输入内容" @keyup.enter="submit"/> -->
            
            <div class="content">
                <div  v-for="todo in showList" style="text-align:left">
                   <input :id="todo.id" type="checkbox" @click="clickCheckBox(todo)" v-model="todo.isFinish" style="width:15px;height:15px"/> 

                   <span style="font-family:'楷体';font-size:24px">{{ todo.text }}</span>

                   <div @click="deleteTodo(todo)" style="float:right;"><img src="../assets/delete.png" style="width:20px;height:20px"/></div>
                </div>
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
input:focus{
    border-color: #66afe9;
    outline: 0;
    -webkit-box-shadow: inset 0 1px 1px rgba(0,0,0,.075),0 0 8px rgba(102,175,233,.6);
    box-shadow: inset 0 1px 1px rgba(0,0,0,.075),0 0 8px rgba(102,175,233,.6)
}
.content{
    width: 40%;
    margin-top:10px;
    margin: 10px auto;
}
.flag{
    border-radius: 15px;
}
.flag:hover{
    background-color: #66afe9;
}
</style>
<script>
export default {
    data(){
        return{
            msg:"TODO MVC",
            count:0,
            currentCount:0,
            completedList:[],
            todoList:[],
            showList:[],
            allList:[],
            todoContent:"",
            state:false,
        }
    },
    methods:{
        submit(){
            if(this.todoContent!=''){
                var todo={"isFinish":false};
                todo['text']=this.todoContent;
                todo['id']=(new Date()).getTime();
                this.allList.push(todo);
                this.todoList.push(todo);
                this.count++;
                this.todoContent='';
                this.show(this.allList);
                this.save();
            }
        },
        clickCheckBox(todo){
            todo.isFinish=!todo.isFinish;
            if(todo.isFinish){
                this.completedList.push(todo);
                this.deleteEleFromArray(this.todoList,todo);
            }else{
                var index=this.completedList.indexOf(todo);
                if(index>-1){
                    this.completedList.splice(index,1);
                }
                this.todoList.push(todo);
            }
            // this.save();
        },
        show(realList){
            this.showList=realList;
            this.currentCount=realList.length;
        },
        deleteTodo(todo){
            this.deleteEleFromArray(this.todoList,todo);
            this.deleteEleFromArray(this.showList,todo);
            this.deleteEleFromArray(this.completedList,todo);
            this.deleteEleFromArray(this.allList,todo);
            this.count--;
            this.currentCount--;
            this.save();
        },
        deleteCompleted(){
            this.completedList.forEach(todo=>{
                this.deleteEleFromArray(this.allList,todo);
                this.count--;
            });
            this.completedList=[];
            this.show(this.completedList);
            this.save();
        },
        deleteEleFromArray(arr,todo){
            if(arr instanceof Array){
                var index=arr.indexOf(todo);
                if(index>-1){
                    arr.splice(index,1);
                }
            }
        },
        save(){
            window.localStorage.setItem("allList",JSON.stringify(this.allList));
            window.localStorage.setItem("todoList",JSON.stringify(this.todoList));
            window.localStorage.setItem("completedList",JSON.stringify(this.completedList)); 
        }
    },
    mounted(){
        var temp=JSON.parse(window.localStorage.getItem("allList"));
        if(temp!=undefined){
            this.allList=temp;
        }
        temp=JSON.parse(window.localStorage.getItem("todoList"));
        if(temp!=undefined){
            this.todoList=temp;
        }
        temp=JSON.parse(window.localStorage.getItem("completedList"));
        if(temp!=undefined){
            this.completedList=temp;
        }
        this.show(this.allList);
        this.currentCount=this.allList.length;
        this.count=this.allList.length;
        
    },
    created(){
        
    }
}
</script>
