<template>
  <div id="app">
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
        <div v-for="(item, index) in packingList" :key="item.id">
          <van-field
            readonly
            v-model="item.stock"
            label="余量"
            placeholder="自动生成"
            input-align='right'
          />
          <!-- <van-field
            v-model="item.countQuantity"
            label="领取数量"
            placeholder="请输入（个）"
            input-align='right'
            :rules="rules.packQuantity"
          /> -->
          <van-field
            v-model="item.countQuantity"
            label="领取数量"
            placeholder="请输入（个）"
            input-align='right'
            :rules="getRule(index)"
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
      packingList:[],
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
    getRule(index) {
      return [
        {index, validator: this.validator, message: '包材领取数量不能大于余量', trigger: 'onChange'},
        { required: true, message: '请填写领取数量', trigger: 'onChange' },
        { validator: this.validatePositiveInteger, message: '只能输入整数', trigger: 'onChange' }
      ];
    },
    validator(value, rule) {
      console.log(value, rule)
      return this.packingList[rule.index].countQuantity <= this.packingList[rule.index].stock;
    },
    validatorGreenBeanGoodQuantity(value) {
      console.log(value)
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