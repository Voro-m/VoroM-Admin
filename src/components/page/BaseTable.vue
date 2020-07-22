<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 商品列表
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
                <el-table-column prop="shopTitle" label="商品名称" property="shopTitle" align="center">
                </el-table-column>
                <!-- 商品图片 -->
                <el-table-column label="商品图片" align="center">
                    <template slot-scope="scope">
                        <el-image
                            class="table-td-thumb"
                            :src="scope.row.shopSpuImgUrl"
                            :preview-src-list="[scope.row.shopSpuImgUrl]"
                        ></el-image>
                    </template>
                </el-table-column>
                <!-- 创建时间 -->
                <el-table-column prop="shopOwnSpec" label="商品规格" property="shopOwnSpec" align="center"></el-table-column>
                <el-table-column prop="shopAfterService" label="商品保障" property="shopAfterService" align="center"></el-table-column>
                <el-table-column prop="shopPrice" label="直播价格" property="shopPrice" align="center"></el-table-column>
                <el-table-column prop="shopActivePrice" label="零售价格" property="shopActivePrice" align="center"></el-table-column>
                <el-table-column prop="shopStock" label="商品库存" property="shopStock" align="center"></el-table-column>
                <el-table-column prop="shopShareRate" label="佣金比例" property="shopShareRate" align="center"></el-table-column>
                <el-table-column label="操作" width="180" align="center">
                    <template slot-scope="scope">
                        <el-button
                            type="text"
                            icon="el-icon-edit"
                            @click="handleEdit(scope.row)"
                        >编辑</el-button>
                        <!-- <el-button
                            type="text"
                            icon="el-icon-edit"
                            @click="handleAddSku(scope.row.shopSpuId)"
                        >添加</el-button> -->
                        <el-button
                            type="text"
                            icon="el-icon-delete"
                            class="red"
                            @click="handleDelete(scope.$index, scope.row,scope.row.shopSpuId)"
                        >删除</el-button>
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
                    :total=total>
                </el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑商品" :visible.sync="dialogFormVisible">
            <el-form :model="changeDetail">
                <el-form-item label="商品名称" :label-width="formLabelWidth">
                    <el-input v-model="changeDetail.title" autocomplete="off"></el-input>
                </el-form-item>
                <!-- 商品主图 -->
                <el-form-item label="商品主图" :label-width="formLabelWidth">
                    <!-- <div class="demo-image__lazy" @click="getBannerIndex($event)" @mouseover="mouseOver" @mouseleave="mouseLeave">
                        <el-image v-for="url in banners" :key="url" :src="url" lazy style="width:148px;htight:148px;border:1px dotted #ccc;margin-right:5px;border-radius:2px"></el-image>
                    </div> -->
                     <el-upload
                        action="https://api.yunsav.com/blade-resource/oss/endpoint/upload"
                        list-type="picture-card"
                        :limit="1"
                        :on-success="handleSuccessMain"
                        :on-preview="handlePictureCardPreview"
                        :on-remove="handleRemove">
                        <i class="el-icon-plus"></i>
                    </el-upload>
                    <el-dialog :visible.sync="dialogVisible">
                        <img width="100%" :src="dialogImageUrl" alt="">
                    </el-dialog>
                </el-form-item>
                <el-form-item label="商品规格" :label-width="formLabelWidth">
                    <el-input v-model="changeDetail.shopOwnSpec" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="商品详情" :label-width="formLabelWidth">
                    <div class="demo-image__lazy" @click="getIndex($event)" @mouseover="mouseOver" @mouseleave="mouseLeave">
                        <el-image v-for="url in urls" :key="url" :src="url" lazy style="width:148px;htight:148px;border:1px dotted #ccc;margin-right:5px;border-radius:2px"></el-image>
                    </div>
                     <el-upload
                        action="https://api.yunsav.com/blade-resource/oss/endpoint/upload"
                        list-type="picture-card"
                        :on-success="handleSuccessDetails"
                        :on-preview="handlePictureCardPreview"
                        :on-remove="handleRemove">
                        <i class="el-icon-plus"></i>
                    </el-upload>
                    <el-dialog :visible.sync="dialogVisible">
                        <img width="100%" :src="dialogImageUrl" alt="">
                    </el-dialog>
                </el-form-item>
                <el-form-item label="商品保障" :label-width="formLabelWidth">
                    <el-input v-model="changeDetail.shopAfterService" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="直播价格" :label-width="formLabelWidth">
                    <el-input v-model="changeDetail.price" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="活动价格" :label-width="formLabelWidth">
                    <el-input v-model="changeDetail.activePrice" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="商品库存" :label-width="formLabelWidth">
                    <el-input v-model="changeDetail.shopStock" autocomplete="off" min="1" type="number" @blur="limitCommissionRatioScope"></el-input>
                </el-form-item>
                <el-form-item label="佣金比例" :label-width="formLabelWidth">
                    <el-input v-model="changeDetail.shareRate" autocomplete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="changeSpuDetail()">确 定</el-button>
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
            dialogImageUrl: '',
            dialogVisible: false,
            imgTitleUrl:'',//商品主图
            imgDetailUrl:'',//商品详情
            querInfo:{
                pagenum:1,
                pagesize:10
            },
            total:'',
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
                spuId:'',
                sKuId:'',
                imgUrl:'',
                shareRate:'',
                shopOwnSpec:'',
                price:'',
                activePrice:'',
                shopStock:'',
                shopAfterService:'',
            },
            urlsString:'',
            urls:[],
            // mouserOverTrue:false,
            banners:[],
            bannersString:[],
            bannersLimit:0,
            bannerImgs:[],
        };
    },
    created() {
        // this.getData();
        
    },
    mounted(){
        this.getCateList()
        console.log(this.tableData)
    },
    methods: {
        async getBannerIndex(e){
            const res = await this.banners.splice(this.banners.indexOf(e.target.currentSrc), 1)
        },
        getIndex(e){
            this.urls.splice(this.urls.indexOf(e.target.currentSrc), 1)
        },
        mouseOver(){
            this.mouserOverTrue = true
        },
        mouseLeave(){
            this.mouserOverTrue = false
        },
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
            that.imgTitleUrl += res.data + ','
            console.log(that.imgTitleUrl)
        },
        /**
        * 删除商品主图
        * @method handleRemoveMain
        */
        handleRemove(file, fileList) {
            console.log(file, fileList);
            for(let i = 0; i < fileList.length; i++){
                console.log("输出剩余图片",fileList[i].response.data)
                this.imgTitleUrl += fileList[i].response.data
            }
            console.log(this.imgTitleUrl.length)
        },
        /**
        * 获取商品列表
        * @method getCateList
        */
        async getCateList(){
            const {data : res} = await this.$axios.get('/shopping-mall/spu/businessGoodsList',{
                params:{
                    order:{
                        
                    },
                    current:this.querInfo.pagenum,
                    size:this.querInfo.pagesize
                },
                headers:{
                    "Authorization": "Basic aDU6aDVfd2Vi",
                    "Blade-Auth": "bearer "+sessionStorage.getItem('accessToken')
                }
            })
            console.log("商品列表",res)
            if(res.code != 200) return this.$message.error(res.msg)
            this.total = res.data.total
            console.log("res.records=>",res.data.records)
            for(var key in this.tableData){
                delete this.tableData[key];
            }
            for(let i = 0 ; i < res.data.records.length ; i++){
                let stringWn = res.data.records[i].ownSpec
                let stringLen = res.data.records[i].ownSpec.length
                let stringLenN = res.data.records[i].ownSpec.length - 2
                let stringS = stringWn.slice(7,stringLenN)
                this.urlsString = res.data.records[i].spuInfoImg
                console.log("详情图地址",this.urlsString)
                this.bannersString = res.data.records[i].spuImg
                this.bannerImgs = this.bannersString.split(',');
                console.log("主图地址",this.bannerImgs)
                let thisListObj = {
                    shopTitle:res.data.records[i].spuTitle,
                    shopSpuImgUrl:this.bannerImgs[0],
                    shopPrice:res.data.records[i].price,
                    shopActivePrice:res.data.records[i].activePrice,
                    shopAfterService:res.data.records[i].afterService,
                    shopShareRate:Number(res.data.records[i].shareRate).toFixed(0),
                    shopSpuId:res.data.records[i].spuId,
                    shopSkuId:res.data.records[i].skuId,
                    shopOwnSpec:stringS,
                    shopStock:res.data.records[i].stock,
                    imgInfoUrl:res.data.records[i].spuInfoImg,
                }
                console.log("遍历后的对象=>",thisListObj)
                this.tableData.push(thisListObj)
            }
            console.log(this.tableData)
        },
        /**
        * 获取上传详情图返回结果
        * @method handleSuccessDetails
        */
        handleSuccessDetails(res) {
            console.log(res)
            console.log(res.data)
            this.$message({
                showClose: true,
                message: '图片上传成功',
                type: 'success'
            });
            let that = this;
            that.imgDetailUrl += res.data +','
            console.log(that.imgDetailUrl)
        },
        /**
        * 提交修改信息
        * @method changeSpuDetail
        */
        changeSpuDetail(){
            let $this = this;
            console.log($this.changeDetail)
            if($this.changeDetail.title == ''){
                this.$message({
                    message: '请填写商品标题',
                    type: 'error'
                });
                return false
            }
            if($this.imgTitleUrl == ''){
                this.$message({
                    message: '请上传商品主图',
                    type: 'error'
                });
                return false
            }
            if($this.changeDetail.shopOwnSpec == ''){
                this.$message({
                    message: '请输入商品规格',
                    type: 'error'
                });
                return false
            }
            if($this.changeDetail.shopAfterService == ''){
                this.$message({
                    message: '请填写商品保障',
                    type: 'error'
                });
                return false
            }
            if($this.changeDetail.price == ''){
                this.$message({
                    message: '请输入商品价格',
                    type: 'error'
                });
                return false
            }
            if($this.changeDetail.activePrice == ''){
                this.$message({
                    message: '请输入活动价格',
                    type: 'error'
                });
                return false
            }
            if($this.changeDetail.shareRate == ''){
                this.$message({
                    message: '请输入佣金比例',
                    type: 'error'
                });
                return false
            }
            if(Number($this.changeDetail.shareRate) > 30){
                this.$message({
                    message: '佣金比例不能大于30',
                    type: 'error'
                });
                return false
            }
            if(Number($this.changeDetail.shareRate) < 2){
                this.$message({
                    message: '佣金比例不能小于2',
                    type: 'error'
                });
                return false
            }
            if($this.changeDetail.shopStock == ''){
                this.$message({
                    message: '商品库存不能为空',
                    type: 'error'
                });
                return false
            }
            for(var i = 0 ; i < $this.urls.length ; i++){
                console.log('error=>',$this.urls[i])
                $this.imgDetailUrl += $this.urls[i] + ','
            }
            for(var i = 0 ; i < $this.banners.length ; i++){
                $this.imgTitleUrl += $this.banners[i] + ','
            }
            let data = {
                spu:{
                    "id":$this.changeDetail.spuId,
                    "spuImg":$this.imgTitleUrl,
                    "spuInfoImg":$this.imgDetailUrl,
                    "title":$this.changeDetail.title,
                    "shareRate":$this.changeDetail.shareRate,
                    "id":$this.changeDetail.spuId,
                    "afterService":$this.changeDetail.shopAfterService,
                },
                sku:{
                    "images":$this.imgTitleUrl,
                    "spuInfo":$this.imgDetailUrl,
                    "stock":$this.changeDetail.shopStock,
                    "title":$this.changeDetail.title,
                    "activePrice":$this.changeDetail.activePrice,
                    "price":$this.changeDetail.price,
                    "enable":1,//是否有效，0无效，1有效
                    "id":$this.changeDetail.skuId,
                    "ownSpec":$this.changeDetail.shopOwnSpec

                }
            }
            this.$axios.post('/shopping-mall/spu/updateGoods',data,
            {
                headers: {
                    "Authorization": "Basic aDU6aDVfd2Vi",
                    "Blade-Auth": "bearer "+sessionStorage.getItem('accessToken')
                }
            })
            .then(res=>{
                console.log('res=>',res);
                // this.changeDetail.id = res.data.data.id
                if(res.data.code == 200){
                    this.$message({
                        message: '操作成功',
                        type: 'success'
                    });
                    $this.reload();
                    this.$set(this.tableData, this.idx, this.form);
                }
            })
            this.dialogFormVisible = false
        },

        // 触发搜索按钮
        // handleSearch() {
        //     this.$set(this.query, 'pageIndex', 1);
        //     this.getData();
        // },
        // 删除操作
        handleDelete(index, row,id) {
            let _this = this
            console.log(id)
            // 二次确认删除
            this.$confirm('确定要删除吗？', '提示', {
                type: 'warning'
            })
            .then(() => {
                let $this = this;
                let data = {
                    id:id
                }
                let param = new URLSearchParams();
                param.append("id", id);
                this.$axios.post('/shopping-mall/spu/delGoods',param,{
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
                    $this.reload();
                    this.tableData.splice(index, 1);
                })
                // this.$message.success('删除成功');
               
            })
            .catch(() => {});
        },
        /**
        * 限制佣金比例输入范围
        * @method limitCommissionRatioScope
        */
        limitCommissionRatioScope(){
            let that = this
            // console.log(that.form.commissionRatio)
            if(that.changeDetail.shopStock < Number(1)){
                this.$message.error('商品库存最少为1');
                that.changeDetail.shopStock = 1
            }
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
            // console.log(skuId)
            this.urls = self.imgInfoUrl.split(',');
            var m = [];
            for(var i = 0; i < this.urls.length - 1; i++){
                m.push(this.urls[i]);
            }
            this.urls = m
            console.log("输出图片详情地址数组",this.urls)
            this.bannersString = self.shopSpuImgUrl;
            // console.log("主图地址",this.bannersString)
            this.banners.push(this.bannersString)
            console.log("banners=>",this.banners)

            console.log("查看删除后的数组",this.banners.length)
            if (this.banners.length == 0){
                this.bannersLimit = 1;
            } else {
                this.bannersLimit = 0;
            }
            this.changeDetail.title = self.shopTitle;
            this.changeDetail.shareRate = self.shopShareRate;
            this.changeDetail.shopOwnSpec = self.shopOwnSpec;
            this.changeDetail.activePrice= self.shopActivePrice
            this.changeDetail.spuId = self.shopSpuId;
            this.changeDetail.skuId = self.shopSkuId;
            this.changeDetail.shopStock = self.shopStock;
            this.changeDetail.price = self.shopPrice;
            this.changeDetail.shopAfterService = self.shopAfterService;
            this.dialogFormVisible = true;
        },
        // 添加sku
        // handleAddSku(self){
        //     // console.log(self)
        //     this.$router.push({ path: `/DragList`, query: { spuId: self }})
        // },
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
        handlePictureCardPreview(file) {
            this.dialogImageUrl = file.url;
            this.dialogVisible = true;
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
