<!--  -->
<template>
  <div>
    <link
      rel="stylesheet"
      href="//at.alicdn.com/t/font_2085780_yswtjjqcr2.css"
    />
    <div class="off">
      <section class="loginContainer">
        <div class="loginInner">
          <div class="login_header">
            <h2 class="login_logo">美团外卖</h2>
            <div class="login_header_title">
              <a
                href="javascript:;"
                :class="loginWay ? 'on' : ''"
                @click="loginWay = true"
                >短信登录</a
              >
              <a
                href="javascript:;"
                :class="loginWay ? '' : 'on'"
                @click="loginWay = false"
                >密码登录</a
              >
            </div>
          </div>
          <div class="login_content">
            <form @submit.prevent="login">
              <div :class="loginWay ? 'on' : ''">
                <section class="login_message">
                  <input
                    type="tel"
                    maxlength="11"
                    placeholder="手机号"
                    v-model="phone"
                  />
                  <button
                    :disabled="
                      (!isphone && computeTime == 0) || computeTime > 0
                    "
                    class="get_verification"
                    :class="isphone && computeTime == 0 ? 'dd' : ''"
                    @click="getCode"
                  >
                    {{
                      computeTime == 0 ? "获取验证码" : `请等待${computeTime}S`
                    }}
                  </button>
                </section>
                <section class="login_verification">
                  <input
                    type="tel"
                    maxlength="8"
                    placeholder="验证码"
                    v-model="code"
                  />
                </section>
                <section class="login_hint">
                  温馨提示：未注册美团外卖帐号的手机号，登录时将自动注册，且代表已同意
                  <a href="javascript:;">《用户服务协议》</a>
                </section>
              </div>
              <div :class="loginWay ? '' : 'on'">
                <section>
                  <section class="login_message">
                    <input
                      type="tel"
                      maxlength="11"
                      placeholder="手机/邮箱/用户名"
                      v-model="name"
                    />
                  </section>
                  <section class="login_verification">
                    <input
                      :type="ispwd ? 'text' : 'password'"
                      maxlength="8"
                      placeholder="密码"
                      v-model="pwd"
                    />
                    <div
                      class="switch_button"
                      @click="ispwd = !ispwd"
                      :class="ispwd ? 'on' : 'off'"
                    >
                      <div
                        class="switch_circle"
                        :class="ispwd ? 'right' : ''"
                      ></div>
                      <span class="switch_text">{{
                        ispwd ? "abc" : "..."
                      }}</span>
                    </div>
                  </section>
                  <section class="login_message">
                    <input
                      type="text"
                      maxlength="11"
                      placeholder="验证码"
                      v-model="captcha"
                    />
                    <img
                      class="get_verification"
                      src="http://localhost:4000/captcha"
                      alt="captcha"
                      @click="getCaptcha"
                      ref="captcha"
                    />
                  </section>
                </section>
              </div>
              <button class="login_submit" @click="login">登录</button>
            </form>
            <a href="javascript:;" class="about_us">关于我们</a>
          </div>
          <!-- <a href="javascript:" class="go_back"></a> -->
          <router-link to="/profile" class="go_back">
            <span class="item_icon">
              <i class="iconfont icon--arrows-"></i>
            </span>
          </router-link>
        </div>
      </section>
    </div>
    <AlertTip
      v-if="isShowAlert"
      @closeAlert="closeAlert"
      :AlertText="AlertText"
    ></AlertTip>
  </div>
</template>

<script>
import AlertTip from "../components/AlertTip/AlertTip";
import { reqSendCode, reqPwdLogin } from "../api";
export default {
  data() {
    return {
      loginWay: true, //登录方式
      phone: "", //电话号
      computeTime: 0, //倒计时
      ispwd: false, //切换密码是否显示
      isShowAlert: false, //是否显示警告框
      AlertText: "", //提示语句
      name: "", // 用户名
      code: "", // 短信验证码
      captcha: "", // 图形验证码
      pwd: "", //密码
    };
  },
  components: {
    AlertTip,
  },
  computed: {
    isphone() {
      return /^1[3456789]\d{9}/gi.test(this.phone);
    },
  },
  methods: {
    async getCode() {
      //发送手机验证码
      let result = await reqSendCode(this.phone);
      if (result.code == 1) {
        this.ShowAlert(result.msg);
        return;
      }
      //验证码触发过等待时间
      this.computeTime = 30;
      //定时器  每一秒减一  当时间为 0时 定时器结束
      let timer = setInterval(() => {
        this.computeTime--;
        if (this.computeTime == 0) {
          clearInterval(timer);
        }
      }, 1000);
    },
    ShowAlert(newAlerttitle) {
      //弹出显示框
      this.isShowAlert = true;
      this.AlertText = newAlerttitle;
    },
    closeAlert() {
      //取消显示框
      this.isShowAlert = false;
    },
    getCaptcha() {
      //更改图形验证码
      // 每次指定的src要不一样
      this.$refs.captcha.src =
        "http://localhost:4000/captcha?time=" + Date.now();
    },
    async login() {
      if (this.loginWay) {
        //密码登录时
        // 手机号错误时
        if (!this.phone) {
          this.ShowAlert("手机号码错误！！！");
          return;
          // 验证码错误时
        } else if (!this.code) {
          this.ShowAlert("手机验证码错误！！！");
          return;
        }
        // 密码登录时判断
      } else if (!this.name) {
        this.ShowAlert("登录账号错误！！！");
        return;
      } else if (!this.pwd) {
        this.ShowAlert("登录密码错误！！！");
        return;
      }
      let result; // 保存登录成功后返回的数据
      // 发送ajax请求密码登陆
      result = await reqPwdLogin({
        name: this.name,
        pwd: this.pwd,
        captcha: this.captcha,
      });
      if (result.code === 0) {
        const user = result.data;
        // 将user保存到vuex的state
        this.$store.dispatch("recordUser", user);
        // 去个人中心界面
        this.$router.replace("/profile");
      } else {
        this.ShowAlert(result.msg);
      }
    },
  },
  mounted() {
    //生命周期  在页面初始时 给图片验证码 更改路径
    this.imgSrc = `http://localhost:4000/captcha`;
  },
};
</script>

<style scoped lang='stylus'>
@import '../assets/stylus/mixins.styl';
@import '../assets/stylus/reset.styl';

.loginContainer {
  width: 100%;
  height: 100%;
  background: #fff;

  .loginInner {
    padding-top: 60px;
    width: 80%;
    margin: 0 auto;

    .login_header {
      .login_logo {
        font-size: 40px;
        font-weight: bold;
        color: $dColor;
        text-align: center;
      }

      .login_header_title {
        padding-top: 40px;
        text-align: center;

        >a {
          color: #333;
          font-size: 14px;
          padding-bottom: 4px;

          &:first-child {
            margin-right: 40px;
          }

          &.on {
            color: $dColor;
            font-weight: 700;
            border-bottom: 2px solid $dColor;
          }
        }
      }
    }

    .login_content {
      >form {
        >div {
          display: none;

          &.on {
            display: block;
          }

          input {
            width: 100%;
            height: 100%;
            padding-left: 10px;
            box-sizing: border-box;
            border: 1px solid #ddd;
            border-radius: 4px;
            outline: 0;
            font: 400 14px Arial;

            &:focus {
              border: 1px solid $dColor;
            }
          }

          .login_message {
            position: relative;
            margin-top: 16px;
            height: 48px;
            font-size: 14px;
            background: #fff;

            .get_verification {
              position: absolute;
              top: 50%;
              right: 10px;
              transform: translateY(-50%);
              border: 0;
              color: #ccc;
              font-size: 14px;
              background: transparent;
            }

            .dd {
              color: #f00;
            }
          }

          .login_verification {
            position: relative;
            margin-top: 16px;
            height: 48px;
            font-size: 14px;
            background: #fff;

            .switch_button {
              font-size: 12px;
              border: 1px solid #ddd;
              border-radius: 8px;
              transition: background-color 0.3s, border-color 0.3s;
              padding: 0 6px;
              width: 30px;
              height: 16px;
              line-height: 16px;
              color: #fff;
              position: absolute;
              top: 50%;
              right: 10px;
              transform: translateY(-50%);

              &.off {
                background: #fff;

                .switch_text {
                  float: right;
                  color: #ddd;
                }
              }

              &.on {
                background: $dColor;
              }

              >.switch_circle {
                // transform translateX(27px)
                position: absolute;
                top: -1px;
                left: -1px;
                width: 16px;
                height: 16px;
                border: 1px solid #ddd;
                border-radius: 50%;
                background: #fff;
                box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.1);
                transition: transform 0.3s;
              }

              .right {
                transform: translateX(27px);
              }
            }
          }

          .login_hint {
            margin-top: 12px;
            color: #999;
            font-size: 14px;
            line-height: 20px;

            >a {
              color: $dColor;
            }
          }
        }

        .login_submit {
          display: block;
          width: 100%;
          height: 38px;
          margin-top: 30px;
          border-radius: 4px;
          background: $dColor;
          color: #fff;
          text-align: center;
          font-size: 16px;
          line-height: 38px;
          border: 0;
        }
      }

      .about_us {
        display: block;
        font-size: 12px;
        margin-top: 20px;
        text-align: center;
        color: #999;
      }
    }

    .go_back {
      position: absolute;
      top: 5px;
      left: 5px;
      width: 30px;
      height: 30px;

      >.iconfont {
        font-size: 20px;
        color: #999;
      }
    }
  }
}
</style>