<script>
import InformationPage from "@/components/informationPage.vue";
import MainDashboard from "@/components/mainDashboard.vue";
import InteractionDashboard from "@/components/interactionDashboard.vue";
import StatsPage from "@/components/StatsPage.vue";
export default {
  name: 'App',
  data() {
    return {
      interacting: false,
      interactionCode: "",
      IdCode: "",
      logged: false,
      auth_token: "",
      statsPage: false,
      email: "",
      boolSubr: "",
    }
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
    statsPage(newVal) {
      localStorage.setItem('statsPage', JSON.stringify(newVal))
    }
  },

  methods: {

    async function_query(method, uri, data) {
      console.log("Querying " + "https://nutriverse.onrender.com/api/v1/" + uri)
      const headers = {
        'auth-token': this.auth_token,
        'Contefunction_verifynt-Type': 'application/json',
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

    async function_verify(confirmationToken) {
      return await this.function_query("PUT", `user/${confirmationToken}`)
    },

    login() {
      this.logged = true;
    },
    logout() {
      this.logged = false;
      document.cookie = "auth_token=; path=/; expires=Thu, 01 Jan 1970 00:00:00 UTC;";
      localStorage.clear();
    },
    openInteractionDashboard(email, YourEmail, boolSubr) {
      this.interacting = true;
      this.interactionCode = email;
      this.IdCode = YourEmail;
      this.boolSubr = boolSubr;
    },
    openStats(email, auth_token){
      this.statsPage=true;
      this.email=email;
      this.auth_token=auth_token;
    },
    exitInteraction() {
      this.interacting = false;
      this.statsPage = false;
    }
  },

  created() {
    // Recupera i dati dal localStorage quando l'app viene creata
    this.interacting = JSON.parse(localStorage.getItem('interacting')) || false;
    this.IdCode = localStorage.getItem('IdCode') || "ALDKSIFN";
    this.interactionCode = localStorage.getItem('interactionCode') || "";
    this.logged = JSON.parse(localStorage.getItem('logged')) || false;
    this.statsPage = JSON.parse(localStorage.getItem('statsPage')) || false;

    const urlParams = new URLSearchParams(window.location.search);
    const token = urlParams.get('token');
    if (token) {
      this.auth_token = token;
      let expires = new Date();
      expires.setTime(expires.getTime() + 3600 * 1000);
      document.cookie = `auth_token=${token}; expires=${expires.toUTCString()}; path=/`;
      this.function_verify(token);
      this.logged=true;
    }
  },

  components: {
    StatsPage,
    InteractionDashboard,
    MainDashboard,
    InformationPage
  }
}
</script>

<template>
  <information-page v-if="!logged" @logged="login"/>
  <main-dashboard v-if="logged && !interacting && !statsPage" @logout="logout" @openInteractionDashboard="openInteractionDashboard" @openInteractionDashboardSubr="openInteractionDashboard" @openStats="openStats" :id-code="IdCode"/>
  <interaction-dashboard v-if="interacting" :IdMail="IdCode" :InteractionEmail="interactionCode" :Subscriber="boolSubr" @back="exitInteraction"/>
  <stats-page v-if="statsPage" :email="email" @back="exitInteraction"/>
</template>

<style>
  body {
    background-color: #e8f4fc;
    margin: 0;
    padding: 0;
  }
</style>
