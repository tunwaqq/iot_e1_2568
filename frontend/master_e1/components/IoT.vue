<template>
  <v-container class="pa-6" fluid>
    <v-row justify="center">
      <v-col cols="12" md="6">
        <v-card
          class="pa-6 elevation-6 rounded-xl"
          outlined
          hover
        >
          <!-- Title + Icon -->
          <v-card-title class="justify-center">
            <v-icon size="36" color="blue">mdi-lightbulb-on-outline</v-icon>
            <span class="text-h6 font-weight-bold ml-3">สถานะอุปกรณ์</span>
          </v-card-title>

          <v-divider class="my-4"></v-divider>

          <!-- Status -->
          <v-card-text class="text-center">
            <v-row justify="center" align="center">
              <v-col cols="12">
                <v-chip
                  :color="sensor.deviceState ? 'green lighten-1' : 'red lighten-1'"
                  dark
                  large
                  pill
                >
                  {{ sensor.deviceState ? 'เปิด' : 'ปิด' }}
                </v-chip>
              </v-col>
            </v-row>
          </v-card-text>

          <!-- Buttons -->
          <v-card-actions class="justify-center">
            <v-btn
              color="success"
              dark
              large
              @click="toggleDevice(true)"
              class="mx-2"
            >
              <v-icon left>mdi-power-plug</v-icon>
              เปิดไฟ
            </v-btn>
            <v-btn
              color="error"
              dark
              large
              @click="toggleDevice(false)"
              class="mx-2"
            >
              <v-icon left>mdi-power-off</v-icon>
              ปิดไฟ
            </v-btn>
          </v-card-actions>

          <!-- Go to showdata -->
          <v-card-actions class="justify-center mt-4">
            <v-btn text color="blue" @click="goshow">
              ดูข้อมูลเพิ่มเติม
              <v-icon right>mdi-arrow-right</v-icon>
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      sensor: {
        deviceState: false,
      },
    };
  },
  mounted() {
    this.fetchSensor();
  },
  methods: {
    async fetchSensor() {
      try {
        const res = await axios.get("http://localhost:7000/sensor");
        this.sensor = res.data;
      } catch (err) {
        console.error("ดึง sensor ไม่สำเร็จ:", err);
      }
    },
    async toggleDevice(state) {
      try {
        await axios.post("http://localhost:7000/device", { state });
        setTimeout(() => this.fetchSensor(), 1000);
      } catch (err) {
        console.error("ส่งคำสั่งไม่สำเร็จ:", err);
      }
    },
    goshow() {
      this.$router.push("/showdata");
    },
  },
};
</script>

<style scoped>
.v-card:hover {
  transform: translateY(-4px);
  transition: 0.3s;
}
</style>
