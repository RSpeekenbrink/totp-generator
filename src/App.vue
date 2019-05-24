<template>
  <div id="app" class="section">
    <div class="container">
      <h1 class="title">
        TOTP Generator
      </h1>

      <div class="field">
        <label class="label">Secret Key</label>
        <div class="control">
          <input class="input" type="text" placeholder="Secret (base-32)" v-model="secret" @input="refreshToken">
        </div>
      </div>

      <div class="field">
        <label class="label">Token Period (Seconds)</label>
        <div class="control">
          <input class="input" type="number" placeholder="Token Period (Seconds)" v-model="tokenPeriod">
        </div>
      </div>

      <div class="content">
        <span class="has-text-grey is-size-7">{{ tokenPeriod - currentPeriod }}</span>
        <progress class="progress is-info" :value="currentPeriod" :max="tokenPeriod">{{ currentPeriod / tokenPeriod * 100  }}%</progress>
      </div>

      <div class="box">
        <p class="title is-size-1 has-text-centered">{{ totpToken }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data: function () {
    return {
      secret: null,
      tokenPeriod: 30,
      currentPeriod: 0,
      updateHandle: null,
      totpToken: null
    };
  },
  mounted: function () {
    this.updateHandle = setInterval(this.update, 1000);
  },
  methods: {
    update: function() {
        this.currentPeriod = (this.getCurrentTimeInSeconds() % this.tokenPeriod);
        this.refreshToken();
    },
    refreshToken: function() {
      if (this.secret) {
        var totp = new window.jsOTP.totp();

        this.totpToken = totp.getOtp(this.secret);
      }
    },
    getCurrentTimeInSeconds: function() {
      return Math.round(new Date().getTime() / 1000.0);
    }
  }
}
</script>

<style lang="scss">
  @import "~bulma";

  .container {
    max-width: 600px;
  }
</style>
