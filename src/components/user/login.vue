<template>
  <div class="login-wrapper">
  </div>
</template>
<script>
import { Form, FormItem, Select, Option } from 'element-ui'
import logo from '../../assets/image/logo.png'
export default {
  name: 'Login',
  components: {
    ElForm: Form,
    ElFormItem: FormItem,
    ElSelect: Select,
    ElOption: Option,
  },
  data() {
    const checkUserID = (rule, value, callback) => {
      if (!/^[a-zA-Z][a-zA-Z0-9_]{3,23}$/.test(value)) {
        callback(new Error('不合法（字母开头+字母/数字，长度4-24)'))
      } else {
        callback()
      }
    }
    return {
      form: {
        userID: 'user0',
        password: ''
      },
      rules: {
        userID: [
          { required: true, message: '请输入 userID', trigger: 'blur' },
          { validator: checkUserID, trigger: 'blur' }
        ],
        password: [{ required: true, message: '请输入密码', trigger: 'blur' }]
      },
      logo: logo,
      registerVisible: false,
      loading: false
    }
  },
  mounted() {
    let user = this.getQueryString('user');
    let userSig = this.getQueryString('userSig');
    this.form.userID=user;
    this.login();
  },
  methods: {
    getQueryString(name) {
      var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
      var r = window.location.search.substr(1).match(reg);
      if (r != null) return (r[2]);
      return null;
    },
    submit() {
      this.$refs['login'].validate(valid => {
        if (valid) {
          this.login()
        }
      })
    },
    login() {
      this.loading = true
      this.tim
        .login({
          userID: this.form.userID,
          userSig: window.genTestUserSig(this.form.userID).userSig
        })
        .then(() => {
          this.loading = false
          this.$store.commit('toggleIsLogin', true)
          this.$store.commit('startComputeCurrent')
          this.$store.commit('showMessage', {
            type: 'success',
            message: '登录成功'
          })
        })
        .catch(error => {
          this.loading = false
          this.$store.commit('showMessage', {
            message: '登录失败：' + error.message,
            type: 'error'
          })
        })
    },
  }
}
</script>
<style lang="stylus" scoped>
.login-wrapper {
  display: flex;
  align-items: center;
  flex-direction: column;
  width: 400px;
  padding: 20px 80px 50px;
  background: $white;
  color: $black;
  border-radius: 5px;
  box-shadow: 0 11px 20px 0 rgba(0, 0, 0, 0.3);

  .logo {
    width: 130px;
    height: 130px;
  }

  .register-button {
    width: 100%;
    margin: 6px 0 0 0;
  }

  .user-selector {
    width: 100%;
  }
}
</style>