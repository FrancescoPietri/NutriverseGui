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
      idPaypal: "",
      age: Number,
      gender: "",
      prof: "",
      newProfession: "",
      typeAcc: 4,
      email: "",
      textReport: "",
      auth_token: null,
      isAdding: false,
      isReporting: false,
      isUpgrading: false,
      expireDate: "",
      bool_chat: false,
      msgAI: "",
      conversationHistory: "",
      professionist: [],
      patiets: [],
      requestsSub: [],
      msgAiChat: [],
    };
  },
  methods: {

    isTokenExpired() {
      const authToken = this.getCookie("auth_token");
      return !(authToken);
    },

    async function_query(method, uri) {
      if(this.isTokenExpired()){
        alert("Session Expired! please login again")
        this.logout();
        return
      }
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
      if(this.isTokenExpired()){
        alert("Session Expired! please login again")
        this.logout();
        return
      }
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

    async function_report(text) {
      return await this.function_query_post("POST", "report", { text });
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

    async function_PayPal(productType) {
      return await this.function_query_post("POST", "paypal", { productType });
    },

    async function_PayPal_Approve(idp) {
      return await this.function_query_post("POST", `paypal/${idp}`);
    },

    async function_endSub() {
      return await this.function_query("GET", `endSubscription`);
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
      this.professionist = []
      this.patiets = []
      this.requestsSub = []
      this.getProfileData();
    },

    openStats(email, auth_token) {
      if(this.isTokenExpired()){
        alert("Session Expired! please login again")
        this.logout();
        return
      }
      this.$emit("openStats", email, auth_token);
    },

    openAddSub() {
      this.isAdding = !this.isAdding;
      this.isReporting = false;
      this.isUpgrading = false;
    },

    openReport() {
      this.isReporting = !this.isReporting;
      this.isAdding = false;
      this.isUpgrading = false;
    },

    openUpgrade() {
      this.isUpgrading = !this.isUpgrading;
      this.isAdding = false;
      this.isReporting = false;
    },

    async upgradePayPal() {
      if (this.typeAcc !== 0) {
        this.function_downgrade().then((json) => {
          this.$emit("logout");
        });
      }
      localStorage.setItem("newProfession", JSON.stringify(this.newProfession));
      var typeP;
      if (
        this.newProfession === "Nutritionist" ||
        this.newProfession === "Personal Trainer"
      ) {
        typeP = "Professionist";
      } else {
        if (this.newProfession === "") {
          return;
        } else {
          typeP = "Premium";
        }
      }
      this.function_PayPal(typeP).then((json) => {
        localStorage.setItem("idPaypal", JSON.stringify(json.id));
        window.location.href = json.links[1].href;
      });
    },

    sendReport() {
      this.function_report(this.textReport);
    },

    openChatAI() {
      this.bool_chat = !this.bool_chat;
      this.$nextTick(() => {
        const container = this.$refs.messagesContainer;
        container.scrollTop = container.scrollHeight;
      });
    },

    async function_AI() {
      try {
        const response = await fetch(
          "https://openrouter.ai/api/v1/chat/completions",
          {
            method: "POST",
            headers: {
              Authorization: `Bearer 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`,
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              model: "mistralai/mistral-7b-instruct:free",
              messages: [
                {
                  role: "user",
                  content: this.conversationHistory + `\nUser: ${this.msgAI}`,
                },
              ], // Includi lo storico come stringa
            }),
          },
        );
        this.msgAI = "";

        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }

        return await response.json();
      } catch (error) {
        console.error("Error:", error);
      }
    },

    sendMessageAi() {
      const userMessage = `User: ${this.msgAI}`;

      // Aggiungi il messaggio dell'utente allo storico della conversazione
      this.conversationHistory += `\n${userMessage}`;

      const tmp = {
        id: this.msgAiChat.length,
        role: "me",
        content: this.msgAI,
      };
      this.msgAiChat.push(tmp);
      this.$nextTick(() => {
        const container = this.$refs.messagesContainer;
        container.scrollTop = container.scrollHeight;
      });

      this.function_AI().then((json) => {
        console.log(json);
        console.log(json.choices[0].message.content);

        const aiMessage = `AI: ${json.choices[0].message.content}`;

        // Aggiungi il messaggio dell'AI allo storico della conversazione
        this.conversationHistory += `\n${aiMessage}`;

        const tmp = {
          id: this.msgAiChat.length,
          role: json.choices[0].message.role,
          content: json.choices[0].message.content,
        };
        this.msgAiChat.push(tmp);

        this.$nextTick(() => {
          const container = this.$refs.messagesContainer;
          container.scrollTop = container.scrollHeight;
        });
      });
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

    getProfileData(){
      this.patiets = []
      this.professionist = []
      this.requestsSub = []
      this.function_data().then((json) => {
        if (json.status === 200) {
          console.log(json);
          const data = json.user;
          this.expireDate = data.subscriptionEndDate;
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

    updateData(){

      this.function_data().then((json) => {
        if (json.status === 200) {
          console.log(json);
          const data = json.user;
          this.expireDate = data.subscriptionEndDate;
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
        }
      })
    },
  },

  watch: {
    idPaypal(newVal) {
      localStorage.setItem("idPaypal", JSON.stringify(newVal));
    },
    newProfession(newVal) {
      localStorage.setItem("newProfession", JSON.stringify(newVal));
    },
  },
  created() {
    this.auth_token = this.getCookie("auth_token");

    let expires = new Date();
    expires.setTime(expires.getTime());
    console.log(expires);

    this.function_endSub().then((json) => {
      if (json.message === "Subscription ended") {
        this.changePlan();
      }
    });

    this.getProfileData()

    this.idPaypal = JSON.parse(localStorage.getItem("idPaypal")) || "";
    this.newProfession =
      JSON.parse(localStorage.getItem("newProfession")) || "";
    const urlParams = new URLSearchParams(window.location.search);
    const PayerID = urlParams.get("PayerID") || "";
    if (PayerID !== "") {
      console.log(this.idPaypal);
      this.function_PayPal_Approve(this.idPaypal).then((json) => {
        if (json.status === "COMPLETED") {
          this.changePlan();
        }
      });
    }
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
      :expire-date="expireDate"
      @logout="logout"
      @openStats="openStats"
      @updateData="updateData"
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
            @removeSub="getProfileData"
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
            @removeSub="getProfileData"
            @openInteractionDashboard="openInteractionDashboardSubr"
          />
        </div>
        <div v-for="pro in requestsSub" :key="pro.email" class="card_div">
          <cardProfileReq :email="pro.email" :auth_token="auth_token" @reloadReq="getProfileData"/>
        </div>
      </div>
    </div>

    <div id="div_button_menu">
      <transition name="slideChat">
        <div class="div_chat" v-if="bool_chat">
          <div id="area_messages" ref="messagesContainer">
            <div
              v-for="mes in msgAiChat"
              :key="mes.id"
              :class="{
                messageSender: mes.role === 'me',
                messageReceiver: mes.role === 'assistant',
              }"
            >
              {{ mes.content }}
            </div>
          </div>
          <div class="input-container">
            <input
              id="input_chat"
              placeholder="write your message here!"
              v-model="msgAI"
              @keyup.enter="sendMessageAi"
            />
            <button id="button_chat" @click="sendMessageAi">></button>
          </div>
        </div>
      </transition>

      <transition name="slide">
        <div
          :class="{
            insEmail: isAdding === true,
            insEmailReport: isReporting === true,
            insUpgrade: isUpgrading === true,
          }"
          v-if="isAdding || isReporting || isUpgrading"
        >
          <div style="width: 100%" id="inter_div">
            <h1 v-if="isAdding" class="h1_rep_add">Add Subscription!</h1>
            <input
              v-model="addSubr"
              class="input_email"
              placeholder="Insert here the email of the professionist to subscribe"
              v-if="isAdding"
            />
            <button id="button_email" @click="sendSubRequest" v-if="isAdding">
              >
            </button>

            <h1 v-if="isReporting" class="h1_rep_add">Send a Report!</h1>
            <input
              class="input_email"
              placeholder="Description"
              v-if="isReporting"
              v-model="textReport"
            />
            <button
              id="button_email_report"
              @click="sendReport"
              v-if="isReporting"
            >
              >
            </button>

            <h1 v-if="isUpgrading" class="h1_rep_add">Change Your Plan!</h1>
            <select
              class="input_upgrade"
              v-model="newProfession"
              v-if="typeAcc === 0 && isUpgrading"
            >
              <option value="" disabled selected></option>
              <option value="Nutritionist">Nutritionist</option>
              <option value="Personal Trainer">Personal Trainer</option>
              <option value="Premium User">Premium user</option>
            </select>

            <div style="display: flex" v-if="typeAcc !== 0">
              <h1 v-if="isUpgrading && typeAcc !== 0">Downgrade:</h1>
              <button
                style="
                  font-size: 40px;
                  padding-right: 45px;
                  border-radius: 10px 10px 10px 10px;
                  margin-left: 1vw;
                "
                id="button_sendUpgrade"
                @click="changePlan"
                v-if="isUpgrading && typeAcc !== 0"
              >
                ⬇
              </button>
            </div>

            <button
              id="button_sendUpgrade"
              @click="upgradePayPal"
              v-if="isUpgrading && typeAcc === 0"
            >
              >
            </button>
          </div>
        </div>
      </transition>

      <button
        class="maindash_button"
        id="but_money"
        v-if="typeAcc !== 0"
        @click="openChatAI"
      >
        <img
          alt="Error"
          src="@/assets/AI_chat_icon.png"
          style="width: 80%; height: 80%; margin-left: 5px"
        />
      </button>
      <button class="maindash_button" id="but_money" @click="openUpgrade">
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
#button_chat {
  border: none;
  padding: 2vh;
  font-size: 16px;
  cursor: pointer;
  background-color: #ffac24;
  color: black;
  border-radius: 0 10px 10px 0;
  transition: background-color 0.3s ease;
}

#button_chat:hover {
  background-color: #c8881d;
}

.div_chat {
  display: flex;
  flex-direction: column;
  overflow: hidden;
  font-family: Arial, sans-serif;
  position: fixed;
  right: 40vw;
  bottom: 0;
  width: 25vw;
  height: 60vh;
  background-color: #ffdc5c; /* Cambia il colore di sfondo per essere coerente con il pulsante */
  border-top: 2px solid black;
  border-left: 2px solid black;
  border-right: 2px solid black;
}

#area_messages {
  flex: 1;
  padding: 10px;
  overflow-y: auto;
  background-color: #ffdc5c; /* Cambia il colore di sfondo per essere coerente con il pulsante */
}

.messageSender {
  margin: 10px 0;
  padding: 10px;
  border-radius: 10px;
  max-width: 60%;
  background-color: #fff5b8;
  margin-left: 10vw;
  box-shadow: 0px 1px 5px rgba(0, 0, 0, 0.1);
}

.messageReceiver {
  margin: 10px 0;
  padding: 10px;
  border-radius: 10px;
  max-width: 60%;
  background-color: #ffffff;
  border: 1px solid #ccc;
  box-shadow: 0px 1px 5px rgba(0, 0, 0, 0.1);
}

#input_chat {
  border: none;
  padding: 2vh;
  font-size: 16px;
  width: 86%;
  box-sizing: border-box;
  border-top: 1px solid #ccc;
  border-radius: 10px 0 0 10px;
  margin-bottom: 1vh;
  margin-left: 1vh;
  margin-top: 1vh;
}

#input_chat:focus {
  border: 2px solid #4d8330; /* Cambia il colore del bordo per essere coerente con il pulsante */
  outline: none; /* Rimuovi il bordo predefinito del focus */
}

.h1_rep_add {
  font-family: "Stinger Fit Trial", sans-serif;
}

#inter_div {
  margin-left: 1.5vw;
}

.slideChat-enter-active {
  animation: slide-in-chat 0.5s ease;
}

.slideChat-leave-active {
  animation: slide-out-chat 0.5s ease;
}

@keyframes slide-in-chat {
  from {
    transform: translateY(120%);
  }
  to {
    transform: translateY(0);
  }
}

@keyframes slide-out-chat {
  from {
    transform: translateY(0);
  }
  to {
    transform: translateY(120%);
  }
}

.slide-enter-active {
  animation: slide-in 0.5s ease;
}

.slide-leave-active {
  animation: slide-out 0.5s ease;
}

@keyframes slide-in {
  from {
    transform: translateX(150%);
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
    transform: translateX(150%);
  }
}

#button_email {
  width: 2vw;
  padding: 10px;
  margin-top: auto;
  margin-bottom: auto;
  margin-right: auto;
  background-color: #45a049;
  transition: background-color 0.3s ease;
  border-radius: 0 10px 10px 0;
}

#button_email:hover {
  background-color: #007a07;
}

#button_email_report {
  width: 2vw;
  padding: 10px;
  margin-top: auto;
  margin-bottom: auto;
  margin-right: auto;
  background-color: #850000;
  transition: background-color 0.3s ease;
  border-radius: 0 10px 10px 0;
}

#button_email_report:hover {
  background-color: #430000;
}

#button_sendUpgrade {
  width: 2vw;
  padding: 10px;
  margin-top: auto;
  margin-bottom: auto;
  margin-right: auto;
  background-color: #ffac24;
  transition: background-color 0.3s ease;
  border-radius: 0 10px 10px 0;
}

#button_sendUpgrade:hover {
  background-color: #b1771b;
}

.input_upgrade {
  width: 86%;
  margin-top: auto;
  margin-bottom: auto;
  margin-left: auto;
  font-size: 16px;
  padding: 10px;
}

.input_email {
  width: 80%;
  margin-top: auto;
  margin-bottom: auto;
  margin-left: auto;
  font-size: 16px;
  padding: 10px;
}

.div_input_email {
  margin-top: auto;
  margin-bottom: auto;
  margin-left: 2vw;
}

.insUpgrade {
  display: flex;
  align-content: center;
  position: relative;
  margin-bottom: 4vh;
  background-color: #ffdc5c;
  width: 23vw;
  height: 20vh;
  border-radius: 10px;
  border: 2px solid black;
}

.insEmail {
  display: flex;
  align-content: center;
  position: relative;
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
