<script>
import CardSchedule from "@/components/cardSchedule.vue";
import CardProfile from "@/components/cardProfile.vue";
import io from "socket.io-client";

const socket = io("wss://nutriverse.onrender.com");

export default {
  name: "interactionDashboard",
  data(){
    return{
      auth_token: "",
      saveStatusSubr: "",
      bool_chat: false,
      inputUrl: "",
      inputTypeP: "",
      payloadMsg : "",
      plans: [],
      messages: [],
    }
  },
  components: {CardProfile, CardSchedule},
  props: {
    IdMail: String,
    InteractionEmail: String,
    Subscriber: String
  },
  methods: {
    async function_query(method, uri, data) {
      console.log("Querying " + "https://nutriverse.onrender.com/api/v1/" + uri);
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
          }
      );
      return await response.json();
    },

    async function_schedule_subr(email) {
      return await this.function_query("GET", `professionPlan/${email}`);
    },

    async function_update_messages(receiver, message) {
      return await this.function_query("POST", `messages/`,  {receiver, message});
    },

    async function_schedule_basic(email) {
      return await this.function_query("GET", `upload/${email}`);
    },

    async function_add_schedule(clientEmail, url, type) {
      return await this.function_query("POST", `upload`, {clientEmail, url, type} );
    },

    async function_remove_schedule(clientEmail, type) {
      return await this.function_query("DELETE", `upload/${clientEmail}`, {type} );
    },

    async function_add_comment(clientEmail, type, comment) {
      return await this.function_query("PUT", `upload/${clientEmail}`, {type, comment} );
    },

    async function_getChat(clientEmail) {
      return await this.function_query("GET", `messages/${clientEmail}`,);
    },

    back() {
      this.$emit("back");
    },
    getCookie(name) {
      const value = `; ${document.cookie}`;
      const parts = value.split(`; ${name}=`);
      if (parts.length === 2) return parts.pop().split(";").shift();
    },

    openChat(){
      this.function_getChat(this.InteractionEmail)
          .then(json=>{
            console.log(json)
            this.messages=json.messages;
          })
      this.bool_chat = !this.bool_chat;
    },

    addSchedule(){
      this.function_add_schedule(this.InteractionEmail, this.inputUrl, this.inputTypeP)
    },
    removePlan(typeP){
      this.function_remove_schedule(this.InteractionEmail, typeP)
    },
    addComment(typeSchedule, newComment){
      this.function_add_comment(this.InteractionEmail, typeSchedule, newComment)
    },
    sendMessage() {
      const messageJson = {
        senderId: "john.doe@example.com",
        receiverId: "cerkapatrick@gmail.com",
        payload: this.payloadMsg
      };
      console.log("Sending message:", messageJson);
      socket.emit('sendMessage', messageJson);
    },

    receiveMessage(payload){
      this.function_update_messages(this.InteractionEmail, payload)
    }
  },
  created() {
    socket.emit("joinRoom", {user1: this.IdMail, user2: this.InteractionEmail})
    console.log("Joined room with users:", this.IdMail, this.InteractionEmail);

    socket.on("receiveMessage",(data)=>{
      console.log(data.payload);
      this.receiveMessage(data.payload);
      const msg = {
        id:this.messages.length,
        text:data.payload,
        sender: this.InteractionEmail
      };
      this.messages.push(msg)
    })

    this.auth_token = this.getCookie("auth_token");
    if(this.Subscriber===""){
      this.saveStatusSubr = JSON.parse(localStorage.getItem('stateSubr')) || false
    }else{
      this.saveStatusSubr = this.Subscriber
    }
    if(this.saveStatusSubr){
        this.function_schedule_subr(this.InteractionEmail)
            .then((json) => {
              console.log(json)
              var tmp
              for (tmp in json.Plans) {
                this.plans.push(json.Plans[tmp])
              }
              console.log(this.plans)
            })
    }else {
      this.function_schedule_basic(this.InteractionEmail)
          .then((json) => {
            console.log(json)
            var tmp
            for (tmp in json.Plans) {
              this.plans.push(json.Plans[tmp])
            }
            console.log(this.plans)
          })
    }
  },
  watch: {
    saveStatusSubr(newVal){
      localStorage.setItem('stateSubr', JSON.stringify(newVal));
    }
  }
}
</script>

<template>
  <div id="div_container_inter">
    <div id="div_upper_bar">

      <button id="button_back" @click="$emit('back')">
        <img id="back" src="@/assets/backArrow.png" alt="" />
      </button>
      <div id="div_int_pic">
        <div class="div_inner">
          <img src="@/assets/defaultPIC.png"/>
          <span class="span_inner">{{ IdMail }}</span>
        </div>
        <div class="div_inner"  style="margin-left: 6vh">
          <img src="@/assets/defaultPIC.png"/>
          <span class="span_inner">{{ InteractionEmail }}</span>
        </div>
        <button class="inter_button" id="but_money" >
          <img alt="Error" src="@/assets/catIcon.png" style="width: 60%; height: 60%; background-color: transparent; border-radius: 0%" @click="openChat">
        </button>
      </div>

      <div v-if="saveStatusSubr" id="addPlan">
        <div id="form_div">
          <input v-model="inputUrl" placeholder="url of the new schedule"/>
          <select v-model="inputTypeP">
            <option value="" selected> type schedule </option>
            <option value='Diet'> Diet </option>
            <option value='Workout'> Workout </option>
          </select>
        </div>
        <button id="but_plus" class="maindash_button" @click="addSchedule">
         <img src="@/assets/plusIcon.png" style="width: 80%; height: 80%">
        </button>
      </div>


    </div>
    <div id="div_schedule">

      <card-schedule v-for="p in plans"  :key="p.id" :type-schedule="p.type" :url="p.url" :comments="p.comment" :type-user="saveStatusSubr" @removePlan="removePlan" @addComment="addComment"/>

      <transition name="slide">
          <div class="div_chat" v-if="bool_chat">
            <div id="area_messages">
              <div v-for="mes in messages" :key="mes.id" :class="{'messageSender': mes.sender===this.IdMail, 'messageReceiver': mes.sender===this.InteractionEmail}">
                {{ mes.text }}
              </div>
            </div>
            <div class="input-container">
              <input id="input_chat" placeholder="write your message here!" v-model="payloadMsg">
              <button id="button_chat" @click="sendMessage"> > </button>
            </div>
          </div>
      </transition>

    </div>
  </div>
</template>

<style scoped>

#button_chat {
  border: none;
  padding: 2vh;
  font-size: 16px;
  cursor: pointer;
  background-color: #58a43c;
  color: white;
  border-radius: 0 10px 10px 0 ;
}

#button_chat:hover {
  background-color: #03750b;
}

.div_chat {
  display: flex;
  flex-direction: column;
  overflow: hidden;
  font-family: Arial, sans-serif;
  position: fixed;
  right: 5vw;
  bottom: 0;
  width: 35vw;
  height: 70vh;
  background-color: #b8e464;
  border-top: 2px solid black;
  border-left: 2px solid black;
  border-right: 2px solid black;
}

#area_messages {
  flex: 1;
  padding: 10px;
  overflow-y: auto;
  background-color: #b8e464;
}

.messageSender {
  margin: 10px 0;
  padding: 10px;
  border-radius: 10px;
  max-width: 60%;
  background-color: #dcf8c6;
  margin-left: 15vw;
}

.messageReceiver {
  margin: 10px 0;
  padding: 10px;
  border-radius: 10px;
  max-width: 60%;
  background-color: #ffffff;
  border: 1px solid #ccc;
}

#input_chat {
  border: none;
  padding: 2vh;
  font-size: 16px;
  width: 90%;
  box-sizing: border-box;
  border-top: 1px solid #ccc;
  border-radius: 10px 0 0 10px;
  margin-bottom: 1vh;
  margin-left: 1vh;
  margin-top: 1vh;
}

#input_chat:focus {
  border: 2px #4d8330;
}


#addPlan {
  display: flex;
  align-items: flex-start;
  background-color: #f9f9f9;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  max-width: 600px;
  margin: auto;
  margin-left: 32vw;
}

#form_div {
  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 100%;
}

#form_div input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
  box-sizing: border-box;
  width: 100%;
}

#form_div select {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
  box-sizing: border-box;
  width: 100%;
}

#but_plus.maindash_button {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 60px;
  height: 50px;
  background-color: #4CAF50;
  border: none;
  border-radius: 50%;
  cursor: pointer;
  margin-left: 20px;
  transition: background-color 0.3s ease;
  margin-top: 20px;
}

#but_plus.maindash_button:hover {
  background-color: #45a049;
}

#but_plus img {
  width: 80%;
  height: 80%;
}


.slide-enter-active {
  animation: slide-in 0.5s ease;
}

.slide-leave-active {
  animation: slide-out 0.5s ease;
}

@keyframes slide-in {
  from {
    transform: translateY(100%);
  }
  to {
    transform: translateY(0%);
  }
}

@keyframes slide-out {
  from {
    transform: translateY(0%);
  }
  to {
    transform: translateY(100%);
  }
}


.div_chat{
}

.maindash_button{
  width: 12vh;
  height: 12vh;
  border-radius: 50%;
  border: 1px solid black;
  transition: background-color 0.3s ease;
  margin-left: 0;
  overflow: hidden;
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


  #div_schedule{
    display: flex;
    flex-wrap: wrap;
    overflow: scroll;
    width: 100%;
    height: 100%;
  }

  .inter_button{
    background-color: transparent;
    border: none;
    margin-top: 3vh;
    width: 12vh;
    height: 12vh;
    border-radius: 50%;
    transition: background-color 0.3s ease;
    margin-left: 8vh;
  }

  .inter_button:hover {
    background-color: #b8e464;
    cursor: pointer;
  }

  .span_inner{
    font-size: 2vh;
    margin-top: 1vh;
    font-family: 'Stinger Fit Trial', sans-serif;
  }

  .div_inner{
    display: flex;
    flex-direction: column;
    margin-left: 2vh;
    height: 100%;
    align-items: center;
  }

  #div_int_pic{
    display: flex;
    height: 70%;
    margin-left: 10vh;
    margin-top: 1vh;
  }

  #div_int_pic img{
    background-color: #e8f4fc;
    border-radius: 50%;
    height: 100%;
  }

  #button_back{
    background-color: transparent;
    border: none;
    margin-top: 5vh;
    margin-left: 3vh;
    width: 10vh;
    height: 10vh;
    border-radius: 50%;
    transition: background-color 0.3s ease;
  }

  #button_back:hover {
    background-color: #b8e464;
    cursor: pointer;
  }

  #back{
    margin-left: -1vh;
    width: 80%;
    height: 80%;
  }

  #div_container_inter{
    display: flex;
    flex-direction: column;
    width: 100%;
    height: 100%;
  }

  #div_upper_bar{
    display: flex;
    width: 100%;
    height: 20vh;
    background-color: #58a43c;
    border-bottom: 2px solid black;
  }

  #div_schedule{
    width: 100%;
    height: 79vh;
  }
</style>