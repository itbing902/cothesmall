<template>
	<div class="bottom-bar">
    <div class="check-content">
      <check-button :is-checked="isSelectAll"
                    class="check-button"
                    @click.native="checkClick"/>
      <span>全选</span>
    </div>

    <div class="price">
      合计: {{totalPrice}}
    </div>

    <div class="calculate" @click="calcClick">
      结算({{checkLength}})
    </div>
  </div>
</template>

<script>
  import CheckButton from "components/content/checkButton/CheckButton";

  import { mapGetters } from 'vuex'

	export default {
		name: "CartBottomBar",
    components: {
      CheckButton
    },
    computed: {
		  ...mapGetters({
        list: 'cartList'
      }),
      totalPrice() {
        return '￥' + this.list.filter(item => {
          return item.checked
        }).reduce((preValue, item) => {
          return preValue + item.price * item.count
        },0).toFixed(2)
      },
      checkLength() {
        return this.list.filter(item => item.checked).length
      },
      isSelectAll() {
        if (this.list.length === 0) return false

        //1.使用filter
        // return !(this.list.filter(item => !item.checked).length)

        //2.使用find
        return !this.list.find(item => !item.checked)

        //3.普通遍历
        // for (let item of this.list) {
        //   if (!item.checked) {
        //     return false
        //   }
        // }
        // return  true
      }
    },
    methods: {
      checkClick() {
        if (this.isSelectAll) {  //全部选中
          this.list.forEach(item => item.checked = false)
        } else { //部分或全部不选中
          this.list.forEach(item => item.checked = true)
        }
      },
      calcClick() {
        if (!this.isSelectAll) {
          this.$toast.show('请选择购买的商品', 2000)
        }
      }
    }
	}
</script>

<style scoped>
  .bottom-bar {
    position: relative;
    display: flex;

    height: 40px;
    line-height: 40px;

    background-color: #eee;
    font-size: 14px;
  }

  .check-content {
    display: flex;
    align-items: center;
    margin-left: 5px;
    width: 60px;
  }

  .check-button {
    width: 22px;
    height: 22px;
    line-height: 23px;
    margin-right: 5px;
  }

  .price {
    margin-left: 20px;
    flex: 1;
  }

  .calculate {
    width: 90px;
    background-color: #f13e3a;
    color: #fff;
    text-align: center;
  }

</style>
