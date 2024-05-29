<script>
import CardProfile from "@/components/cardProfile.vue";
import ProfileContainer from "@/components/ProfileContainer.vue";

export default {
  name: "mainDashboard",
  components: { ProfileContainer, CardProfile },
  data() {
    return {
      auth_token: null,
      professionist: [
        { name: "patrick", code: "AAAAAAAA", typeP: "N" },
        { name: "tester", code: "AGCDEFGHI", typeP: "PT" },
        { name: "tester", code: "ASCDEFGHI", typeP: "PT/N" },
        { name: "tester", code: "AACDEFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABGDEFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABCHEFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABADEFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABCDHFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABCEEFGHI", typeP: "PT/N" },
      ],

      patiets: [
        { name: "gennaro", code: "AAAAAAAA", typeP: "N" },
        { name: "tester", code: "AGCDEFGHI", typeP: "PT/N" },
        { name: "tester", code: "ASCDEFGHI", typeP: "PT/N" },
        { name: "tester", code: "AACDEFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABGDEFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABCHEFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABADEFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABCDHFGHI", typeP: "PT/N" },
        { name: "tester", code: "ABCEEFGHI", typeP: "PT/N" },
      ]
    }
  },
  methods: {
    logout() {
      this.$emit("logout");
    },
    removeSubscription(code) {
      this.professionist = this.professionist.filter(pro => pro.code !== code);
    },
    removeSubscriber(code) {
      this.patiets = this.patiets.filter(pro => pro.code !== code);
    },
    openInteractionDashboard(code) {
      this.$emit("openInteractionDashboard", code);
    },
    getCookie(name) {
      const value = `; ${document.cookie}`;
      const parts = value.split(`; ${name}=`);
      if (parts.length === 2) return parts.pop().split(';').shift();
    }
    },
    created() {
      this.auth_token = this.getCookie('auth_token');

    },
    props: {
      typeAcc: Number,
      IdCode: String
    }
}
</script>


<template>
  <div id="out_containter">
    <profile-container :typeAcc="typeAcc" @logout="logout"/>
    <div id="div_profile_space"></div>

    <div :class="{ 'div_subscriptions': typeAcc===0 || typeAcc===1, 'div_subscriptions_pro' : typeAcc===2 }">
      <div style="height: 8vh">
        <div id="h1_subscriptions_div">
          <h1 id="h1_subscriptions">Subscriptions:</h1>
        </div>
      </div>
        <div id="div_info_card">
            <div v-for="pro in professionist" :key="pro.code" class="card_div">
                <cardProfile :name-p="pro.name" :code="pro.code" :type-p="pro.typeP" @removeSub="removeSubscription" @openInteractionDashboard="openInteractionDashboard"/>
            </div>
        </div>
    </div>

    <div id="div_subscriber" v-if="typeAcc===2">
      <div style="height: 8vh">
        <div id="h1_subscriptions_div">
            <h1 id="h1_subscriptions">Subscriber:</h1>
        </div>
      </div>
      <div id="div_info_card">
          <div v-for="pro in patiets" :key="pro.code" class="card_div">
              <cardProfile :name-p="pro.name" :code="pro.code" :type-p="pro.typeP" @removeSub="removeSubscriber" @openInteractionDashboard="openInteractionDashboard"/>
          </div>
      </div>
    </div>

    <div id="div_button_menu">
        <button class="maindash_button" id="but_money" v-if="typeAcc!==0">
          <img alt="Error" src="@/assets/catIcon.png" style="width: 80%; height: 80%">
        </button>
        <button class="maindash_button" id="but_money">
          <img alt="Error" src="@/assets/dollarIcon.png" style="width: 80%; height: 80%" v-if="typeAcc===0">
          <img alt="Error" src="@/assets/downgrade.png" style="width: 80%; height: 80%" v-if="typeAcc!==0">
        </button>
        <button class="maindash_button" id="but_report">
          <img alt="Error" src="@/assets/reportIcon.png" style="width: 80%; height: 80%">
        </button>
        <button class="maindash_button" id="but_plus">
          <img alt="Error" src="@/assets/plusIcon.png" style="width: 80%; height: 80%">
        </button>
      </div>

  </div>
</template>

<style scoped>

  #div_profile_space{
    width: 30vw;
    margin-left: 4px;
    height: 100%;
  }

  #div_subscriber{
    width: 35vw;
    height: 100%;
    background-color: #e8f4fc;
    overflow: scroll;
    border-left: 2px solid black;
  }

  .div_subscriptions {
    width: 70vw;
    height: 100%;
    overflow: scroll;
    background-color: #e8f4fc;
  }

  .div_subscriptions_pro {
    width: 35vw;
    height: 100%;
    overflow: scroll;
    background-color: #e8f4fc;
  }

  #but_money{
    background-color: #ffdc5c;
    border: 1px solid black;
    transition: background-color 0.3s ease;
  }

  #but_money:hover{
    background-color: #ffac24;
    cursor: pointer;
  }

  #but_report{
    background-color: #cf1f02;
    border: 1px solid black;
    transition: background-color 0.3s ease;
  }

  #but_report:hover{
    background-color: #a61902;
    cursor: pointer;
  }

  #but_plus{
    background-color: #b8e464;
    border: 1px solid black;
    transition: background-color 0.3s ease;
  }

  #but_plus:hover{
    background-color: #93b650;
    cursor: pointer;
  }

  .maindash_button{
    width: 12vh;
    height: 12vh;
    border-radius: 50%;
    border: 1px solid black;
    transition: background-color 0.3s ease;
    margin-right: 2vw;
    overflow: hidden;
  }

  #div_button_menu{
    position: fixed;
    margin-bottom: 2vw;
    bottom: 0;
    right: 0;
  }

  #h1_subscriptions_div{
    position: fixed;
    background-color: #e8f4fc;
    border-bottom: 2px solid black;
    border-bottom: 2px solid black;
    width: 100%
  }

  .card_div {
    margin-top: 3vh;
    margin-left: 2vw;
    width: 14vw;
    height: 40vh;
  }

  #div_info_card {
    display: flex;
    flex-wrap: wrap;
    justify-content: flex-start;
  }

  #h1_subscriptions{
    margin-left: 3vw;
    font-family: 'Stinger Fit Trial', sans-serif;
    font-size: 6vh;
  }

  #out_containter {
    display: flex;
    width: 100vw;
    height: 100vh;
  }

</style>