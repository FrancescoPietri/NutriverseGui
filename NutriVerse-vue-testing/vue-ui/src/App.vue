<script>
import InformationPage from "@/components/informationPage.vue";
import MainDashboard from "@/components/mainDashboard.vue";
import InteractionDashboard from "@/components/interactionDashboard.vue";
export default {
  name: 'App',
  data() {
    return {
      interacting: false,
      IdCode: "ALDKSIFN",
      interactionCode: "",
      logged: false,
    }
  },

  async function_query(method, uri, data) {
    console.log("Querying " + "https://nutriverse.onrender.com/api/v1/" + uri)
    const headers = {
      'Content-Type': 'application/json',
      'Access-Control-Allow-Origin': '*',
    }
    const response = await fetch("https://nutriverse.onrender.com/api/v1/" + uri, {
      method: method,
      headers: headers,
      body: JSON.stringify(data),
      cache: 'no-cache',
    })
    return await response.json()
  },

  async function_verify(token) {
    return await this.function_query("PUT", "user", { token })
  },

  created() {
    const urlParams = new URLSearchParams(window.location.search);
    const token = urlParams.get('token');
    alert(token)
    if (token) {
      this.authToken = token;
      localStorage.setItem('authToken', token);
      let expires = new Date();
      expires.setTime(expires.getTime() + 3600 * 1000);
      document.cookie = `auth_token=${token}; expires=${expires.toUTCString()}; path=/`;
      this.function_verify(token);
      this.logged=true;
    } else {
      // Carica il token dal localStorage se esiste
      this.authToken = localStorage.getItem('authToken') || "";
    }

    // Recupera i dati dal localStorage quando l'app viene creata
    this.interacting = JSON.parse(localStorage.getItem('interacting')) || false;
    this.IdCode = localStorage.getItem('IdCode') || "ALDKSIFN";
    this.interactionCode = localStorage.getItem('interactionCode') || "";
    this.logged = JSON.parse(localStorage.getItem('logged')) || false;
  },
  watch: {
    interacting(newVal) {
      localStorage.setItem('interacting', JSON.stringify(newVal));
    },
    IdCode(newVal) {
      localStorage.setItem('IdCode', newVal);
    },
    interactionCode(newVal) {
      localStorage.setItem('interactionCode', newVal);
    },
    logged(newVal) {
      localStorage.setItem('logged', JSON.stringify(newVal));
    },
  },
  methods: {
    login() {
      this.logged = true;
    },
    logout() {
      this.logged = false;
      document.cookie = "auth_token=; path=/; expires=Thu, 01 Jan 1970 00:00:00 UTC;";
    },
    openInteractionDashboard(code) {
      this.interacting = true;
      this.interactionCode = code;
    },
    exitInteraction() {
      this.interacting = false;
    }
  },
  components: {
    InteractionDashboard,
    MainDashboard,
    InformationPage
  }
}
</script>

<template>
  <information-page v-if="!logged" @logged="login"/>
  <main-dashboard v-if="logged && !interacting" @logout="logout" @openInteractionDashboard="openInteractionDashboard" :id-code="IdCode"/>
  <interaction-dashboard v-if="interacting" :IdCode="IdCode" :interactionCode="interactionCode" @back="exitInteraction"/>
</template>

<style>
  body {
    background-color: #e8f4fc;
    margin: 0;
    padding: 0;
  }
</style>
