<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-calendar"></i> 添加商品
                </el-breadcrumb-item>
                <el-breadcrumb-item>商品管理 </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <el-header>基本信息</el-header>
            <div class="basicInformation-box">
                <el-form ref="form" :model="basicInformation" label-width="80px">
                    <!-- 商品分类 -->
                    <el-form-item label="商品分类">
                        <el-select v-model="optionsValue" placeholder="请选择" value-key="id" @change="currentSel">
                            <el-option
                                v-for="item in optionslist"
                                :key="item.id"
                                :label="item.categoryName"
                                :value="item">
                            </el-option>
                        </el-select>
                    </el-form-item>
                    <!-- 商品主图 -->
                    <el-form-item label="商品主图">
                        <el-upload
                            action="https://api.yunsav.com/blade-resource/oss/endpoint/upload"
                            list-type="picture-card"
                            :limit="1"
                            accept="image/*"
                            :before-upload="beforeAvatarUpload"
                            :on-success="handleSuccessMain"
                            :on-preview="handlePictureCardPreview"
                            :on-remove="handleRemoveMain">
                            <i class="el-icon-plus"></i>
                        </el-upload>
                        <el-dialog :visible.sync="dialogVisible">
                            <img width="100%" :src="dialogImageUrl" alt="">
                        </el-dialog>
                    </el-form-item>
                    <el-dialog :visible.sync="basicInformation.dialogVisible">
                        <img width="100%" :src="basicInformation.dialogImageUrl" alt="">
                    </el-dialog>
                    <!-- 商品标题 -->
                    <el-form-item label="商品标题">
                        <el-input v-model="basicInformation.shopTitle" placeholder="请输入商品标题"></el-input>
                    </el-form-item>
                    <!-- 商品描述 -->
                    <el-form-item label="商品规格">
                        <el-input v-model="basicInformation.shopDescribe" placeholder="请输入商品规格"></el-input>
                    </el-form-item>
                    <!-- 商品详情图 -->
                    <el-form-item label="商品详情">
                        <el-upload
                            action="https://api.yunsav.com/blade-resource/oss/endpoint/upload"
                            list-type="picture-card"
                            accept="image/*"
                            :before-upload="beforeAvatarUpload"
                            :on-success="handleSuccessDetails"
                            :on-preview="handlePictureCardPreview"
                            :on-remove="handleRemove">
                            <i class="el-icon-plus"></i>
                        </el-upload>
                        <el-dialog :visible.sync="dialogVisible">
                            <img width="100%" :src="dialogImageUrl" alt="">
                        </el-dialog>
                    </el-form-item>
                    <el-dialog :visible.sync="basicInformation.dialogVisible">
                        <img width="100%" :src="basicInformation.dialogImageUrl" alt="">
                    </el-dialog>
                     <!-- 商品保障 -->
                    <el-form-item label="商品保障">
                        <el-input type="textarea" rows="5" v-model="basicInformation.shopSecurity" placeholder="请输入商品保障"></el-input>
                    </el-form-item>
                </el-form>
            </div>
            <!-- 物流信息 -->
            <!-- <el-header>物流信息</el-header>
            <div class="logisticsInformation-box">
                <el-form ref="form" :model="basicInformation" label-width="80px">
                    <el-form-item label="配送方式">
                        <el-checkbox-group v-model="logisticsInformation.checkList">
                            <el-checkbox label="快递发货" disabled></el-checkbox>
                            <el-checkbox label="同城配送" disabled></el-checkbox>
                            <el-checkbox label="到店自提" disabled></el-checkbox>
                        </el-checkbox-group>
                    </el-form-item>
                    <el-form-item label="商品运费">
                        <el-input placeholder="请输入商品运费" v-model="logisticsInformation.shopDescribe" type="number" min="0" readonly>
                            <template slot="prepend">￥</template>
                        </el-input>
                    </el-form-item>
                </el-form>
            </div> -->
            <el-header>商品库存</el-header>
            <div class="logisticsInformation-box">
                <el-form ref="form" :model="logisticsInformation" label-width="80px">
                    <el-form-item label="商品库存">
                        <el-input placeholder="请输入商品库存" v-model="logisticsInformation.stock" type="number" min="0">
                            <template slot="prepend">件</template>
                        </el-input>
                    </el-form-item>
                </el-form>
            </div>
            <!-- 价格信息 -->
            <el-header>价格信息</el-header>
            <div class="logisticsInformation-box">
                <el-form ref="form" :model="priceInformation" label-width="80px">
                    
                    <el-form-item label="零售价格">
                        <el-input placeholder="请输入商品零售价" v-model="priceInformation.shopRetailPrice" type="number" min="0">
                            <template slot="prepend">￥</template>
                        </el-input>
                    </el-form-item>
                  
                    <el-form-item label="直播价格">
                        <el-input placeholder="请输入商品直播价" v-model="priceInformation.shopLivePrice" type="number" min="0">
                            <template slot="prepend">￥</template>
                        </el-input>
                    </el-form-item>
                </el-form>
            </div>
            <!-- 佣金信息 -->
            <el-header>
                佣金信息
                <el-popover
                    placement="top-start"
                    width="300"
                    trigger="hover"
                    content="输入2-30的佣金比例范围">
                <el-button type="text" slot="reference"><i class="el-icon-question"></i></el-button>
                </el-popover>
            </el-header>
            <div class="logisticsInformation-box">
                <el-form ref="form" :model="commissionInformation" label-width="80px">
                    <!-- 佣金比例 -->
                    <el-form-item label="佣金比例">
                        <el-input
                            placeholder="请输入商品佣金比例"
                            v-model="commissionInformation.commissionRatio"
                            type="number"
                            max="30"
                            min="2"
                            @blur="limitCommissionRatioScope"
                            @keyup.native="getKeyboardVal($event)"
                        >
                            <template slot="prepend">%</template>
                        </el-input>
                    </el-form-item>
                     <!-- 提交表单 -->
                    <el-form-item>
                        <el-button type="primary" @click="onSubmit">表单提交</el-button>
                        <el-button>取消</el-button>
                    </el-form-item>
                </el-form>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'baseform',
    inject: ["reload"],
    data() {
        return {
            fileList: [],
            dialogImageUrl: '',
            dialogVisible: false,
            imgTitleUrl:'',//商品主图
            imgDetailUrl:'',//商品详情图片
            basicInformation: {
                shopTitle: '', //商品标题
                shopDescribe:'', //商品描述
                commissionRatio:'', //佣金比例
                shopSecurity:'', //商品保障
                dialogImageUrl: '',
                dialogVisible: false,
                disabled: false
            },
            logisticsInformation: {
                shopDescribe:'', //商品运费
                checkList:['快递发货'], //配送方式选择
                stock:'', //商品库存
            },
            priceInformation: {
                shopRetailPrice:'', //零售价
                shopLivePrice:'', //直播价
            },
            commissionInformation: {
                commissionRatio:'', //商品佣金
            },
            shopBannerUrl:'', //商品主图地址
            shopDetailsUrl:'', //商品详情地址
            optionslist:[],//商品分类选择
            optionsValue:'',
            categoryId:'',
        };
    },
    mounted(){
        this.getShopCategory()
    },
    methods: {
        onSubmit() {
            console.log(this.imgTitleUrl)
            if(this.basicInformation.shopTitle == ''){
                this.$message({
                    showClose: true,
                    message: '请输入商品名称',
                    type: 'error'
                });
            }else {
                if(this.imgTitleUrl == ''){
                    this.$message({
                        showClose: true,
                        message: '请上传商品主图',
                        type: 'error'
                    });
                } else {
                    let $this = this;
                    let data = {
                        spu:{
                            "brandId":0,
                            "cid1": 0,
                            "cid2": 0,
                            "cid3": 0,
                            "videoCategoryId":$this.categoryId,//分类id
                            "saleable":1,//是否上架 1上架 0下架
                            "valid":1,//是否有效  0已删除，1有效
                            "spuImg":$this.imgTitleUrl,//商品主图
                            "spuInfoImg":$this.imgDetailUrl,//商品详情
                            "title":$this.basicInformation.shopTitle,//商品标题
                            "shareRate":$this.commissionInformation.commissionRatio,//商品佣金比例
                            "afterService":$this.basicInformation.shopSecurity
                        },
                        sku:{
                            "ownSpec":$this.basicInformation.shopDescribe, //商品规格
                            "stock":$this.logisticsInformation.stock, //商品库存
                            "enable":1, //	是否有效 0无效 1有效
                            "spuShow":1, //	是否有效 0无效 1有效
                            "images":$this.imgTitleUrl,//商品主图
                            "spuInfo":$this.imgDetailUrl,//商品详情
                            "title":$this.basicInformation.shopTitle,//商品标题
                            "activePrice":$this.priceInformation.shopRetailPrice,//活动展示价
                            "price":$this.priceInformation.shopLivePrice,//售卖价
                        }
                       

                    };
                    this.$axios.post('/shopping-mall/spu/saveGoods',data,
                    {
                        headers: {
                            "Authorization": "Basic aDU6aDVfd2Vi",
                            "Blade-Auth": "bearer "+sessionStorage.getItem('accessToken')
                        }
                    })
                    .then(res=>{
                        console.log('res=>',res);
                        if(res.data.code == 200){
                            this.$message({
                                showClose: true,
                                message: '上传成功',
                                type: 'success'
                            });
                            $this.reload();
                            this.$router.push({ path: `/BaseTable`})
                        }     
                    })
                }
            }
        },
        /**
        * 获取上传主图返回结果
        * @method handleSuccessMain
        */
        handleSuccessMain(res) {
            console.log("上传结果=>",res) //上传结果
            if(res.code == 200){
                this.$message({
                    showClose: true,
                    message: res.msg,
                    type: 'success'
                });
                let _this = this;
                _this.imgTitleUrl = res.data
            } else {
                this.$message({
                    showClose: true,
                    message: res.msg,
                    type: 'error'
                });
            }
        },
        /**
        * 删除商品主图
        * @method handleRemoveMain
        */
        handleRemoveMain(file,fileList) {
            console.log(file);
            console.log(fileList);
            for(let i = 0; i < fileList.length; i++){
                console.log("输出剩余图片",fileList[i].response.data)
                this.imgTitleUrl = fileList[i].response.data
            }
            console.log(this.imgTitleUrl.length)

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
        * 限制佣金比例输入范围
        * @method limitCommissionRatioScope
        */
        limitCommissionRatioScope(){
            let that = this
            // console.log(that.form.commissionRatio)
            if(that.commissionInformation.commissionRatio > Number(30)){
                this.$message.error('佣金范围最大值为30');
                that.commissionInformation.commissionRatio = 30
            }
            if(that.commissionInformation.commissionRatio < Number(2)){
                this.$message.error('佣金范围最小值为2');
                that.commissionInformation.commissionRatio = 2
            }
        },
        /**
        * 获取键盘码禁止输入
        * @method getKeyboardVal
        * @param {object} e 事件对象
        */
        getKeyboardVal(e){
            let keynum  = window.event ? e.keyCode : e.which;
            let keychar = String.fromCharCode(keynum);
            if(keynum == 189 || keynum == 190 || keynum == 110 || keynum == 109){
                this.$message.error('禁止输入小数');
                e.target.value = ''
            }
        },
        handlePictureCardPreview(file) {
            this.basicInformation.dialogImageUrl = file.url;
            this.basicInformation.dialogVisible = true;
        },
        handleDownload(file) {
            console.log(file);
        },
        handleRemove(file, fileList) {
            console.log(file, fileList);
            for(let i = 0; i < fileList.length; i++){
                console.log("输出剩余图片",fileList[i].response.data)
                this.imgDetailUrl += fileList[i].response.data + ','
            }
            console.log(this.imgDetailUrl.length)
        },
        beforeAvatarUpload(file) {
            console.log("res=>",file)
            // console.log(isIMG)
            const isLtM = file.size / 1024 / 1024 < 1;
            // const isIMG = file.type == 'image/jgp' || 'image/gif' || 'image/jpeg' || 'image/jpg' || 'image/png' || 'image/svg';
            // if (!isIMG) {
            //     this.$message.error('只能上传image文件!');
            // }
            if (!isLtM) {
                this.$message.error('上传图片大小不能超过 1MB!');
            }
            return isLtM;
        },
        /**
        * 获取商品分类
        * @method getKeyboardVal
        */
        async getShopCategory(){
            let $this = this;
            const {data : res} = await this.$axios.get('/video/videocategory/list',{
                params:{
                    order:{
                        
                    },
                    current:1,
                    size:500
                },
                headers:{
                    "Authorization": "Basic aDU6aDVfd2Vi",
                    "Blade-Auth": "bearer "+sessionStorage.getItem('accessToken')
                }
            })
            console.log("分类列表=>",res)
            for(let i = 0 ; i < res.data.records.length ; i++){
                let thisListObj = {
                    categoryName:res.data.records[i].categoryName,
                    createDate:res.data.records[i].createDate,
                    id:res.data.records[i].id,
                    imgUrl:res.data.records[i].imgUrl,
                    sort:res.data.records[i].sort,
                    status:res.data.records[i].status
                }
                console.log(thisListObj)
                this.optionslist.push(thisListObj)
            }
            console.log(this.optionslist)
        },
        // 单选选中参数
        currentSel(selVal) {
            this.categoryId = selVal.id;
            // this.name = selVal.shipName;
            console.log("选择的name为：" + this.categoryId, "选择的id为:" + this.categoryId);
            console.log(selVal);
            // this.MerchantNo = {
            //     "shipName":selVal.shipName,
            //     "shipCode":selVal.shipCode
            // }
                
        },
    }
};
</script>
<style scoped>
    .el-header, .el-footer {
        background-color: #f8f8f8;
        color: #333;
        text-align: left;
        line-height: 40px;
        height: 40px!important;
        margin-bottom: 20px;
        /* border-radius: 5px; */
    }
    .basicInformation-box,.logisticsInformation-box{
        width: 600px;
    }
    .container{
        border-radius: 0!important;
    }
</style>