<template>
  <div class="main-container">
    <div class="form-container">
      <div class="form">
        <v-form ref="form" v-model="valid" lazy-validation>
          <v-text-field
            v-model="url"
            :rules="urlRules"
            label="Text to QR"
            outlined
            required
          ></v-text-field>
          <div v-if="url" class="form-button-container">
            <v-btn outlined @click="cleanUrl">
              <v-icon style="margin-right: 10px"
                >mdi-close-circle-outline</v-icon
              >
              Clean
            </v-btn>
          </div>
        </v-form>
      </div>
    </div>
    <div class="qr-container">
      <img v-if="base64Url" class="qr-image" :src="base64Url" />
      <div class="qr-download-button-container" v-if="base64Url">
        <v-btn outlined @click="downloadImage">
          <v-icon style="margin-right: 10px">mdi-download</v-icon>
          Download</v-btn
        >
      </div>
    </div>
  </div>
</template>

<script>
import QRCode from "qrcode";

export default {
  name: "IndexPage",
  data: () => ({
    valid: true,
    url: "",
    base64Url: "",
    loadingUrl: false,
    urlRules: [(v) => !!v || "Field required"],
  }),
  watch: {
    url() {
      this.createQr();
    },
  },
  methods: {
    cleanUrl() {
      this.url = "";
      this.base64Url = "";
    },
    async createQr() {
      if (this.$refs.form.validate()) {
        try {
          this.loadingUrl = true;
          this.$refs.form.resetValidation();
          this.base64Url = await QRCode.toDataURL(this.url);
          this.loadingUrl = false;
        } catch (error) {
          console.log(error);
          this.loadingUrl = false;
        }
      }
    },
    downloadImage() {
      var a = document.createElement("a"); //Create <a>
      a.href = this.base64Url; //Image Base64 Goes here
      a.download = "MyQr.png"; //File name Here
      a.click();
    },
  },
};
</script>
<style scoped>
.main-container {
  display: flex;
  height: 100vh;
  flex-flow: column;
}
.form-button-container {
  display: flex;
  justify-content: flex-end;
}
.form-container {
  margin: 10px;
  border-radius: 20px;
  padding: 40px 20px 40px 20px;
  display: flex;
  justify-content: center;
}
.form {
  width: 60%;
}
.qr-container {
  display: flex;
  justify-content: center;
  padding: 10px;
  flex: 1;
  align-items: center;
  flex-flow: column;
}
.qr-image {
  width: 250px;
  height: 250px;
}
.qr-download-button-container {
  margin-top: 40px;
}
@media (max-width: 600px) {
  .form {
    width: 90%;
  }
}
</style>
