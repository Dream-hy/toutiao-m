<template>
<div class="update-photo">
  <img class="img" :src="img" alt="" ref="img"/>
  <div class="toolbar">
    <div class="cancel" @click="$emit('close')">取消</div>
    <div class="confirm" @click="onConfirm">完成</div>
  </div>
</div>
</template>

<script>
import 'cropperjs/dist/cropper.css'
import Cropper from 'cropperjs'
import {updateUserPhoto} from '@/api/user'
export default {
  name: "UpdatePhoto",
  components: {},
  props: {
    img:{
      type:[String,Object],
      required:true
    }
  },
  data() {
    return {
      cropper:null
    };
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {
    const image = this.$refs.img
    this.cropper = new Cropper(image,{
        viewMode: 1,
        dragMode: 'move',
        aspectRatio: 1,
        // autoCropArea: 1,
        cropBoxMovable: false,
        cropBoxResizable: false,
        background: false,
        movable: true
    })
  },
  methods: {
    onConfirm(){
      //纯客户端的裁切使用
      this.cropper.getCroppedCanvas().toBlob(blob => {
        this.updateUserPhoto(blob)
      })
    },
    async updateUserPhoto (blob){
      this.$toast.loading({
        message:'保存中...',
        forbidClick:true,//禁止背景点击
        duration:0 //持续展示
      })
      try {
        // 如果 Content-Type 要求是	application/json	，则 data 传普通对象 {}
        // 如果 Content-Type 要求是	multipart/form-data	，则 data 传 FormData 对象
        const formData = new FormData()
        formData.append('photo',blob)

        const {data} = await updateUserPhoto(formData)

        this.$emit('close')

        this.$emit('update-photo',data.data.photo)
        //提示成功
        this.$toast.success('更新成功！')
      } catch (err) {
        this.$toast.fail('更新失败！')
      }
      
    }
  },
};
</script>

<style scoped lang="less">
.update-photo{
  .toolbar{
    position:fixed;
    left: 0;
    right: 0;
    bottom: 0;
    display: flex;
    justify-content: space-between;
    .cancel,.confirm{
      width: 90px;
      height: 90px;
      font-size: 30px;
      display: flex;
      justify-content: center;
      align-items: center;
    }
  }
}
.img{
  display: block;
  max-width: 100%;

}
</style>
