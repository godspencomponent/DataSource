<template>
  <el-input type="textarea" class="input" v-model="incontent"></el-input>
</template>

<script>
  export default {
    name: 'attr-com',
    data () {
      return {
        incontent: ''
      }
    },
    props: ['content'],
    computed: {
    },
    watch: {
      content: {
        handler (val) {
          this.incontent = val
        },
        immediate: true
      },
      incontent (v) {
        this.$emit('update:content', v)
      }
    },
    methods: {
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
  .input {
    height: 100%;
    width: 100%;

    >>>textarea {
      height: 100%;
      width: 100%;
    }
  }
</style>
