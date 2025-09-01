<template>
  <v-app>
    <v-main>
      <div class="login-screen">
        <v-card elevation="6" class="login-card">
          <!-- Title -->
          <v-card-title class="login-title">
            <v-icon size="36" color="#1976d2" class="mr-2">mdi-lock-outline</v-icon>
            Login
          </v-card-title>

          <v-card-text>
            <v-form ref="form" @submit.prevent="doLogin">
              <v-text-field
                v-model="email"
                label="E-mail"
                prepend-inner-icon="mdi-email-outline"
                outlined dense
                required
              ></v-text-field>

              <v-text-field
                v-model="password"
                label="Password"
                prepend-inner-icon="mdi-lock-outline"
                type="password"
                outlined dense
                required
              ></v-text-field>

              <v-btn
                class="mt-4"
                block
                color="#1976d2"
                dark
                rounded
                type="submit"
              >
                Login
              </v-btn>
            </v-form>
          </v-card-text>

          <v-card-actions class="justify-center mt-2">
            <small>
              Don’t have an account? 
              <a href="#" class="signup-link">Sign Up</a>
            </small>
          </v-card-actions>

          <!-- Snackbar -->
          <v-snackbar
            v-model="snackbar.show"
            :color="snackbar.color"
            timeout="2000"
            top
            elevation="6"
          >
            {{ snackbar.text }}
            <template v-slot:action="{ attrs }">
              <v-btn icon text v-bind="attrs" @click="snackbar.show = false">
                <v-icon>mdi-close</v-icon>
              </v-btn>
            </template>
          </v-snackbar>
        </v-card>
      </div>
    </v-main>
  </v-app>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      email: "",
      password: "",
      snackbar: { show: false, text: "", color: "success" },
    };
  },
  methods: {
    async doLogin() {
      if (!this.email || !this.password) {
        this.snackbar = { show: true, text: "กรุณากรอกอีเมลและรหัสผ่าน", color: "error" };
        return;
      }

      try {
        const response = await axios.post("http://localhost:7000/login", {
          email: this.email,
          password: this.password,
        });

        if (response.data.status === "ok" && response.data.token) {
          localStorage.setItem("token", response.data.token);
          this.snackbar = { show: true, text: "เข้าสู่ระบบสำเร็จ!", color: "success" };
          setTimeout(() => this.$router.push("/dashboard"), 1500);
        } else {
          this.snackbar = { show: true, text: "อีเมลหรือรหัสผ่านไม่ถูกต้อง", color: "error" };
          this.reset();
        }
      } catch (error) {
        console.error("Login error:", error);
        this.snackbar = { show: true, text: "ไม่สามารถเชื่อมต่อกับเซิร์ฟเวอร์ได้", color: "error" };
      }
    },
    reset() {
      this.email = "";
      this.password = "";
    },
  },
};
</script>

<style scoped>
html, body, #app {
  height: 100%;
  margin: 0;
  font-family: 'Roboto', sans-serif;
}

.login-screen {
  width: 100vw;
  height: 100vh;
  background-image: url('/winter.jpg');
  background-size: cover;
  background-position: center;
  display: flex;
  justify-content: center;
  align-items: center;
}

.login-card {
  width: 380px;
  max-width: 90vw;
  padding: 32px 24px;
  border-radius: 16px;
  background-color: rgba(255,255,255,0.95);
  box-shadow: 0 12px 24px rgba(0,0,0,0.3);
  backdrop-filter: blur(8px);
  transition: transform 0.2s, box-shadow 0.2s;
}

.login-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 16px 32px rgba(0,0,0,0.35);
}

.login-title {
  justify-content: center;
  font-weight: 600;
  font-size: 1.5rem;
  margin-bottom: 16px;
}

.signup-link {
  color: #1976d2;
  text-decoration: none;
  font-weight: 500;
}

.signup-link:hover {
  text-decoration: underline;
}
</style>
