<template>
  <v-app>
    <v-card style="margin:auto;width:50%;" :loading="this.login_data.is_loading">
      <v-card-title>
        <div style="font-size:24px;margin-top:10px" class="mx-auto">欢迎来到叽里咕噜</div>
      </v-card-title>
      <v-card-text>
        <v-text-field v-model="login_data.UserAccount" label="账号" hint="UserAccount" auto-grow></v-text-field>
        <v-text-field
          v-model="login_data.UserPWD"
          label="密码"
          hint="UserPWD"
          type="password"
          auto-grow
        ></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn
          class="mx-auto"
          style="margin-bottom:10px;"
          width="300px"
          color="primary"
          @click="Login()"
        >Login</v-btn>
      </v-card-actions>
    </v-card>
  </v-app>
</template>
<script>
import qs from "qs";

export default {
  name: "Login",
  data() {
    return {
      login_data: {
        UserAccount: "",
        UserPWD: "",
        is_loading: false,
      },
    };
  },
  methods: {
    Login: function () {
      this.is_loading = true;
      this.$axios({
        method: "post",
        url: this.glob.root_path + "/user/Login",
        data: qs.stringify({
          UserAccount: this.login_data.UserAccount,
          UserPWD: this.login_data.UserPWD,
        }),
      }).then((response) => {
        if (response.data != null) {
          this.$root.PublicKey = response.data;
          this.Get_user_data();

          this.is_loading = false;
          this.$root.navi_show = true;

          this.$router.push({ name: "Home" });
        }
      });
    },
    Get_user_data: function () {
      this.$axios({
        method: "post",
        url: this.glob.root_path + "/user/Get_user_data",
        data: qs.stringify({
          Token: this.$encrypt(this.$root.PublicKey, new Date().toISOString()),
        }),
      }).then((response) => {
        this.$root.UserName = response.data.Name;
        this.$root.UserAvatar = response.data.Avatar;
      });
    },
  },
  mounted() {
    this.$root.navi_show = false;
  },
};
</script>