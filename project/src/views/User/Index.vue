<template>
    <div >
        <head-top></head-top>
        <div class="container">
            <h2 style="margin:20px 0">管理员列表</h2>
            <hr>
            <table>
                <thead>
                    <tr>
                        <th>姓名</th>
                        <th>电话</th>
                        <th>邮箱</th>
                        <th>操作</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item, index) in info" :key="index">
                        <td>{{item.userName}}</td>
                        <td>{{item.phoneNumber}}</td>
                        <td>{{item.email}}</td>
                        <td>
                            <router-link :to="{ path: '/user/detail', query: { id: item.id }}">查看</router-link>
                            <router-link :to="{ path: '/user/edit', query: { id: item.id }}">编辑</router-link>
                            <button @click='del(item.id)'>删除</button>
                        </td>
                    </tr>
                </tbody>
            </table>
            <b-pagination size="md" 
                :total-rows="total"
                prev-text="上一页"
                next-text="下一页"
                align="center"
                @change="getdata"
                hide-goto-end-buttons
                v-model="currentPage"
                :per-page="10" > 
            </b-pagination > 
         <foot-bottom></foot-bottom>
        </div>
    </div>
</template>

<script>
import {mapState} from 'vuex'
import HeadTop from '../../components/header'
import FootBottom from '../../components/footer'
export default {
    data(){
        return {
            info:[],//数据
            currentPage:1, //默认当前页为第一页
            total:10,
            pageNum:1,
            isdel:false
        }
    },
    computed: {
        ...mapState([
            'loginStatus',
            'loginPower'
        ])
    },
    mounted(){
        this.getdata(this.currentPage); 
        this.showloginStatus(); 
    },
    components: {
        HeadTop,
        FootBottom
    },
    methods:{
        showloginStatus(){
            console.log(this.loginStatus);
        },
         getdata(pageNum){
             var that=this;
             that.pageNum=pageNum;
            console.log(111);
            console.log(this.loginStatus);
            var formData=new FormData();
            formData.append('PageSize',10);
            formData.append('PageIndex',pageNum);
            this.axios({
                method: 'POST',
                url: '/User/Index',
                data: formData
            }).then(function(res){
                that.total=res.data.total;
                that.info=res.data.data;
                 that.isdel=false
                // this.RECORD_USERINFO(res.data.data,res.status,1);
            }).catch(function(err){
                if(err.response) {
                    console.log(err.response)
                    //控制台打印错误返回的内容
                }
            })
         },
         del(param){
             var that=this;
            this.axios.get("/User/Delete/"+param).then(function(res){
                console.log(res);
                that.isdel=true;
                // that.getdata(that.pageNum);
            })
         }
     },
     watch: {
        isdel:function(value) {
            if(value)

                this.getdata(this.currentPage); 
        }
    }
}
     

</script>

<style>
    h2{
        font-size:30px;
        font-weight:500;
    }
    table{
        width:100%;
        box-sizing:border-box;
        border-collapse: collapse;
        border-spacing: 0;
        margin-bottom:40px;
    }
    tr{
        height:30px;
        
    }
    th{
        font-size:16px;
        font-weight: bold;
        color:#333;
    }
    th,td{
        text-align:center;
        height:30px;
        line-height:30px;
        padding:8px;
        border-bottom:2px solid rgb(189, 185, 185);
    }
    td a{
        padding: 5px 10px;
        font-size: 12px;
        line-height: 1.5;
        border-radius: 3px;
        border:1px solid gray;
        color:#333;
    }
    td a:hover{
        color: #333;
        background-color: #e6e6e6;
        border-color: #adadad;
    }
    td button{
        padding: 5px 10px;
        font-size: 12px;
        line-height: 1.5;
        border-radius: 3px;
        color:#fff;
        border:1px solid #d9534f;
        background:#d9534f;
        cursor:pointer;
    }
    td button:hover{
        background:#d32923;
        border:1px solid #d32923;
    }
</style>
