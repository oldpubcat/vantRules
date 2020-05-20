<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">


      <van-form @submit="onSubmit">
        <van-field
          v-model="form.quantity"
          label="生产总数"
          placeholder="请输入（个）"
          input-align='right'
          @change="changeQuantity"
        />
        <van-field
          v-model="form.sampleQuantity"
          label="留样数量"
          placeholder="请输入（个）"
          input-align='right'
          @change="changeQuantity"
        />
        <div v-for="(item) in packingList" :key="item.id">
          <van-field
            readonly
            v-model="item.stock"
            label="余量"
            placeholder="自动生成"
            input-align='right'
          />
          <van-field
            v-model="item.countQuantity"
            label="领取数量"
            placeholder="请输入（个）"
            input-align='right'
            :rules="rules.packQuantity"
          />
        </div>
        <div style="margin: 16px 16px 0;">
          <van-button round block type="info" native-type="submit">
            提交
          </van-button>
        </div>
      </van-form>
  </div>
</template>

<script>
import { Field, Form, Button } from 'vant'
export default {
  name: 'App',
  components: {
    'van-field': Field,
    'van-form': Form,
    'van-button': Button
  },
  data(){
    return {
      form: {
        quantity: '',
        sampleQuantity: ''
      },
      packingList:[
      ],
      rules: {
        packQuantity: [
          { required: true, message: '请填写领取数量', trigger: 'onChange' },
          { validator: this.validatePositiveInteger, message: '只能输入整数', trigger: 'onChange' },
          { validator: this.validatorGreenBeanGoodQuantity, message: '包材领取数量不能大于余量', trigger: 'onChange' }
        ]
      },
      arr: [
        {
          stock: 60,
          countQuantity: null
        },
        {
          stock: 20,
          countQuantity: null
        }
      ]
    }
  },
  mounted(){
    console.log('init')
    // 假设这里有请求
    setTimeout(()=>{
      this.packingList = this.arr;
    }, 500)
  },
  methods: {
    onSubmit(){},
    validatePositiveInteger(value) {
      return /^\+?[0-9][0-9]*$/.test(value)
    },
    validatorGreenBeanGoodQuantity(value) {
      console.log(value)
      /** 
      * 当我想找出现在修改的然后进行匹配，会发生以下情况
      *  操作实例：  生产总数输入22  留样数量 输入22   然后两个领取数量都报错，然后修改不满足条件的那个领取数量， 另一个一直显示错误，无法消除， 只能再修改一次另一个才可以消除错误
      *  因为是由另外两个不相关的值计算得到的，两个值的领取数量都一致，由于没有返回索引，没办法拿到具体的某一项
      *  没办法拿到具体某一项就会出现一个不匹配，几项全报错的情况。
      *  这里需要一个 index  值
      *  */ 
      return this.packingList.every(item=>{
        return item.countQuantity <= item.stock;
      })
    },
    changeQuantity(){
      this.packingList.forEach(item => {
        item.countQuantity = Number(this.form.quantity) + Number(this.form.sampleQuantity);
      })
    },
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
