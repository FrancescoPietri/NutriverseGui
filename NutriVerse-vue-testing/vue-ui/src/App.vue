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
      typeAcc: 0
    }
  },
  created() {
    // Recupera i dati dal localStorage quando l'app viene creata
    this.interacting = JSON.parse(localStorage.getItem('interacting')) || false;
    this.IdCode = localStorage.getItem('IdCode') || "ALDKSIFN";
    this.interactionCode = localStorage.getItem('interactionCode') || "";
    this.logged = JSON.parse(localStorage.getItem('logged')) || false;
    this.typeAcc = JSON.parse(localStorage.getItem('typeAcc')) || 0;
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
    typeAcc(newVal) {
      localStorage.setItem('typeAcc', JSON.stringify(newVal));
    }
  },
  methods: {
    login(password) {
      this.logged = true;
      if(password === "premium") {
        this.typeAcc = 1;
      }
      if(password === "pro") {
        this.typeAcc = 2;
      }
    },
    logout() {
      this.typeAcc = 0;
      this.logged = false;
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
  <main-dashboard v-if="logged && !interacting" @logout="logout" @openInteractionDashboard="openInteractionDashboard" :type-acc="typeAcc" :id-code="IdCode"/>
  <interaction-dashboard v-if="interacting" :IdCode="IdCode" :interactionCode="interactionCode" @back="exitInteraction"/>
</template>

<style>
  body {
    background-color: #e8f4fc;
    margin: 0;
    padding: 0;
  }
</style>
