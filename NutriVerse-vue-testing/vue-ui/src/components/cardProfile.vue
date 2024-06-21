<script>
import BoxProfileIcon from "@/components/boxProfileIcon.vue";

export default {
  name: "cardProfile",
  components: {BoxProfileIcon},
  data(){
    return{
      isExternalButtonDisabled: false,
    }
  },
  props: {
    email: String,
    nameP: String,
    typeP: String,
    auth_token: String,
  },
  methods: {
    async function_query_post(method, uri) {
      console.log("Querying " + "https://nutriverse.onrender.com/api/v1/" + uri)
      const headers = {
        'auth-token': this.auth_token,
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*',
      }
      const response = await fetch("https://nutriverse.onrender.com/api/v1/" + uri, {
        method: method,
        headers: headers,
        cache: 'no-cache',
      })
      return await response.json()
    },

    async function_remove(email) {
      return await this.function_query_post("DELETE", `subscription/${email}`)
    },

    openStats(email, auth_token){
      this.$emit("openStats", email, auth_token)
    },

    removeSub() {
      this.function_remove(this.email);
    },
    openInteractionDashboard() {
      this.$emit("openInteractionDashboard", this.email);
    },

    disableExternalButton(){
      this.isExternalButtonDisabled=true;
    },
    enableExternalButton(){
      this.isExternalButtonDisabled=false;
    },
  }
}
</script>

<template>
  <button class="button_card_profile" @click="openInteractionDashboard" :disabled="isExternalButtonDisabled">
    <div id="container_card">
      <button class="close_button" @click="removeSub" @mouseover="disableExternalButton" @mouseout="enableExternalButton">
        <img src="@/assets/closeButton.png" style="width: 100%; height: 100%"/>
      </button>
      <box-profile-icon :email="email" style="width: 8.3vw;" @openStats="openStats"/>
      <div id="div_info_card">
        <div class="show_info">
          <h2 class="h2_card" > Email: {{ email }}</h2>
        </div>
        <div class="show_info">
          <h2 class="h2_card"> Name: {{nameP}}</h2>
        </div>
        <div class="show_info">
          <h2 class="h2_card"> Status: {{typeP}}</h2>
        </div>
      </div>
    </div>
  </button>
</template>

<style scoped>

  .button_card_profile{
    background-color: transparent;
    border: none;
    height: 33vh;
  }

  .close_button{
    margin-top: 1vh;
    margin-left: -11.5vw;
    border-radius: 30%;
    border:none;
    background-color: transparent;
    width: 2vw;
    transition: background-color 0.3s ease;
  }

  .close_button:hover {
    background-color: #844034;
    cursor: pointer;
  }

  #div_info_card {
    margin-top: 3vh;
    display: flex;
    flex-direction: column;
    align-items: center; /* Per centrare verticalmente */
    justify-content: center; /* Per centrare orizzontalmente */
  }

  .show_info {
      display: flex;
      margin-top: -2vh;
  }

  .h2_card {
    font-family: verdana;
    font-size: 16px;
  }


  #div_card_pic {
    display: flex;
    width: 100%;
    height: 20vh;
    border-radius: 50%;
    margin-top: -2vh;
    align-items: center;
    justify-content: center;
  }

  #div_card_pic img {
    width: 20vh;
    height: 20vh;
    border-radius: 50%;
    background-color: #e8f4fc;
  }

  #container_card {
      display: block;
      width: 14vw;
      height: 100%;
      margin-top: 5vh;
      margin-left: 2vw;
      background-color: #4d8330;
      border: 1px solid black;
      align-items: center; /* Per centrare verticalmente */
      justify-content: center; /* Per centrare orizzontalmente */
      transition: background-color 0.3s ease;
      cursor: pointer;
  }

  #container_card:hover {
    background-color: #73a25a;
  }
</style>