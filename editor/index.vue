<template>
  <div class="component-editor">
    <el-collapse v-model="activeNames">
      <el-collapse-item name="1">
        <template slot="title">
          <div class="titleWrap">
            <div>数据源</div>
          </div>
        </template>
        <div>
          <el-form size="mini" label-width="80px" class="demo-ruleForm">
            <el-form-item label="数据源">
              <el-select v-model="componentInfo.type" placeholder="请选择">
                <el-option
                  v-for="item in types"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <div v-if="componentInfo.type=='api'">
              <el-checkbox v-model="componentInfo.auto">自动更新请求</el-checkbox>
              <el-input-number
                v-model="componentInfo.autoTime"
                size="mini"
                :min="1"
                :max="50"
                style="width:80px"
                controls-position="right"
                label="描述文字"
              ></el-input-number>秒一次
            </div>

            <div v-if="componentInfo.type=='data'">
              <el-input type="textarea" :rows="6" v-model="componentInfo.extended.data"></el-input>
            </div>
            <div v-else-if="componentInfo.type=='api'">
              <div>
                <h5>URL:</h5>
              </div>
              <div class="url-info-text">将回调参数配置到url中, 例:http://api.test?value=:value</div>
              <el-input type="textarea" :rows="3" v-model="componentInfo.extended.api.url"></el-input>
            </div>
          </el-form>
        </div>
      </el-collapse-item>
      <el-collapse-item name="2">
        <template slot="title">
          <div class="titleWrap">
            <div>过滤器</div>
          </div>
        </template>
        <div>与现实生活一致：与现实生活的流程、逻辑保持一致，遵循用户习惯的语言和概念；</div>
      </el-collapse-item>
      <div>
        <div class="resultWrap">
          <div>结果</div>
          <i class="header-icon el-icon-refresh" v-if="componentInfo.type=='api'" @click="syncdata"></i>
        </div>
        <el-input v-loading="dataLoading" type="textarea" :rows="6" v-model="value"></el-input>
        <!-- <attr-data v-loading="dataLoading" class="dataArea" :content="value"></attr-data> -->
      </div>
    </el-collapse>
  </div>
</template>

<script>
  import {Server} from 'godspen-lib'

  export default {
    name: 'maliangeditor',
    props: {
      componentInfo: { // 固定字段，收集所有属性值
        type: [Object],
        default () {
          return {
            type: 'data',
            dataFiltter: [],
            auto: false,
            autoTime: 1,
            extended: {
              api: {
                url: 'https://godspen.ymm56.com/shop/api/users/publisher?id=21'
              },
              data: '{}'
            }
          }
        }
      }
    },
    data: function () {
      return {
        dataLoading: false,
        value: '',
        activeNames: ['1', '3'],
        types: [{
          value: 'data',
          label: '静态数据'
        }, {
          value: 'api',
          label: 'API接口'
        }]
      }
    },
    computed: {
    },
    watch: {
      'componentInfo.type' (data) {
        this.syncdata()
      },
      'componentInfo.extended.data' (data) {
        if (this.componentInfo.type == 'data') {
          this.value = data
        }
      }
    },
    mounted: function () {
    },
    methods: {
      changeData (data) {
        this.value = data
      },
      async getUrlData (data) {
        this.dataLoading = true
        var response = await Server.fetch(data.url)
        var info = await response.text()
        this.dataLoading = false
        this.changeData(info)
      },
      syncdata () {
        if (this.componentInfo.type == 'data') {
          this.changeData(this.componentInfo.extended.data)
        } else if (this.componentInfo.type == 'api') {
          this.getUrlData(this.componentInfo.extended.api)
        }
      },
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
  .component-editor {
    .titleWrap {
      display: flex;
      padding: 5px;
      font-size: 14px;

      .header-icon {
        line-height: 50px;
        margin-left: 10px;
      }
    }

    .resultWrap {
      display: flex;
      padding: 10px 5px;
      font-size: 14px;

      .header-icon {
        line-height: 20px;
        margin-left: 10px;
        cursor: pointer;
      }
    }

    .dataArea {
      height: 300px;
    }

    .dataTextarea {
      height: 200px;
    }
  }
</style>
