<template>
  <div id="category">
  </div>
</template>

<script>

  import BScroll from 'better-scroll'
	export default {
		name: "Category",
    data() {
		  return {
		    scroll: null
      }
    },
    methods: {
		  btnClick() {
        console.log('btnClick');
      }
    },
    //组件创建完后调用
    mounted() {
      this.scroll = new BScroll(document.querySelector('.wrapper'), {
        probeType: 3,
        pullUpLoad: true,
        click: true
      })

      this.scroll.on('scroll', (position) => {
        // console.log(position);
      })

      this.scroll.on('pullingUp', () => {
        // console.log('上拉加载更多');
      })
    }
  }
</script>

<style scoped>
  .wrapper {
    height: 185px;
    background-color: red;

    overflow: hidden;
    /*overflow-y: scroll;*/
  }
</style>
