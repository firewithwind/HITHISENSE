<template>
    <div class="prod-detail">
        <div class="content-wrapper">
            <el-collapse v-model="activeNames">
                <el-collapse-item name="1">
                    <template slot="title">
                        <p class="title">基本信息</p>
                    </template>
                    <el-form
                        class="base-infor"
                        :model="baseInfor"
                        label-width="1rem">
                        <el-form-item label="产品ID">
                            <span>{{baseInfor.pdtId}}</span>
                        </el-form-item>
                        <el-form-item label="产品名称">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.pdtName" placeholder="输入产品名称"></el-input>
                            <span v-else>{{baseInfor.pdtName}}</span>
                        </el-form-item>
                        <el-form-item label="产品类别">
                            <el-radio-group v-if="isMOr&&editor" v-model="baseInfor.pdtType">
                                <el-radio
                                    v-for="type in prodTypeEnum"
                                    :key="type.value"
                                    :label="type.value">{{type.label}}</el-radio>
                            </el-radio-group>
                            <span v-else>{{prodType[baseInfor.pdtType]}}</span>
                        </el-form-item>
                        <el-form-item label="产品LOGO">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.pdtIcon" placeholder="输入产品LOGO"></el-input>
                            <span v-else>{{baseInfor.pdtIcon}}</span>
                        </el-form-item>
                        <el-form-item label="产品版本号">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.pdtVersion" placeholder="输入产品版本号"></el-input>
                            <span v-else>{{baseInfor.pdtVersion}}</span>
                        </el-form-item>
                        <el-form-item label="产品状态">
                            <el-radio-group v-if="isMOr&&editor" v-model="baseInfor.pdtStatus">
                                <el-radio v-for="st in pdtStatusEnum" :label="st.value" :key="st.value">{{st.label}}</el-radio>
                            </el-radio-group>
                            <span v-else>{{prodStatus[baseInfor.pdtStatus]}}</span>
                        </el-form-item>
                        <el-form-item label="运营团队ID">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.busoprTeamId" placeholder="输入运营团队ID"></el-input>
                            <span v-else>{{baseInfor.busoprTeamId}}</span>
                        </el-form-item>
                        <el-form-item label="产品概述">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.pdtDesc" placeholder="输入产品概述"></el-input>
                            <span v-else>{{baseInfor.pdtDesc}}</span>
                        </el-form-item>
                        <el-form-item label="产品功能">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.pdtFunc" placeholder="输入产品功能"></el-input>
                            <span v-else>{{baseInfor.pdtFunc}}</span>
                        </el-form-item>
                        <el-form-item label="使用场景">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.useCase" placeholder="输入使用场景"></el-input>
                            <span v-else>{{baseInfor.useCase}}</span>
                        </el-form-item>
                        <el-form-item label="应用案例">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.appCase" placeholder="输入应用案例"></el-input>
                            <span v-else>{{baseInfor.appCase}}</span>
                        </el-form-item>
                        <el-form-item label="产品手册">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.pdtMan" placeholder="输入产品手册"></el-input>
                            <span v-else>{{baseInfor.pdtMan}}</span>
                        </el-form-item>
                        <el-form-item label="常见问题">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.pdtQuestion" placeholder="输入常见问题"></el-input>
                            <span v-else>{{baseInfor.pdtQuestion}}</span>
                        </el-form-item>
                        <el-form-item label="服务协议">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.serviceProtocol" placeholder="输入服务协议"></el-input>
                            <span v-else>{{baseInfor.serviceProtocol}}</span>
                        </el-form-item>
                        <el-form-item label="创建人">
                            <el-input v-if="isMOr&&editor" v-model="baseInfor.createdBy" placeholder="输入创建人"></el-input>
                            <span v-else>{{baseInfor.createdBy}}</span>
                        </el-form-item>
                    </el-form>
                </el-collapse-item>
                <el-collapse-item name="2">
                    <template slot="title">
                        <p class="title">组件信息</p>
                    </template>
                    <div class="comp-wrapper">
                        <component-infor :infor="compList"></component-infor>
                    </div>
                </el-collapse-item>
            </el-collapse>
            <div class="content-footer">
                <el-button type="primary" @click="savePdt">保存</el-button>
            </div>
        </div>
    </div>
</template>
<script>
import ComponentInfor from '@/components/ComponentInfor'
import {prodTypeEnum, pdtStatusEnum, prodType, prodStatus} from '@/dataMap'
import {query, uuid} from '@/utils'

export default {
    components: {
        ComponentInfor
    },
    data() {
        return {
            prodTypeEnum,
            pdtStatusEnum,
            prodType,
            prodStatus,
            activeNames: ['1'],
            baseInfor: {
                pdtId: '',
                pdtName: '',
                pdtType: '01',
                pdtIcon: '',
                pdtVersion: '',
                bizServiceIDs: '',
                pdtStatus: '0',
                busoprTeamId: '',
                pdtDesc: '',
                pdtFunc: '',
                useCase: '',
                appCase: '',
                pdtMan: '',
                pdtQuestion: '',
                serviceProtocol: '',
                createdBy: '',
                createdTime: '',
                lastModifiedBy: '',
                lastModifiedTime: ''
            },
            compList: [],
            editor: false,
            isMOr: false
        }
    },
    methods: {
        savePdt() {
            this.$request
                .post('/api/cloudplatform/MOrpdtEdit-baseInfo')
                .set('contentType', 'application/json')
                .send(this.baseInfor)
                .end((err) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    }
                })
            let result = this.compList.map(item => {
                return {
                    id: item.bscID,
                    version: item.version
                }
            })
            result.unshift({
                id: this.baseInfor.pdtId,
                version: this.baseInfor.pdtVersion
            })
            this.$request
                .post('/api/cloudplatform/MOrpdtEdit-selectBSCs')
                .set('contentType', 'application/json')
                .send(result)
                .end((err) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    }
                })
            this.$message('保存成功！')
        }
    },
    created() {
        let param = query(location.href.split('?')[1])
        this.editor = !!param.editor
        this.isMOr = !!param.isMOr
        if (!!param.id) {
            this.baseInfor.pdtId = param.id
            this.baseInfor.pdtVersion = param.version
            this.$request
                .post('/api/cloudplatform/selectpdtInfo-baseInfo')
                .set('contentType', 'application/json')
                .send({
                    id: param.id,
                    version: param.version
                })
                .end((err, res) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    } else {
                        this.baseInfor = res.body
                    }
                })
            this.$request
                .post('/api/cloudplatform/selectpdtInfo-BSCs')
                .set('contentType', 'application/json')
                .send({
                    id: param.id,
                    version: param.version
                })
                .end((err, res) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    } else {
                        this.compList = res.body || []
                    }
                })
        } else {
            let newPdtUUId = 'P' + uuid()
            this.baseInfor.pdtId = newPdtUUId
            this.$request
                .get('/api/cloudplatform/getPdtSelectBSCs')
                .end((err, res) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    } else {
                        this.compList = res.body || []
                    }
                })
        }
    }
}
</script>
<style lang="stylus">
.prod-detail
    padding: 0 .1rem
    .el-collapse
        margin-bottom: 1rem
        .title
            text-align: left
            margin: 0
            padding: 0 .3rem
            overflow: hidden
        .el-form
            font-size: .14rem
            padding: 0 .5rem
            text-align: left
        .comp-wrapper
            width: 90%
            margin: 0 auto
            margin-bottom: .2rem
            box-shadow: -0.05rem .1rem .1rem #F2F6FC
    .content-footer
        position: fixed
        bottom: 0
        left: 0
        right: 0
        padding: .1rem
        text-align: center
        background: rgba(0, 0, 0, .1)
        z-index: 2000
</style>
