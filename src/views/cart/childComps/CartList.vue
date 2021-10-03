<template>
	<div class="cart-list">
    <scroll class="content" ref="scroll">
      <cart-list-item v-for="(item, index) in list"
                      :key="index"
                      :item-info="item"/>
    </scroll>
  </div>
</template>

<script>
  import Scroll from "components/common/scroll/Scroll";

  import CartListItem from "./CartListItem";

  import {mapGetters} from 'vuex'

	export default {
		name: "CartList",
    components: {
      Scroll,
      CartListItem
    },
    computed: {
		  ...mapGetters({
        list: 'cartList'
      })
    },
    activated() {
		  this.$refs.scroll.refresh()
    }
  }
</script>

<style scoped>
  .cart-list {
    height: calc( 100vh - 133px );
  }

  .content {
    height: 100%;
    overflow: hidden;
    /*position: absolute;*/
    /*top: 44px;*/
    /*bottom: 49px;*/
    /*left: 0;*/
    /*right: 0;*/
  }
</style>
