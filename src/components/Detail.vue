<template>
    <div class="detail">
        <div class="content-wrapper" v-if="!publish">
            <el-collapse v-model="activeNames">
                <el-collapse-item name="1">
                    <template slot="title">
                        <p class="title">基本信息</p>
                    </template>
                    <el-form
                        class="base-infor"
                        :model="baseInfor"
                        label-width=".9rem"
                        :inline="true">
                        <el-form-item label="组件ID：">
                            <span>{{baseInfor.bscID}}</span>
                        </el-form-item>
                        <el-form-item label="组件名称：">
                            <span>{{baseInfor.bscName}}</span>
                        </el-form-item>
                        <el-form-item label="组件版本：">
                            <el-input v-model="baseInfor.version"></el-input>
                        </el-form-item>
                        <el-form-item label="创建人：">
                            <span>{{baseInfor.developer}}</span>
                        </el-form-item>
                        <el-form-item label="创建时间：">
                            <span>{{baseInfor.bscTime}}</span>
                        </el-form-item>
                    </el-form>
                </el-collapse-item>
                <el-collapse-item name="2">
                    <template slot="title">
                        <p class="title">描述信息</p>
                    </template>
                    <div class="interface">
                        <span class="label">组件统一访问接口：</span>
                        <span v-if="!isDr" class="baseInfor.url"></span>
                        <el-input v-else v-model="baseInfor.url" placeholder="输入组件统一访问接口"></el-input>
                    </div>
                    <div class="interface">
                        <span class="label">组件描述：</span>
                        <span v-if="!isDr">{{baseInfor.description}}</span>
                        <el-input
                            v-else
                            type="textarea"
                            :autosize="{ minRows: 2}"
                            placeholder="请输入内容"
                            v-model="baseInfor.description">
                        </el-input>
                    </div>
                    <el-button v-if="isDr" type="primary" size="mini" @click="saveDesc">保存配置</el-button>
                </el-collapse-item>
                <el-collapse-item name="3">
                    <template slot="title">
                        <p class="title">服务信息</p>
                    </template>
                    <div class="service-wrapper">
                        <span v-if="!services.length">暂无数据~</span>
                        <div class="service-content" v-for="(service, index) in services" :key="service.id">
                            <el-form
                                class="service-data"
                                :model="service"
                                :inline="true">
                                <el-form-item label="服务ID">
                                    <span>{{service.serviceId}}</span>
                                </el-form-item>
                                <el-form-item label="服务名称">
                                    <span v-if="!isDr">{{service.serviceName}}</span>
                                    <el-input v-else v-model="service.serviceName" size="mini"></el-input>
                                </el-form-item>
                                <el-form-item label="服务类型">
                                    <span v-if="!isDr">{{service.serviceType}}</span>
                                    <el-input v-else v-model="service.serviceType" size="mini"></el-input>
                                </el-form-item>
                                <el-form-item label="服务访问路径">
                                    <span v-if="!isDr">{{service.servicePath}}</span>
                                    <el-input v-else v-model="service.servicePath" size="mini"></el-input>
                                </el-form-item>
                                <el-form-item label="服务访问参数">
                                    <span v-if="!isDr">{{service.serviceParam}}</span>
                                    <el-input v-else v-model="service.serviceParam" size="mini"></el-input>
                                </el-form-item>
                                <el-form-item label="是否对外开放">
                                    <span v-if="!isDr">{{service.serviceOpen}}</span>
                                    <el-checkbox v-else v-model="service.serviceOpen"></el-checkbox>
                                </el-form-item>
                                <el-form-item label="是否加密">
                                    <span v-if="!isDr">{{service.serviceSecurity}}</span>
                                    <el-checkbox v-else v-model="service.serviceSecurity"></el-checkbox>
                                </el-form-item>
                                <el-form-item label="加密方法">
                                    <span v-if="!isDr">{{service.serviceMethod}}</span>
                                    <el-input v-else v-model="service.serviceMethod" size="mini"></el-input>
                                </el-form-item>
                                <el-form-item label="密钥">
                                    <span v-if="!isDr">{{service.serviceKey}}</span>
                                    <el-input v-else v-model="service.serviceKey" size="mini"></el-input>
                                </el-form-item>
                                <el-form-item label="返回数据格式类型">
                                    <span v-if="!isDr">{{service.serviceReturnvaluetype}}</span>
                                    <el-input v-else v-model="service.serviceReturnvaluetype" size="mini"></el-input>
                                </el-form-item>
                                <el-form-item label="返回数据格式：">
                                    <span v-if="!isDr">{{service.serviceFormat}}</span>
                                    <el-input v-else v-model="service.serviceFormat" size="mini"></el-input>
                                </el-form-item>
                            </el-form>
                            <div v-if="isDr" class="service-delete operate" @click="deleteService(index)">
                                删除
                            </div>
                        </div>
                        <div v-if="isDr" style="text-align: left">
                            <el-button type="primary" size="mini" @click="handleAddService">添加</el-button>
                        </div>
                        <el-button v-if="isDr" type="primary" @click="saveService">保存配置</el-button>
                    </div>
                </el-collapse-item>
                <el-collapse-item name="4">
                    <template slot="title">
                        <p class="title">资源配置信息</p>
                    </template>
                </el-collapse-item>
                <el-collapse-item name="5">
                    <template slot="title">
                        <p class="title">组件依赖</p>
                    </template>
                    <div class="comp-dependience">
                        <span v-if="!deps.length">暂无数据~</span>
                        <div
                            class="dep-wrapper"
                            v-for="(dep, index) in deps"
                            :key="index">
                            <el-form class="dep-content" :model="dep" label-width=".8rem" :inline="true">
                                <el-form-item label="组件ID">
                                    <span v-if="!isDr">{{dep.id}}</span>
                                    <el-input v-else v-model="dep.id"></el-input>
                                </el-form-item>
                                <el-form-item label="组件版本">
                                    <span v-if="!isDr">{{dep.version}}</span>
                                    <el-input v-else v-model="dep.version"></el-input>
                                </el-form-item>
                            </el-form>
                            <div v-if="isDr" class="dep-delete operate" @click="deleteDep(index)">
                                删除
                            </div>
                        </div>
                        <div  v-if="isDr" class="add-wrapper">
                            <el-button type="primary" size="mini" @click="addDep">添加</el-button>
                        </div>
                        <el-button v-if="isDr" type="primary" size="mini" @click="saveRel">保存配置</el-button>
                    </div>
                </el-collapse-item>
                <el-collapse-item name="6">
                    <template slot="title">
                        <p class="title">组件数据源</p>
                    </template>
                    <div class="database-source">
                        <el-form :model="database" label-width="1rem" :inline="true">
                            <el-form-item label="数据库名称">
                                <span v-if="!isDr">{{database.dbName}}</span>
                                <el-input v-else v-model="database.dbName" placeholder="输入数据库名称"></el-input>
                            </el-form-item>
                            <el-form-item label="数据库版本">
                                <span v-if="!isDr">{{database.dbVersionId}}</span>
                                <el-input v-else v-model="database.dbVersionId" placeholder="输入数据库版本"></el-input>
                            </el-form-item>
                        </el-form>
                        <el-button v-if="isDr" type="primary" size="mini" @click="saveDs">保存配置</el-button>
                    </div>
                </el-collapse-item>
                <el-collapse-item name="7">
                    <template slot="title">
                        <p class="title">数据模型</p>
                    </template>
                    <div class="data-model">
                        <data-model-infor :dataModelList="dataModel"></data-model-infor>
                    </div>
                </el-collapse-item>
            </el-collapse>
        </div>
        <div class="content-wrapper publish-content-wrapper" v-else>
             <el-collapse v-model="publishActiveNames">
                <el-collapse-item name="1">
                    <template slot="title">
                        <p class="title">组件配置</p>
                    </template>
                    <el-form
                        class="comp-config"
                        :model="compConfig">
                        <el-form-item label="环境类型">
                            <el-radio-group v-model="compConfig.profileType">
                                <el-radio label="online">线上</el-radio>
                                <el-radio label="dev">开发</el-radio>
                                <el-radio label="test">测试</el-radio>
                            </el-radio-group>
                        </el-form-item>
                        <el-form-item label="是否为当前运行版本">
                            <el-radio-group v-model="compConfig.isRunning">
                                <el-radio :label="1">是</el-radio>
                                <el-radio :label="0">否</el-radio>
                            </el-radio-group>
                        </el-form-item>
                        <el-form-item label="发布描述信息">
                            <el-input
                                v-model="compConfig.desc"
                                type="textarea"
                                autosize></el-input>
                        </el-form-item>
                    </el-form>
                </el-collapse-item>
                <el-collapse-item name="2">
                    <template slot="title">
                        <p class="title">模型配置</p>
                    </template>
                    <el-form
                        v-for="modelConfig in modelConfigs"
                        :key="modelConfig.dmID"
                        class="model-config"
                        :model="modelConfig"
                        :inline="true">
                        <el-form-item label="模型ID">
                            <span>{{modelConfig.dmID}}</span>
                        </el-form-item>
                        <el-form-item label="模型版本">
                            <span>{{modelConfig.dmVersion}}</span>
                        </el-form-item>
                        <el-form-item label="环境类型">
                            <el-radio-group v-model="modelConfig.profileType">
                                <el-radio label="online">线上</el-radio>
                                <el-radio label="dev">开发</el-radio>
                                <el-radio label="test">测试</el-radio>
                            </el-radio-group>
                        </el-form-item>
                        <el-form-item label="是否为当前运行版本">
                            <el-radio-group v-model="modelConfig.isRunning">
                                <el-radio label="1">是</el-radio>
                                <el-radio label="0">否</el-radio>
                            </el-radio-group>
                        </el-form-item>
                        <el-form-item label="发布信息描述">
                            <el-input
                                v-model="modelConfig.desc"
                                type="textarea"
                                autosize></el-input>
                        </el-form-item>
                    </el-form>
                </el-collapse-item>
            </el-collapse>
        </div>
        <div class="content-footer">
            <el-button v-if="!publish" type="primary" @click="submit">发布</el-button>
            <el-button v-else type="primary" @click="saveDeploy">确认发布</el-button>
        </div>
    </div>
</template>
<script>
import {query, formatDate, formatTime, uuid} from '@/utils'
import ComponentInfor from '@/components/ComponentInfor'
import DataModelInfor from '@/components/DataModelInfor'

export default {
    components: {
        ComponentInfor,
        DataModelInfor
    },
    data() {
        return {
            id: '',
            version: '',
            activeNames: ['1'],
            publishActiveNames: ['1'],
            baseInfor: {
                bscID: '',
                bscName: '',
                bscType: '',
                bscTime: '',
                modifiedTime: '',
                bscNum: '',
                developer: '',
                version: '',
                url: '',
                description: '',
                open: ''
            },
            services: [],
            deps: [],
            database: {
                dbName: '',
                dbVersionId: '',
                h2compId: '',
                deployVersionId: '',
                createdBy: '',
                gmtCreate: '',
                modifiedBy: '',
                gmtModified: ''
            },
            dataModel: [],
            compConfig: {
                preVersionID: '0',
                profileType: 'online',
                desc: '',
                isRunning: 1,
                createdBy: '',
                gmtCreate: '',
                modifiedBy: '',
                gmtModeified: ''
            },
            modelConfigs: [],
            newService: {
                serviceId: '',
                serviceName: '',
                serviceType: '',
                servicePath: '',
                serviceParam: '',
                serviceOpen: false,
                serviceSecurity: false,
                serviceMethod: '',
                serviceKey: '',
                serviceReturnvaluetype: '',
                serviceFormat: ''
            },
            newDep: {
                id: '',
                version: ''
            },
            publish: false,
            showAddService: false,
            isDr: false
        }
    },
    created() {
        let param = query(location.href.split('?')[1])
        this.id = param.id || ''
        this.version = param.version || ''
        this.isDr = !!param.isDr
        this.$request
            .post('/api/cloudplatform/selectDrBSCInfo-Main')
            .set('contentType', 'application/json')
            .send({
                id: this.id,
                version: this.version
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
            .post('/api/cloudplatform/selectBSCInfo-Service')
            .set('contentType', 'application/json')
            .send({
                id: this.id,
                version: this.version
            })
            .end((err, res) => {
                if (!!err) {
                    this.$message({
                        type: 'error',
                        message: err.response.text
                    })
                } else {
                    this.services = res.body
                }
            })
        this.$request
            .post('/api/cloudplatform/selectBSCInfo-Rel')
            .set('contentType', 'application/json')
            .send({
                id: this.id,
                version: this.version
            })
            .end((err, res) => {
                if (!!err) {
                    this.$message({
                        type: 'error',
                        message: err.response.text
                    })
                } else {
                    this.deps = res.body
                }
            })
        this.$request
            .post('/api/cloudplatform/selectBSCInfo-Ds')
            .set('contentType', 'application/json')
            .send({
                id: this.id,
                version: this.version
            })
            .end((err, res) => {
                if (!!err) {
                    this.$message({
                        type: 'error',
                        message: err.response.text
                    })
                } else {
                    this.database = res.body
                }
            })
        this.$request
            .post('/api/cloudplatform/selectDrBSCDm')
            .set('contentType', 'application/json')
            .send({
                id: this.id,
                version: this.version
            })
            .end((err, res) => {
                if (!!err) {
                    this.$message({
                        type: 'error',
                        message: err.response.text
                    })
                } else {
                    this.dataModel = res.body
                }
            })
    },
    mounted() {
    },
    methods: {
        uuid,
        formatDate,
        formatTime,
        submit() {
            this.publish = true
            this.$request
                .post('/api/cloudplatform/deployDmIdAndVersion')
                .set('contentType', 'application/json')
                .send({
                    id: this.id,
                    version: this.version
                })
                .end((err, res) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    } else {
                        this.modelConfigs = res.body
                    }
                })
        },
        saveDeploy() {
            this.$request
                .post('/api/cloudplatform/deployBSCandDm')
                .set('contentType', 'application/json')
                .send({
                    ...this.compConfig,
                    dmReleaseEntity: this.modelConfigs,
                    bscID: this.id,
                    bscVersion: this.version
                })
                .end((err) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    } else {
                        this.$message('组件发布成功！')
                    }
                })
        },
        deleteService(index) {
            this.services.splice(index, 1)
        },
        saveDesc() {
            this.$request
                .post('/api/cloudplatform/submitDrBSCInfo-Desc')
                .set('contentType', 'application/json')
                .send({
                    id: this.id,
                    version: this.version,
                    url: this.baseInfor.url,
                    desc: this.baseInfor.description
                })
                .end((err) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    } else {
                        this.$message('保存成功！')
                    }
                })
        },
        saveService() {
            let result = [...this.services];
            if(this.services.length === 0) {
                result.push({
                    ...this.newService,
                    bscID: this.id,
                    bscVersion: this.version
                })
            }
            this.$request
                .post('/api/cloudplatform/submitDrBSCInfo-Service')
                .set('contentType', 'application/json')
                .send(result)
                .end((err) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    } else {
                        this.$message('保存成功！')
                    }
                })
        },
        saveRel() {
            let result = [...this.deps];
            result.unshift({
                id: this.id,
                version: this.version
            })
            this.$request
                .post('/api/cloudplatform/submitDrBSCInfo-Rel')
                .set('contentType', 'application/json')
                .send(result)
                .end((err) => {
                    if (!!err) {
                        this.$message({
                            type: 'error',
                            message: err.response.text
                        })
                    } else {
                        this.$message('保存成功！')
                    }
                })
        },
        saveDs() {
            this.$request
                  .post('/api/cloudplatform/submitDrBSCInfo-Ds')
                  .set('contentType', 'application/json')
                  .send(this.database)
                  .end((err) => {
                      if (!!err) {
                          this.$message({
                              type: 'error',
                              message: err.response.text
                          })
                      } else {
                          this.$message('保存成功！')
                      }
                  })
        },
        handleAddService() {
            let id = uuid();
            this.services.push({
                ...this.newService,
                serviceId: id,
                bscID: this.id,
                bscVersion: this.version
            })
        },
        addDep() {
            this.deps.push({
                ...this.newDep
            })
        },
        deleteDep(index) {
            this.deps.splice(index, 1)
        }
    }
}
</script>
<style lang="stylus">
.detail
    font-size: .14rem
    padding: 0 .1rem
    background: #E4E7ED
    .content-wrapper
        position: fixed
        top: .6rem
        left: 0
        bottom: 0
        right: 0
        overflow-y: auto
    .el-collapse
        margin-bottom: 1rem
        .title
            text-align: left
            margin: 0
            padding: 0 .3rem
            overflow: hidden
            .line
                display: inline-block
                margin-left: .14rem
                width: 90%
                height: 1px
                border-top: 1px solid #000000
        .el-collapse-item__wrap
            padding: 0 .2rem
            .el-form
                text-align: left
            .base-infor
                .el-form-item
                    width: 30%
            .interface
                display: flex
                align-items: center
                margin-bottom: .1rem
                .label
                    width: 1.5rem
            .operate
                display: flex
                align-items: center
                font-size: .14rem
                position: absolute
                top: 0
                right: 0
                width: .2rem
                height: 100%
                background: #F56C6C
                color: #ffffff
                cursor: pointer
                &:active
                    background: #FF4040
            .service-wrapper
                width: 100%
                margin: 0 auto
                .service-content
                    position: relative
                    margin-bottom: .1rem
                    border-radius: .5rem
                    .service-data
                        display: flex
                        justify-content: space-between
                        flex-wrap: wrap
                        padding: 0 .05rem
                        margin-right: .2rem
                        background: rgba(0, 0, 0, .06)
            .comp-dependience
                width: 100%
                .dep-wrapper
                    position: relative
                    margin-bottom: .1rem
                    border-radius: .5rem
                    .dep-content
                        display: flex
                        align-items: center
                        padding: 0 .05rem
                        margin-right: .2rem
                        background: rgba(0, 0, 0, .06)
                        .el-form-item
                            margin: .1rem 0
                .add-wrapper
                    text-align: left
            .model-config
                background: rgba(0, 0, 0, .06)
                padding: 0 .1rem
                .el-form-item
                    min-width: 30%
    .add-service
        .el-form
            text-align: left
            .el-form-item
                .el-input
                    max-width: 70%
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
