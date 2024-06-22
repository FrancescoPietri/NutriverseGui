<script>
import CardProfile from "@/components/cardProfile.vue";
import ProfileContainer from "@/components/profileContainer.vue";
import CardProfileReq from "@/components/cardProfileReq.vue";
import insertEmail from "@/components/insertEmail.vue";

export default {
  name: "mainDashboard",
  components: { ProfileContainer, CardProfile, CardProfileReq, insertEmail },
  data() {
    return {
      addSubr: "",
      userN: "",
      weight: "",
      height: "",
      age: Number,
      gender: "",
      prof: "",
      newProfession: "",
      typeAcc: 4,
      email: "",
      auth_token: null,
      isAdding: false,
      isReporting: false,

      professionist: [],
      patiets: [],
      requestsSub: [],
    };
  },
  methods: {
    async function_query(method, uri) {
      console.log(
        "Querying " + "https://nutriverse.onrender.com/api/v1/" + uri,
      );
      const headers = {
        "auth-token": this.auth_token,
        "Content-Type": "application/json",
        "Access-Control-Allow-Origin": "*",
      };
      const response = await fetch(
        "https://nutriverse.onrender.com/api/v1/" + uri,
        {
          method: method,
          headers: headers,
          cache: "no-cache",
        },
      );
      return await response.json();
    },

    async function_query_post(method, uri, data) {
      console.log(
        "Querying " + "https://nutriverse.onrender.com/api/v1/" + uri,
      );
      const headers = {
        "auth-token": this.auth_token,
        "Content-Type": "application/json",
        "Access-Control-Allow-Origin": "*",
      };
      const response = await fetch(
        "https://nutriverse.onrender.com/api/v1/" + uri,
        {
          method: method,
          headers: headers,
          body: JSON.stringify(data),
          cache: "no-cache",
        },
      );
      return await response.json();
    },

    async function_upgrade(Profession) {
      return await this.function_query_post("POST", "prouser", { Profession });
    },

    async function_downgrade() {
      return await this.function_query("DELETE", "prouser");
    },

    async function_data() {
      return await this.function_query("GET", "user");
    },

    async function_subscription() {
      return await this.function_query("GET", "subscription");
    },

    async function_subscriber() {
      return await this.function_query("GET", "prouser");
    },

    async function_addSubReq(email) {
      return await this.function_query_post("POST", "subscription", { email });
    },

    logout() {
      this.$emit("logout");
    },

    openInteractionDashboard(email) {
      this.$emit("openInteractionDashboard", email, this.email, false);
    },
    openInteractionDashboardSubr(email) {
      this.$emit("openInteractionDashboardSubr", email, this.email, true);
    },
    getCookie(name) {
      const value = `; ${document.cookie}`;
      const parts = value.split(`; ${name}=`);
      if (parts.length === 2) return parts.pop().split(";").shift();
    },

    sendSubRequest() {
      this.function_addSubReq(this.addSubr);
    },

    openStats(email, auth_token) {
      this.$emit("openStats", email, auth_token);
    },

    openAddSub() {
      this.isAdding = !this.isAdding;
      this.isReporting = false;
    },

    openReport() {
      this.isReporting = !this.isReporting;
      this.isAdding = false;
    },

    changePlan() {
      if (this.typeAcc === 0) {
        this.function_upgrade(this.newProfession).then((json) => {
          this.$emit("logout");
        });
      } else {
        this.function_downgrade().then((json) => {
          this.$emit("logout");
        });
      }
    },
  },
  created() {
    this.auth_token = this.getCookie("auth_token");
    this.function_data().then((json) => {
      if (json.status === 200) {
        const data = json.user;
        this.userN = data.name;
        this.height = data.height;
        this.age = data.age;
        this.gender = data.gender;
        this.email = data.email;
        if (data.weight.length > 0) {
          this.weight = data.weight[data.weight.length - 1].value;
        }
        if (data.userType === "ProUser") {
          if (data.Profession !== "Premium User") {
            this.prof = data.Profession;
            this.typeAcc = 2;
          } else {
            this.typeAcc = 1;
          }
        } else {
          this.typeAcc = 0;
        }
        var tmp = 0;
        this.function_subscription().then((json) => {
          if (json.status === 200) {
            for (tmp in json.subscriptions.subscriptions) {
              this.professionist.push(json.subscriptions.subscriptions[tmp]);
            }
          }
        });
        if (this.typeAcc === 2) {
          this.function_subscriber().then((json) => {
            if (json.status === 200) {
              console.log(json);
              for (tmp in json.subscribers) {
                this.patiets.push(json.subscribers[tmp]);
              }
              for (tmp in json.requests) {
                this.requestsSub.push(json.requests[tmp]);
              }
            }
          });
        }
      } else {
        alert("error:" + json.status);
      }
    });
  },
  props: {
    IdCode: String,
  },
};
</script>

<template>
  <div id="out_containter">
    <profile-container
      :email="email"
      :typeAcc="typeAcc"
      :user-n="userN"
      :age="age"
      :gender="gender"
      :height="height"
      :weight="weight"
      :prof="prof"
      @logout="logout"
      @openStats="openStats"
    />
    <div id="div_profile_space"></div>

    <div
      :class="{
        div_subscriptions: typeAcc === 0 || typeAcc === 1,
        div_subscriptions_pro: typeAcc === 2,
      }"
    >
      <div style="height: 8vh">
        <div id="h1_subscriptions_div">
          <h1 id="h1_subscriptions">Subscriptions:</h1>
        </div>
      </div>
      <div id="div_info_card">
        <div v-for="pro in professionist" :key="pro.code" class="card_div">
          <cardProfile
            :auth_token="auth_token"
            :name-p="pro.name"
            :email="pro.email"
            :type-p="pro.profession"
            @openInteractionDashboard="openInteractionDashboard"
          />
        </div>
      </div>
    </div>

    <div id="div_subscriber" v-if="typeAcc === 2">
      <div style="height: 8vh">
        <div id="h1_subscriptions_div">
          <h1 id="h1_subscriptions">Subscribers:</h1>
        </div>
      </div>
      <div id="div_info_card">
        <div v-for="pro in patiets" :key="pro.code" class="card_div">
          <cardProfile
            @openStats="openStats"
            :auth_token="auth_token"
            :name-p="pro.name"
            :email="pro.email"
            :type-p="pro.profession"
            @openInteractionDashboard="openInteractionDashboardSubr"
          />
        </div>
        <div v-for="pro in requestsSub" :key="pro.email" class="card_div">
          <cardProfileReq :email="pro.email" :auth_token="auth_token" />
        </div>
      </div>
    </div>

    <div id="div_button_menu">

      <transition name="slide">
        <div
          :class="{
            insEmail: isAdding === true,
            insEmailReport: isReporting === true,
          }"
          v-if="isAdding || isReporting"
        >
          <input
            v-model="addSubr"
            class="input_email"
            placeholder="Insert here the email of the professionist to subscribe"
            v-if="isAdding"
          />
          <button id="button_email" @click="sendSubRequest" v-if="isAdding">
            >
          </button>

          <div class="div_input_email" v-if="isReporting">
            <input
              v-model="addSubr"
              class="input_email"
              placeholder="Insert the email of the user that you want to report"
              v-if="isReporting"
            />
            <input
              v-model="addSubr"
              class="input_email"
              placeholder="Description"
              v-if="isReporting"
            />
          </div>
          <button id="button_email_report" @click="" v-if="isReporting">
            >
          </button>
        </div>
      </transition>

      <button class="maindash_button" id="but_money" v-if="typeAcc !== 0">
        <img
          alt="Error"
          src="@/assets/catIcon.png"
          style="width: 80%; height: 80%"
        />
      </button>
      <select v-model="newProfession" v-if="typeAcc === 0">
        <option value="" disabled selected></option>
        <option value="Nutritionist">Nutritionist</option>
        <option value="Personal Trainer">Personal Trainer</option>
        <option value="Premium User">Premium user</option>
      </select>
      <button class="maindash_button" id="but_money" @click="changePlan">
        <img
          alt="Error"
          src="@/assets/dollarIcon.png"
          style="width: 80%; height: 80%"
          v-if="typeAcc === 0"
        />
        <img
          alt="Error"
          src="@/assets/downgrade.png"
          style="width: 80%; height: 80%"
          v-if="typeAcc !== 0"
        />
      </button>
      <button class="maindash_button" id="but_report" @click="openReport">
        <img
          alt="Error"
          src="@/assets/reportIcon.png"
          style="width: 80%; height: 80%"
        />
      </button>
      <button class="maindash_button" id="but_plus" @click="openAddSub">
        <img
          alt="Error"
          src="@/assets/plusIcon.png"
          style="width: 80%; height: 80%"
        />
      </button>
    </div>
  </div>
</template>

<style scoped>
.slide-enter-active {
  animation: slide-in 0.5s ease;
}

.slide-leave-active {
  animation: slide-out 0.5s ease;
}

@keyframes slide-in {
  from {
    transform: translateX(120%);
  }
  to {
    transform: translateX(0);
  }
}

@keyframes slide-out {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(120%);
  }
}

#button_email {
  width: 2vw;
  padding: 5px;
  margin-top: auto;
  margin-bottom: auto;
  margin-right: auto;
  background-color: #45a049;
  transition: background-color 0.3s ease;
  border-radius: 0 10px 10px 0 ;
}

#button_email:hover {
  background-color: #007a07;
}

#button_email_report  {
  width: 2vw;
  padding: 21px;
  margin-top: auto;
  margin-bottom: auto;
  margin-left: -3.4vw;
  background-color: #850000;
  transition: background-color 0.3s ease;
  border-radius: 0 10px 10px 0 ;
}

#button_email_report:hover {
  background-color: #430000;
}

.input_email {
  width: 80%;
  margin-top: auto;
  margin-bottom: auto;
  margin-left: auto;
  font-size: 14px;
  padding: 5px;
}

.div_input_email {
  margin-top: auto;
  margin-bottom: auto;
  margin-left: 2vw;
}

.insEmail {
  display: flex;
  align-content: center;
  position: relative;
  left: +20%;
  margin-bottom: 4vh;
  background-color: #b0e464;
  width: 23vw;
  height: 20vh;
  border-radius: 10px;
  border: 2px solid black;
}

.insEmailReport {
  display: flex;
  align-content: center;
  position: relative;
  left: +20%;
  margin-bottom: 4vh;
  background-color: #cf1f02;
  width: 23vw;
  height: 20vh;
  border-radius: 10px;
  border: 2px solid black;
}

#div_profile_space {
  width: 30vw;
  margin-left: 4px;
  height: 100%;
}

#div_subscriber {
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

#but_money {
  background-color: #ffdc5c;
  border: 1px solid black;
  transition: background-color 0.3s ease;
}

#but_money:hover {
  background-color: #ffac24;
  cursor: pointer;
}

#but_report {
  background-color: #cf1f02;
  border: 1px solid black;
  transition: background-color 0.3s ease;
}

#but_report:hover {
  background-color: #a61902;
  cursor: pointer;
}

#but_plus {
  background-color: #b8e464;
  border: 1px solid black;
  transition: background-color 0.3s ease;
}

#but_plus:hover {
  background-color: #93b650;
  cursor: pointer;
}

.maindash_button {
  width: 12vh;
  height: 12vh;
  border-radius: 50%;
  border: 1px solid black;
  transition: background-color 0.3s ease;
  margin-right: 2vw;
  overflow: hidden;
}

#div_button_menu {
  position: fixed;
  margin-bottom: 2vw;
  bottom: 0;
  right: 0;
}

#h1_subscriptions_div {
  position: fixed;
  background-color: #e8f4fc;
  border-bottom: 2px solid black;
  border-bottom: 2px solid black;
  width: 100%;
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

#h1_subscriptions {
  margin-left: 3vw;
  font-family: "Stinger Fit Trial", sans-serif;
  font-size: 6vh;
}

#out_containter {
  display: flex;
  width: 100vw;
  height: 100vh;
}
</style>
