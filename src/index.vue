<template>
  <div class="component"></div>
</template>

<script>
  import {VueExtend, Server} from 'godspen-lib'

  export default {
    mixins: [VueExtend.mixin],
    name: 'dataSource',
    label: process.env.LABEL,
    style: process.env.STYLE,
    props: {
      // 数据源
      type: {
        type: String,
        default: 'api',
        editor: {
          ignore: true
        }
      },
      // 过滤器
      dataFiltter: {
        type: Array,
        editor: {
          ignore: true
        }
      },
      datakey: {
        type: String,
        default: 'a.b',
        editor: {
          type: 'input',
          label: '数据PATH', // 将字段名显示为可读性更强的文本，不提供此项时，显示字段名
          desc: '该组件获取到数据后，会把数据设置到该参数对应的path上', // 为字段提供提示信息，帮助理解字段的意义
        }
      },
      // 是否自动请求数据
      auto: {
        type: Boolean,
        default: false,
        editor: {
          ignore: true
        }
      },
      // 自动更新间隔时间
      autoTime: {
        type: Number,
        default: 1,
        editor: {
          ignore: true
        }
      },
      // 数据源的配置信息
      extended: {
        type: Object,
        default () {
          return {
            api: {
              url: 'https://godspen.ymm56.com/shop/api/users/publisher?id=21'
            },
            data: '{}'
          }
        },
        editor: {
          ignore: true
        }
      }

    },
    data () {
      return {
        value: ''
      }
    },
    watch: {
      type: {
        handler (v) {
          this.syncdata()
        }
      },
      extended: {
        handler (v) {
          this.syncdata()
        },
        deep: true
      },
      auto () {
        this.createTimer()
      },
      autoTime () {
        this.createTimer()
      }
    },
    computed: {

    },
    mounted () {
      this.syncdata()
    },
    editorMethods: {
    },
    methods: {
      // 创建定时器 对数据进行访问和处理
      createTimer () {
        if (this._timer != null) {
          clearInterval(this._timer)
        }
        if (this.auto) {
          this._timer = setInterval(() => {
            this.syncdata()
          }, this.autoTime * 1000)
        } else {
          clearInterval(this._timer)
        }
      },
      changeData (data) {
        this.value = data
        /// 设置到数据总线中
        if (this.datakey) {
          this.dataHubSet && this.dataHubSet(this.datakey, data)
        }
      },
      async getUrlData (data) {
        var response = await Server.fetch(data.url)
        var info = await response.text()
        this.changeData(info)
      },
      syncdata () {
        if (this.type == 'data') {
          this.changeData(this.extended.data)
        } else if (this.type == 'api') {
          this.getUrlData(this.extended.api)
        }
      },
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
  .component {
    width: 0;
    height: 0;
  }
</style>
