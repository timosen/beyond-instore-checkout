<template>
  <div class="jumbotron">
    <Alerts :alerts="alerts" />

    <p class="lead text-center">Please enter your shop's API credentials:</p>

    <form>
      <div class="form-group">
        <label for="api_url">API URL:</label>
        <input id="api_url" class="form-control"
          v-model="api_url"
          autocomplete="off"
          autocorrect="off"
          autocapitalize="off"
          spellcheck="false"
          placeholder="e.g. https://myshop.beyondshop.cloud/api"
        >
      </div>

      <div class="form-group">
        <label for="client_id">Client ID:</label>
        <input id="client_id" class="form-control"
          v-model="client_id"
          autocomplete="off"
          autocorrect="off"
          autocapitalize="off"
          spellcheck="false"
          placeholder="enter the client_id of your custom app"
        >
      </div>

      <div class="form-group">
        <label for="client_secret">Client Secret:</label>
        <input id="client_secret" class="form-control"
          v-model="client_secret"
          type="password"
          autocomplete="off"
          autocorrect="off"
          autocapitalize="off"
          spellcheck="false"
          placeholder="enter the client_secret of your custom app"
        >
      </div>

      <div class="btn-toolbar">
        <button class="btn btn-primary" v-on:click.prevent="fetchToken">Fetch token</button>
        &nbsp;
        <button class="btn btn-outline-dark" :disabled="!access_token" v-on:click.prevent="clearAccessToken">Clear token</button>
        &nbsp;
        <button class="btn btn-outline-danger" v-on:click.prevent="clearAll">Clear all</button>
      </div>
    </form>
  </div>
</template>

<script>
/* eslint-disable */
import Alerts from "@/components/Alerts";
import AccessTokenMixin from "@/mixins/AccessTokenMixin";
import axios from "axios";

export default {
  name: "Authorize",

  mixins: [AccessTokenMixin],

  components: {
    Alerts
  },

  data: function() {
    return {
      alerts: [],
      client_id: null,
      client_secret: null
    };
  },

  created: function() {
    this.client_id = this.$storage.get("client_id", null);
    this.client_secret = this.$storage.get("client_secret", null);
  },

  methods: {
    clearAll: function() {
      this.clearCredentials();
      this.clearApiUrl();
      this.clearAccessToken();
    },

    clearCredentials: function() {
      this.client_id = null;
      this.client_secret = null;
      console.log("Clearing credentials from LocalStorage");
      this.$storage.remove("client_id");
      this.$storage.remove("client_secret");
    },

    fetchToken: async function() {
      if (!this.api_url || !this.client_id || !this.client_secret) {
        this.alerts.push({ message: "Please enter your credentials" });
        return;
      }

      this.persistApiUrl();
      this.$storage.set("client_id", this.client_id);
      this.$storage.set("client_secret", this.client_secret);

      const postOauthToken = {
        baseURL: this.api_url,
        timeout: 5000,
        method: "POST",
        url: "/oauth/token",
        params: {
          grant_type: "client_credentials"
        },
        auth: {
          username: this.client_id,
          password: this.client_secret
        }
      };

      axios
        .request(postOauthToken)
        .then(response => {
          this.access_token = {
            bearer: response.data.access_token,
            // (issued at + expires in) converted to milliseconds,
            // see https://en.wikipedia.org/wiki/JSON_Web_Token#Standard_fields
            expiry: (response.data.iat + response.data.expires_in) * 1000
          };
          this.persistAccessToken();
        })
        .catch(e => {
          console.error(e);
          this.alerts.push({ message: "error fetching token" });
        });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
