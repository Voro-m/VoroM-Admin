<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 订单列表
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
           
            <el-table
                :data="tableData"
                border
                class="table"
                ref="multipleTable"
                header-cell-class-name="table-header"
                @selection-change="handleSelectionChange"
            >
                <el-table-column type="selection" width="55" align="center"></el-table-column>
                <!-- 商品名 -->
                <el-table-column prop="shopTitle" label="商品名称" property="shopTitle">
                </el-table-column>
                <!-- 商品图片 -->
                <el-table-column label="商品图片" align="center">
                    <template slot-scope="scope">
                        <el-image
                            class="table-td-thumb"
                            :src="scope.row.shopImg"
                            :preview-src-list="[scope.row.shopImg]"
                        ></el-image>
                    </template>
                </el-table-column>
                <!-- 商品数量 -->
                <el-table-column prop="shopNum" label="商品数量" property="shopNum" align="center"></el-table-column>
                <!-- 下单人姓名 -->
                <el-table-column prop="buyerNick" label="下单人姓名" property="buyerNick" align="center"></el-table-column>
                <!-- 下单人手机号 -->
                <el-table-column prop="receiverMobile" label="下单人手机号" property="receiverMobile" align="center"></el-table-column>
                 <!-- 实付金额 -->
                <el-table-column prop="totalPay" label="实付金额" property="totalPay" align="center"></el-table-column>
                <!-- 买家留言 -->
                <el-table-column prop="buyerMessage" label="买家留言" property="buyerMessage" align="center"></el-table-column>
                <!-- 收件人 -->
                <el-table-column prop="receiverName" label="收件人" property="receiverName" align="center"></el-table-column>
                <!-- 交易状态 -->
                <el-table-column prop="statusName" label="交易状态" property="statusName" align="center"></el-table-column>
                <!-- 下单地址 -->
                <el-table-column prop="receiverAddress" label="收货地址" property="receiverAddress" align="center"></el-table-column>
                <!-- 快递名称 -->
                <el-table-column prop="shipName" label="快递名称" property="shipName" align="center"></el-table-column>
                <!-- 快递单号 -->
                <el-table-column prop="shipCode" label="快递单号" property="shipCode" align="center"></el-table-column>
                <el-table-column label="操作" width="180" align="center">
                    <template slot-scope="scope">
                        <el-button
                            type="text"
                            icon="el-icon-edit"
                            @click="handleEdit(scope.row)"
                        >发货</el-button>
                        <!-- <el-button
                            type="text"
                            icon="el-icon-delete"
                            class="red"
                            @click="handleDelete(scope.$index, scope.row,scope.row.shopSpuId)"
                        >删除</el-button> -->
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination
                    @size-change='handleSizeChange'
                    background
                    @current-change='handleCurrentChange'
                    :current-page ='querInfo.pagenum'
                    :page-sizes='[5,10,15,20,50]'
                    :page-size='querInfo.pagesize'
                    layout='total,sizes,prev,pager,next,jumper'
                    :total='total'>
                </el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑商品" :visible.sync="dialogFormVisible">
            <el-form :model="changeDetail">
                <!-- 商品分类 -->
                <el-form-item label="快递选择" :label-width="formLabelWidth">
                    <el-select v-model="value" placeholder="请选择" value-key="recordsId" @change="currentSel">
                        <el-option
                            v-for="item in options"
                            :key="item.recordsId"
                            :label="item.shipName"
                            :value="item">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="快递单号" :label-width="formLabelWidth">
                    <el-input v-model="orderId" autocomplete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="shippingMerchant()">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
// import { fetchData } from '../../api/index';
export default {
    name: 'basetable',
    inject: ["reload"],
    data() {
        return {
            querInfo:{
                pagenum:1,
                pagesize:10
            },
            total:'',
            orderId:'',
            value:'',
            options:[],
            dialogTableVisible: false,
            dialogFormVisible: false,
            formLabelWidth: '120px',
            query: {
                address: '',
                name: '',
                pageIndex: 1,
                pageSize: 10
            },
            tableData: [],
            pageTotal: 0,
            basicInformation: {
                shopTitle: '', //商品标题
                shopDescribe:'', //商品描述
                commissionRatio:'', //佣金比例
                shopSecurity:'', //商品保障
                dialogImageUrl: '',
                dialogVisible: false,
                disabled: false
            },
            shopForm: {
                title:'',
                commission:''
            },
            changeDetail:{
                title:'',
                id:'',
                imgUrl:'',
                price:'',
                activePrice:'',
            },
            courierList:{
                id:'',//订单号
                orderDetailList:{
                    title:'',//商品标题
                },
                totalPay:'',//用户支付金额
                recipientName:'',//收货人姓名
                receiverState:'',//收货人地址省份
                receiverCity:'',//收货人地址市
                receiverDistrict:'',//收货人地址区
                receiverAddress:'',//收货人详细地址
                receiverMobile:'',//收货人手机号
            },
            couriersList:[],
            Merchant:[],
            MerchantNo:{},
        };
    },
    created() {
        // this.getData();
    },
    mounted(){
        this.getCateList()
    },
    methods: {
        /**
        * 获取上传主图返回结果
        * @method handleSuccessMain
        */
        handleSuccessMain(res) {
            // console.log(res) //上传结果
            this.$message({
                showClose: true,
                message: '图片上传成功',
                type: 'success'
            });
            let that = this;
            that.changeDetail.imgUrl = res.data
            console.log(that.changeDetail.imgUrl)
        },
        /**
        * 删除商品主图
        * @method handleRemoveMain
        */
        handleRemoveMain(file,fileList) {
            console.log(file);
            console.log(fileList)
            let that = this
            that.shopBannerUrl = ''
            console.log(that.shopBannerUrl)

        },
        /**
        * 获取订单列表
        * @method getOrderList
        */
        async getCateList(){
            const {data : res} = await this.$axios.get('/orders/order/businessOrderList',{
                params:{
                    order:{
                        
                    },
                    current:this.querInfo.pagenum,
                    size:this.querInfo.pagesize
                },
                headers:{
                    "Authorization": "Basic aDU6aDVfd2Vi",
                    "Blade-Auth": "bearer " + sessionStorage.getItem('accessToken')
                }
            })
            console.log(res)
            if(res.code != 200) return this.$message.error(res.msg)
            this.total = res.data.total
            console.log("res.records=>",res.data.records)
            for(var key in this.tableData){
                delete this.tableData[key];
            }
            for(let i = 0 ; i < res.data.records.length ; i++){
                let thisListObj = {
                    orderId:res.data.records[i].id, //订单号
                    shopTitle:res.data.records[i].orderDetailList[0].title, //商品标题
                    shopImg:res.data.records[i].orderDetailList[0].image, //商品图片
                    shopNum:res.data.records[i].orderDetailList[0].num, //商品购买数量
                    orderPayNo:res.data.records[i].payNo,//支付单号
                    totalPay:res.data.records[i].totalPay,//支付金额
                    receiverName:res.data.records[i].receiver, //收件人姓名
                    statusName:res.data.records[i].statusName, //订单状态
                    status:res.data.records[i].status,//订单状态码
                    buyerNick:res.data.records[i].receiver, //下单人姓名
                    receiverMobile:res.data.records[i].receiverMobile, //下单人手机号
                    receiverState:res.data.records[i].receiverState, //下单人地址（省）
                    receiverCity:res.data.records[i].receiverCity, //下单人地址（市）
                    receiverDistrict:res.data.records[i].receiverDistrict, //下单人地址（区）
                    receiverAddress:res.data.records[i].receiverState + res.data.records[i].receiverCity + res.data.records[i].receiverDistrict + res.data.records[i].receiverAddress, //下单人地址（详细地址）
                    shipName:res.data.records[i].shippingName,//快递名称
                    shipCode:res.data.records[i].shippingCode,//快递单号
                    buyerMessage:res.data.records[i].buyerMessage,//买家留言
                }
                console.log(thisListObj)
                this.tableData.push(thisListObj)
            }
            console.log(this.tableData)
        },
        /**
        * 提交修改信息
        * @method shippingMerchant
        */
        shippingMerchant(){
            console.log(this.Merchant)
            console.log(this.MerchantNo)
            let $this = this;
            this.$axios.post('/orders/order/businessUpdate',{
                id:$this.Merchant.orderId,
                shippingName:$this.MerchantNo.shipName,
                shipCode:$this.MerchantNo.shipCode,
                shippingCode:$this.orderId,
            },{
                headers:{
                    "Authorization": "Basic aDU6aDVfd2Vi",
                    "Blade-Auth": "bearer "+sessionStorage.getItem('accessToken')
                }
            })
            .then(res=>{
                console.log('res=>',res);
                let $this = this
                if(res.data.code == 200){
                    this.$message({
                        message: '提交成功',
                        type: 'success'
                    });
                    $this.reload();
                }
            })
            this.dialogFormVisible = false
        },
        // 单选选中参数
        currentSel(selVal) {
            // this.code = selVal.recordsId;
            // this.name = selVal.shipName;
            // console.log("选择的name为：" + this.name, "选择的id为:" + this.code,selVal.shipCode);
            console.log(selVal);
            this.MerchantNo = {
                "shipName":selVal.shipName,
                "shipCode":selVal.shipCode
            }
                
        },
        // 触发搜索按钮
        // handleSearch() {
        //     this.$set(this.query, 'pageIndex', 1);
        //     this.getData();
        // },
        // 删除操作
        handleDelete(index, row,id) {
            console.log(id)
            // 二次确认删除
            this.$confirm('确定要删除吗？', '提示', {
                type: 'warning'
            })
            .then(() => {
                let $this = this;
                let data = {
                    ids:id
                }
                let param = new URLSearchParams();
                param.append("ids", id);
                this.$axios.post('/shopping-mall/sku/remove',param,{
                    headers:{
                        "Authorization": "Basic aDU6aDVfd2Vi",
                        "Blade-Auth": "bearer "+sessionStorage.getItem('accessToken')
                    }
                })
                .then(res=>{
                    console.log('res=>',res);
                    if(res.data.code == 200){
                        this.$message({
                            message: '删除成功',
                            type: 'success'
                        });
                    }
                     this.tableData.splice(index, 1);
                })
                // this.$message.success('删除成功');
               
            })
            .catch(() => {});
        },
        // 多选操作
        handleSelectionChange(val) {
            this.multipleSelection = val;
        },
        delAllSelection() {
            const length = this.multipleSelection.length;
            let str = '';
            this.delList = this.delList.concat(this.multipleSelection);
            for (let i = 0; i < length; i++) {
                str += this.multipleSelection[i].name + ' ';
            }
            this.$message.error(`删除了${str}`);
            this.multipleSelection = [];
        },
        // 编辑操作
        handleEdit(self) {
            console.log(self)
            this.Merchant = self
            
            let $this = this;
            // this.changeDetail.id = self
            // 获取快递列表

            let data = {
                "current":'1',//页数
                "size":500,//每页数量
            };
            this.$axios.get('/shopping-mall/shipkd/list',{
                params:{
                  
                },
                headers:{
                    "Authorization": "Basic aDU6aDVfd2Vi",
                    "Blade-Auth": "bearer "+sessionStorage.getItem('accessToken')
                }
            })
            .then(res=>{
                console.log('res=>',res);
                // this.options = res.data.data.records
                console.log(res.data.data.records)
                for(let i = 0 ; i < res.data.data.records.length ; i++){
                    let thisObj = {
                        recordsId:res.data.data.records[i].id,
                        shipCode:res.data.data.records[i].shipCode,
                        shipName:res.data.data.records[i].shipName,
                    }
                    this.options.push(thisObj)
                }
            })
            this.dialogFormVisible = true;
            console.log(this.options)
        },
        // 保存编辑
        saveEdit() {
            this.editVisible = false;
            this.$message.success(`修改第 ${this.idx + 1} 行成功`);
            this.$set(this.tableData, this.idx, this.form);
        },
        // 分页导航
        handlePageChange(val) {
            this.$set(this.query, 'pageIndex', val);
            // this.getData();
        },
        handleSizeChange(val){
            this.querInfo.pagesize = val;
            this.getCateList()
        },
        handleCurrentChange(val){
            this.querInfo.pagenum = val;
            this.getCateList()
        },
    }
};
</script>

<style scoped>
.handle-box {
    margin-bottom: 20px;
}

.handle-select {
    width: 120px;
}

.handle-input {
    width: 300px;
    display: inline-block;
}
.table {
    width: 100%;
    font-size: 14px;
}
.red {
    color: #ff0000;
}
.mr10 {
    margin-right: 10px;
}
.table-td-thumb {
    display: block;
    margin: auto;
    width: 40px;
    height: 40px;
}
</style>
