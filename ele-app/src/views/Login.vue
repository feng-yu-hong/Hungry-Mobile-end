<template>
  <div class="login">
    <div class="logo">
      <img src="../assets/logo.jpg" alt="my login image" />
    </div>
    <!-- 手机号 -->
    <InputGroup
      type="number"
      v-model="phone"
      placeholder="手机号"
      :btnTitle="btnTitle"
      :disabled="disabled"
      :error="errors.phone"
      @btnClick="getVerifyCode"
    />
    <!-- 验证码 -->
    <InputGroup type="number" v-model="verifyCode" placeholder="验证码" :error="errors.code" />
    <!-- 用户服务协议 -->
    <div class="login_des">
      <p>
        新用户登录即自动注册，表示已同意
        <span>《用户服务协议》</span>
      </p>
    </div>
    <!-- 登录按钮 -->
    <div class="login_btn">
      <button :disabled="isClick" @click="handlelogin">登录</button>
    </div>
  </div>
</template>


<script>
import InputGroup from "../components/InputGroup";
export default {
  name: "login",
  data() {
    return {
      phone: "",
      verifyCode: "",
      errors: {},
      btnTitle: "获取验证码",
      disabled: false
    };
  },
  computed: {
    isClick() {
      if (!this.phone || !this.verifyCode) return true;
      else return false;
    }
  },
  methods: {
    handlelogin() {
      //取消错误提醒
      this.errors = {};
      //发送请求
      this.$axios
        .post("/api/posts/sms_back", {
          phone: this.phone,
          code: this.verifyCode
        })
        .then(res => {
          console.log(res);
          //检验成功，设置登录状态并且跳转到
          localStorage.setItem("ele_login", res.data.user._id);
          this.$router.push("/");
        })
        .catch(err => {
          //返回错误信息
          this.errors = {
            code: err.response.data.msg
          };
        });
    },
    getVerifyCode() {
      if (this.validatePhone()) {
        // 发送网络请求
        this.$axios
          .post("/api/posts/sms_send", {
            // tpl_id: "213577",
            // key: "614a24c10a35fd5ae6e0c8ed771c50b4",
            // phone: this.phone
            sid: "6e8bbab081dd179a9cdcc8b69754bb20",
            token: "c3f0d427c57345f8622e9a6603986e80",
            appid: "e5665478e5314176bf6452121c095046",
            templateid:"548865",
            phone: this.phone
          })
          .then(res => {
            console.log(res);
            this.validateBtn();
          });
      }
    },
    validatePhone() {
      // 验证手机号码
      if (!this.phone) {
        this.errors = {
          phone: "手机号码不能为空！"
        };
        return false;
      } else if (!/^1[345678]\d{9}$/.test(this.phone)) {
        this.errors = {
          phone: "请填写正确的手机号码！"
        };
        return false;
      } else {
        this.errors = {};
        return true;
      }
    },
    validateBtn() {
      // 倒计时
      let time = 60;
      let timer = setInterval(() => {
        if (time == 0) {
          clearInterval(timer);
          this.btnTitle = "获取验证码";
          this.disabled = false;
        } else {
          // 倒计时
          this.btnTitle = time + "秒后重试";
          this.disabled = true;
          time--;
        }
      }, 1000);//清除定时器
    }
  },
  components: {
    InputGroup
  }
};
</script>


<style scoped>
.login {
  width: 100%;
  height: 100%;
  padding: 30px;
  box-sizing: border-box;
  background: #fff;
}

.logo {
  text-align: center;
}
.logo img {
  width: 150px;
}
.text_group,
.login_des,
.login_btn {
  margin-top: 20px;
}
.login_des {
  color: #aaa;
  line-height: 22px;
}
.login_des span {
  color: #4d90fe;
}
.login_btn button {
  width: 100%;
  height: 40px;
  background-color: #48ca38;
  border-radius: 4px;
  color: white;
  font-size: 14px;
  border: none;
  outline: none;
}
.login_btn button[disabled] {
  background-color: #8bda81;
}
</style>
