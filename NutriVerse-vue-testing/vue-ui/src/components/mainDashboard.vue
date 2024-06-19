<script>
import CardProfile from "@/components/cardProfile.vue";
import ProfileContainer from "@/components/ProfileContainer.vue";

export default {
  name: "mainDashboard",
  components: { ProfileContainer, CardProfile },
  data() {
    return {
      addSubr: "",
      userN: "",
      weight: "",
      height: "",
      age: Number,
      gender: "",
      prof : "",
      typeAcc: 4,
      email: "",
      auth_token: null,
      professionist: [
      ],

      patiets: [
      ]
    }
  },
  methods: {
    async function_query(method, uri) {
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

    async function_data() {
      return await this.function_query("GET", "user")
    },

    async function_subscription() {
      return await this.function_query("GET", "subscription")
    },

    async function_subscriber() {
      return await this.function_query("GET", "prouser")
    },

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
      this.function_data()
          .then(json => {
            if (json.status===200){
              const data=json.user;
              this.userN = data.name;
              this.height = data.height;
              this.age = data.age;
              this.gender = data.gender;
              this.email = data.email;
              if(data.weight.length>0){
                this.weight = data.weight[0].value;
              }
              if (data.userType==="ProUser"){
                if (data.Profession !== ""){
                  this.prof = data.Profession
                  this.typeAcc = 2;
                }else{
                  this.typeAcc = 1;
                }
              }else{
                this.typeAcc = 0;
              }
              var tmp =0;
              this.function_subscription()
                  .then(json =>{
                    if(json.status===200){
                      for (tmp in json.subscriptions.subscriptions){
                        this.professionist.push(json.subscriptions.subscriptions[tmp])
                      }
                    }
                  })
              if(this.typeAcc===2) {
                this.function_subscriber()
                    .then(json => {
                      if (json.status === 200) {
                        for (tmp in json.subscribers.subscribers) {
                          this.patiets.push(json.subscribers.subscribers[tmp])
                        }
                      }
                    })
              }
            }else{
              alert("error:" + json.status)
            }
          })
    },
    props: {
      IdCode: String
    }
}
</script>


<template>
  <div id="out_containter">
    <profile-container :email="email" :typeAcc="typeAcc" :user-n="userN" :age="age" :gender="gender" :height="height" :weight="weight" :prof="prof" @logout="logout"/>
    <div id="div_profile_space"></div>

    <div :class="{ 'div_subscriptions': typeAcc===0 || typeAcc===1, 'div_subscriptions_pro' : typeAcc===2 }">
      <div style="height: 8vh">
        <div id="h1_subscriptions_div">
          <h1 id="h1_subscriptions">Subscriptions:</h1>
        </div>
      </div>
        <div id="div_info_card">
            <div v-for="pro in professionist" :key="pro.code" class="card_div">
                <cardProfile :name-p="pro.name" :code="pro.index" :type-p="pro.profession" @removeSub="removeSubscription" @openInteractionDashboard="openInteractionDashboard"/>
            </div>
        </div>
    </div>

    <div id="div_subscriber" v-if="typeAcc===2">
      <div style="height: 8vh">
        <div id="h1_subscriptions_div">
            <h1 id="h1_subscriptions">Subscribers:</h1>
        </div>
      </div>
      <div id="div_info_card">
          <div v-for="pro in patiets" :key="pro.code" class="card_div">
              <cardProfile :name-p="pro.name" :code="pro.index" :type-p="pro.profession" @removeSub="removeSubscriber" @openInteractionDashboard="openInteractionDashboard"/>
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
        <input v-model="addSubr">
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