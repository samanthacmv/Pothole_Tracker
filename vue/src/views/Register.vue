<template>
  <div id="register" class="text-center">
    <form @submit.prevent="register">
      <h1 class="createAcc">Create Account</h1>
      <div role="alert" v-if="registrationErrors">
        {{ registrationErrorMsg }}
      </div>
      <div class="form-input-group">
        <input
          type="email"
          id="username"
          v-model="user.username"
          required
          autofocus
          placeholder="Email Address"
        />
      </div>
      <div class="form-input-group">
        <input
          type="password"
          id="password"
          v-model="user.password"
          required
          placeholder="Password"
        />
      </div>
      <div class="form-input-group">
        <input
          type="password"
          id="confirmPassword"
          v-model="user.confirmPassword"
          required
          placeholder="Confirm Password"
        />
      </div>
      <div class="form-input-group">
        <input
          type="text"
          v-model="user.phoneNumber"
          id="phoneNumber"
          placeholder="###-###-####"
        />
      </div>
      <button id="registerBtn" type="submit">
        Create Account
        <i class="fa-solid fa-circle-plus"></i>
      </button>
      <p>
        <router-link :to="{ name: 'login' }"
          >Already have an account? Log in.</router-link
        >
      </p>
    </form>
  </div>
</template>
<script>
import authService from "../services/AuthService";
export default {
  name: "register",
  data() {
    return {
      user: {
        username: "",
        password: "",
        confirmPassword: "",
        role: "user",
        phoneNumber: "",
      },
      registrationErrors: false,
      registrationErrorMsg: "There were problems registering this user.",
    };
  },
  methods: {
    register() {
      if (this.user.password != this.user.confirmPassword) {
        this.registrationErrors = true;
        this.registrationErrorMsg = "Password & Confirm Password do not match.";
      } else {
        authService
          .register(this.user)
          .then((response) => {
            if (response.status == 201) {
              this.$router.push({
                path: "/login",
                query: { registration: "success" },
              });
            }
          })
          .catch((error) => {
            const response = error.response;
            this.registrationErrors = true;
            if (response.status === 400) {
              this.registrationErrorMsg = "Bad Request: Validation Errors";
            }
          });
      }
    },
    clearErrors() {
      this.registrationErrors = false;
      this.registrationErrorMsg = "There were problems registering this user.";
    },
  },
};
</script>
<style scoped>
.form-input-group {
  margin-bottom: 1rem;
}
label {
  margin-right: 0.5rem;
}
#register {
  text-align: center;
  border-radius: 20px;
  background-color: rgba(128, 128, 128, 0.822);
  width: 35%;
  height: 40vh;
  margin: auto;
}
body {
  padding: 50;
}
.createAcc {
  color: white;
  font-size: 40px;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  text-align: center;
  background-color: transparent;
  max-width: 100%;
}
.form-input-group {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}
#registerBtn {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
}
#registerBtn:hover {
  background-color: #0056b3;
}
</style>










