<template>
  <div class="update-name">
    <!-- 导航栏 -->
    <van-nav-bar
      title="设置昵称"
      left-text="取消"
      right-text="完成"
      @click-left="$emit('close')"
      @click-right="onComfirm"
    />
    <!-- /导航栏 -->

    <!-- 输入框 -->
    <div class="filed-wrap">
      <van-field
      v-model.trim="localName"
      row="2"
      autosize
      type="textarea"
      maxlength="7"
      placeholder="请输入昵称"
      show-word-limit
    />
    </div>
    <!-- 输入框 -->

  </div>
</template>

<script>
import {updateUserProfile} from '@/api/user'
export default {
  name: 'UpdateName',
  components: {},
  props: {
    value:{
      type:String,
      required:true
    }
  },
  data () {
    return {
      localName:this.value
    }
  },
  computed: {},
  watch: {},
  created () {},
  mounted () {},
  methods: {
    async onComfirm(){
      this.$toast.loading({
        message:'保存中...',
        forbidClick:true,//禁止背景点击
        duration:0 //持续展示
      })
      try {
        const localName = this.localName
        if(!localName.length){
          this.$toast('昵称不能为空！')
          return
        }
        const {data} = await updateUserProfile({
          name:localName
        })
        console.log(data)
        //更新视图
        this.$emit('input',localName)
        //关闭弹层
        this.$emit('close')
        //提示成功
        this.$toast.success('更新成功！')
      } catch (err) {
        this.$toast.fail('更新失败！')
      }
    }
  }
}
</script>

<style scoped lang="less">
.filed-wrap{
  padding: 10px;
}
</style>
