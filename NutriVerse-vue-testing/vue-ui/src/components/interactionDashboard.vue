<script>
import CardSchedule from "@/components/cardSchedule.vue";

export default {
  name: "interactionDashboard",
  data(){
    return{
      auth_token: "",
      saveStatusSubr: "",
    }
  },
  components: {CardSchedule},
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

    async function_schedule(email) {
      return await this.function_query("GET", `upload/${email}`);
    },

    back() {
      this.$emit("back");
    },
    getCookie(name) {
      const value = `; ${document.cookie}`;
      const parts = value.split(`; ${name}=`);
      if (parts.length === 2) return parts.pop().split(";").shift();
    },
  },
  created() {
    this.auth_token = this.getCookie("auth_token");
    if(this.Subscriber===""){
      this.saveStatusSubr = JSON.parse(localStorage.getItem('stateSubr')) || false
    }else{
      this.saveStatusSubr = this.Subscriber
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
          <img alt="Error" src="@/assets/catIcon.png" style="width: 60%; height: 60%; background-color: transparent; border-radius: 0%">
        </button>
      </div>

      <button v-if="saveStatusSubr" id="but_plus" class="maindash_button">
       <img src="@/assets/plusIcon.png" style="width: 80%; height: 80%">
      </button>


    </div>
    <div id="div_schedule">

      <card-schedule type-schedule="Diet" url="https://dashboard.render.com/" :comments="['Ciao non mangiare porcate', 'Ricorda di bere molta acqua', 'Mangia più verdure']" />

      <card-schedule type-schedule="Workout" url="https://dashboard.render.com/" :comments="['se vedi Gore menalo']"/>

    </div>
  </div>
</template>

<style scoped>

.maindash_button{
  width: 12vh;
  height: 12vh;
  border-radius: 50%;
  border: 1px solid black;
  transition: background-color 0.3s ease;
  margin-left: 40vw;
  overflow: hidden;
  margin-top: 4vh;
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