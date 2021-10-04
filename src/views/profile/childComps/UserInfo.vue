<template>
  <div id="user-info">
    <a href="#" class="clear-fix">
      <slot name="user-icon">
        <div class="profile"><img src="~assets/img/profile/avatar.svg"></div>
      </slot>
      <div class="login-info left">
        <slot name="user-nickname">
          <div>登录/注册</div>
        </slot>
        <div class="phone">
          <span>
            <div class="icon-mobile"><img src="~assets/img/profile/phone.svg"></div>
          </span>
          <slot name="user-phone">暂无绑定手机号</slot>
        </div>
      </div>
      <div class="more"><img src="~assets/img/common/arrow-left.svg"></div>
    </a>
  </div>
</template>

<script>
	export default {
		name: "UserInfo"
	}
</script>

<style scoped>
  #user-info {
    background-color: var(--color-tint);
    padding: 5px;
    margin-top: -5px;
  }

  #user-info .profile img {
    width: 60px;
    height: 60px;
    background-color: #fff;
    border-radius: 30px;

    position: relative;
    left: 15px;
    top: 15px
  }

  .left {
    float: left;
  }

  #user-info .more {
    width: 11px;
    height: 22px;
    margin-top: 18px;
  }

  #user-info .login-info {
    color: #fff;
    margin: -35px 0 0 100px;
  }

  #user-info .login-info .phone {
    position: relative;

    font-size: 13px;
    margin-top: 5px;
    margin-left: 15px;
    font-weight: 300;
  }

  #user-info .login-info .phone .icon-mobile img {
    position: absolute;
    width: 12px;
    height: 18px;
    left: -15px;
    top: 0px;
  }

  .more img {
    width: 20px;
    height: 20px;

    position: relative;
    left: 280px;
    top: -45px;
  }


</style>
