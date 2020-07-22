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
                    <!-- <el-form-item label="商品分类">
                        <el-select v-model="basicInformation.region" placeholder="请选择">
                            <el-option key="fl1" label="分类1" value="fl1"></el-option>
                            <el-option key="fl2" label="分类2" value="fl2"></el-option>
                            <el-option key="fl3" label="分类3" value="fl3"></el-option>
                        </el-select>
                    </el-form-item> -->
                    <!-- 商品主图 -->
                    <el-form-item label="商品主图">
                        <el-upload
                            :on-success="handleSuccessMain"
                            :on-remove="handleRemoveMain"
                            :limit="1"
                            action="https://dev.yunsav.com/blade-resource/oss/endpoint/upload"
                            list-type="picture-card"
                            :auto-upload="true">
                                <i slot="default" class="el-icon-plus"></i>
                                <div slot="file" slot-scope="{file}">
                                <img
                                    class="el-upload-list__item-thumbnail"
                                    :src="file.url" alt=""
                                >
                                <span class="el-upload-list__item-actions">
                                    <span
                                        class="el-upload-list__item-preview"
                                        @click="handlePictureCardPreview(file)"
                                    >
                                    <i class="el-icon-zoom-in"></i>
                                    </span>
                                    <span
                                        v-if="!basicInformation.disabled"
                                        class="el-upload-list__item-delete"
                                        @click="handleDownload(file)"
                                    >
                                    <i class="el-icon-download"></i>
                                    </span>
                                    <span
                                        v-if="!basicInformation.disabled"
                                        class="el-upload-list__item-delete"
                                        @click="handleRemoveMain(file)"
                                    >
                                    <i class="el-icon-delete"></i>
                                    </span>
                                </span>
                            </div>
                        </el-upload>
                    </el-form-item>
                    <el-dialog :visible.sync="basicInformation.dialogVisible">
                        <img width="100%" :src="basicInformation.dialogImageUrl" alt="">
                    </el-dialog>
                    <!-- 商品标题 -->
                    <el-form-item label="商品标题">
                        <el-input v-model="basicInformation.shopTitle" placeholder="请输入商品标题"></el-input>
                    </el-form-item>
                    <!-- 商品描述 -->
                    <el-form-item label="商品描述">
                        <el-input v-model="basicInformation.shopDescribe" placeholder="请输入商品描述"></el-input>
                    </el-form-item>
                    <!-- 商品详情图 -->
                    <el-form-item label="商品详情">
                        <el-upload
                            :on-success="handleSuccessDetails"
                            action="https://dev.yunsav.com/blade-resource/oss/endpoint/upload"
                            list-type="picture-card"
                            :auto-upload="true">
                                <i slot="default" class="el-icon-plus"></i>
                                <div slot="file" slot-scope="{file}">
                                <img
                                    class="el-upload-list__item-thumbnail"
                                    :src="file.url" alt=""
                                >
                                <span class="el-upload-list__item-actions">
                                    <span
                                        class="el-upload-list__item-preview"
                                        @click="handlePictureCardPreview(file)"
                                    >
                                    <i class="el-icon-zoom-in"></i>
                                    </span>
                                    <span
                                        v-if="!basicInformation.disabled"
                                        class="el-upload-list__item-delete"
                                        @click="handleDownload(file)"
                                    >
                                    <i class="el-icon-download"></i>
                                    </span>
                                    <span
                                        v-if="!basicInformation.disabled"
                                        class="el-upload-list__item-delete"
                                        @click="handleRemove(file)"
                                    >
                                    <i class="el-icon-delete"></i>
                                    </span>
                                </span>
                            </div>
                        </el-upload>
                    </el-form-item>
                    <el-dialog :visible.sync="basicInformation.dialogVisible">
                        <img width="100%" :src="basicInformation.dialogImageUrl" alt="">
                    </el-dialog>
                     <!-- 商品保障 -->
                    <el-form-item label="商品保障">
                        <el-input type="textarea" rows="5" v-model="basicInformation.desc" placeholder="请输入商品保障"></el-input>
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
                        <el-input placeholder="请输入商品运费" v-model="logisticsInformation.shopDescribe" type="number" min="0">
                            <template slot="prepend">￥</template>
                        </el-input>
                    </el-form-item>
                </el-form>
            </div> -->
            <!-- 价格信息 -->
            <el-header>价格信息</el-header>
            <div class="logisticsInformation-box">
                <el-form ref="form" :model="priceInformation" label-width="80px">
                    <!-- 零售价格 -->
                    <el-form-item label="直播价格">
                        <el-input placeholder="请输入商品零售价" v-model="priceInformation.shopRetailPrice" type="number" min="0">
                            <template slot="prepend">￥</template>
                        </el-input>
                    </el-form-item>
                    <!-- 直播价格 -->
                    <el-form-item label="活动价格">
                        <el-input placeholder="请输入商品直播价" v-model="priceInformation.shopLivePrice" type="number" min="0">
                            <template slot="prepend">￥</template>
                        </el-input>
                    </el-form-item>
                    <!-- 提交表单 -->
                    <el-form-item>
                        <el-button type="primary" @click="onSubmit">表单提交</el-button>
                        <el-button>取消</el-button>
                    </el-form-item>
                </el-form>
            </div>
            <!-- 佣金信息 -->
            <!-- <el-header>
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
                    
                </el-form>
            </div> -->
        </div>
    </div>
</template>

<script>
export default {
    name: 'baseform',
    data() {
        return {
            spuId:'',
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
        };
    },
    created(){
        
    },
    mounted(){
        console.log(this.$route.query.spuId)
        this.spuId = this.$route.query.spuId
    },
    methods: {

        onSubmit() {
            console.log(this.spuId)
            if(this.basicInformation.shopTitle == ''){
                this.$message({
                    showClose: true,
                    message: '请输入商品标题',
                    type: 'error'
                });
            } else {
                if(this.shopBannerUrl == ''){
                    this.$message({
                        showClose: true,
                        message: '请上传商品图片',
                        type: 'error'
                    });
                } else {
                    if(this.priceInformation.shopRetailPrice == ''){
                        this.$message({
                            showClose: true,
                            message: '请输入商品价格',
                            type: 'error'
                        });
                    } else {
                        let $this = this;
                        let data = {
                            "indexes": 0,//规格显示（一个 string）
                            "spuShow":1,
                            "enable":1,
                            "activePrice": $this.priceInformation.shopLivePrice, //商品活动价
                            "price": $this.priceInformation.shopRetailPrice, //商品售价
                            "spuId":$this.spuId,
                            "images":$this.shopBannerUrl,//商品主图
                            "spuInfo":$this.shopDetailsUrl,//商品详情图
                            "title":$this.basicInformation.shopTitle,//商品标题
                            // "shareRate":$this.commissionInformation.commissionRatio,//商品佣金比例

                        };
                        this.$axios.post('/shopping-mall/sku/save',data,
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
                            }
                            this.$router.push({ path: `/DragDialog`})  
                        })
                    }
                }
            }
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
            that.shopBannerUrl = res.data
            console.log(that.shopBannerUrl)
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
            that.shopDetailsUrl += res.data
            console.log(that.shopDetailsUrl)
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
        }
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