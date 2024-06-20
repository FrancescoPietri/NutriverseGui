<script>
import BoxProfileIcon from "@/components/boxProfileIcon.vue";

export default {
  name: "ProfileContainer",
  components: {BoxProfileIcon},
  data() {
    return {
      auth_token : "",
      nameU: this.userN,
      weightU: this.weight,
      heightU: this.height,
      ageU: this.age,
      professionU: this.prof,
      genderU: this.gender,
    }
  },
  props: {
    typeAcc: "",
    userN: "",
    weight: "",
    height: "",
    age: "",
    gender: "",
    prof : "",
    email : "",
    isStatsPage: false,
  },
  methods: {
    async function_query(method, uri, data) {
      console.log("Querying " + "https://nutriverse.onrender.com/api/v1/" + uri)
      const headers = {
        'auth-token': this.auth_token,
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

    async function_update(name, weight, age, height, profession, gender) {
      return await this.function_query("PUT", "user", { name, weight, age, height, profession, gender })
    },

    openStats(email, auth_token){
      this.$emit("openStats", email, auth_token)
    },

    logout() {
      this.$emit("logout");
      this.$emit("back")
    },

    getCookie(name) {
      const value = `; ${document.cookie}`;
      const parts = value.split(`; ${name}=`);
      if (parts.length === 2) return parts.pop().split(';').shift();
    },

    async updateProfile() {
      this.auth_token = this.getCookie('auth_token');
      var profession;
      if(this.typeAcc===2) {
        if ((document.getElementById("radio_N").checked || document.getElementById("radio_PT").checked) && this.typeAcc === 2) {
          if (document.getElementById("radio_N").checked) {
            profession = "Nutritionist";
          } else {
            profession = "Personal Trainer";
          }
        }
      }
      if(this.genderU===""){
        this.genderU=this.gender
      }
      const response = await this.function_update(this.nameU, this.weightU, this.ageU, this.heightU, profession, this.genderU);
      alert(response.message);
    }
  },
}
</script>

<template>
  <div id="div_profile" :class="{ 'prof_basic': typeAcc===0, 'prof_pro' : typeAcc===1, 'prof_work' : typeAcc===2 }">
      <div id="div_logo" >
        <img alt="Error" style="width: 100%; height: 100%" src="@/assets/NutriverseLogo.png">
      </div>
      <div id="div_logut_pic">
        <div id="div_logout">
          <button id="button_logout" :class="{ 'but_basic': typeAcc===0, 'but_pro' : typeAcc===1 , 'but_work' : typeAcc===2}" @click="logout">
            <img alt="Error" id="img_logout" src="@/assets/logout.png" v-if="!isStatsPage">
            <img alt="Error" id="img_logout" src="@/assets/backArrow.png" v-if="isStatsPage">
          </button>
        </div>
        <box-profile-icon :email="this.email" :auth_token="this.auth_token" @openStats="openStats"></box-profile-icon>

        <div id="radio_buttons" v-if="typeAcc===2" >
          <input type="radio" id="radio_N" name="account_type" :checked="prof === 'Nutritionist'" :disabled="isStatsPage">
          <label for="radio_basic">N</label><br>
          <input type="radio" id="radio_PT" name="account_type" :checked="prof === 'Personal Trainer'" :disabled="isStatsPage">
          <label for="radio_pro">PT</label><br>
        </div>

      </div>
      <div id="div_personal_information">
        <div>
          <div id="div_upForm">
            <h2 id="h2_profile">Profile:</h2>
            <button id="but_pi" :class="{ 'but_pi_basic': typeAcc===0, 'but_pi_pro' : typeAcc===1 , 'but_pi_work' : typeAcc===2}" @click="updateProfile" v-if="!isStatsPage">UPDATE</button>
          </div>
          <form>
            <div id="div_input_form">
              <div class="div_inner_form">
                <h2 class="h2_form">Name: </h2>
                <input class="input_form" type="text" name="name"  v-model="nameU" :placeholder=userN :disabled="isStatsPage">
              </div>
              <div class="div_inner_form">
                <h2 class="h2_form">Email: </h2>
                <input class="input_form" type="text" name="email" :placeholder=email disabled>
              </div>
              <div class="div_inner_form">
                <h2 class="h2_form">Status: </h2>
                <h2 class="h2_form" style="width: auto" v-if="typeAcc===0"> Patient Basic </h2>
                <h2 class="h2_form" style="width: auto" v-if="typeAcc===1"> Patient Pro </h2>
                <h2 class="h2_form" style="width: auto" v-if="typeAcc===2"> Professionals </h2>
              </div>
              <h2 id="h2_profile" style="margin-top: -0.2vh"> Optional Personal Info: </h2>
              <div id="div_downForm">
                <div class="div_inner_form">
                  <h2 class="h2_form_down" style="width: 20%">Weight: </h2>
                  <input class="input_form_down" style="width: 20%"  type="Number" v-model="weightU"  name="Weight" :placeholder=weight :disabled="isStatsPage">
                  <h2 class="h2_form_down" style="width: 20%; margin-left: 1vw" >Height: </h2>
                  <input class="input_form_down" style="width: 20%" type="Number" v-model="heightU" name="Height" :placeholder=height :disabled="isStatsPage">
                </div>
                <div class="div_inner_form">
                  <h2 class="h2_form_down" style="width: 20%">Age: </h2>
                  <input class="input_form_down" style="width: 20%" type="Number" v-model="ageU" name="Age" :placeholder=age :disabled="isStatsPage">
                  <h2 class="h2_form_down" style="width: 20%; margin-left: 1vw">Gender: </h2>
                  <select class="input_form_down" style="width: 21.5%" v-model="genderU"  name="gender" :disabled="isStatsPage">
                    <option value="" selected>{{gender}}</option>
                    <option value="male">Male</option>
                    <option value="female">Female</option>
                    <option value="other">Other</option>
                  </select>
                </div>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
</template>

<style scoped>
  #radio_buttons{
    margin-left: 9vh;
    font-size: 20px;
    font-family: 'Stinger Fit Trial', sans-serif;
    margin-top: 3vh;
  }
  #form_textarea{
    width: 16.85vw;
    height: 11vh;
    font-size: 15px;
    text-align: left;
    resize: none;
  }

  .div_inner_form{
    display: flex;
    width: 100%;
  }

  .h2_form{
    font-family: 'Stinger Fit Trial', sans-serif;
    font-size: 20px;
    width: 29%;
  }

  .input_form{
    width: 16vw;
    height: 5vh;
    font-size: 20px;
    text-align: center;
  }

  .input_form_down{
    height: 5vh;
    font-size: 20px;
    text-align: center;
  }

  .h2_form_down{
    font-family: 'Stinger Fit Trial', sans-serif;
    font-size: 20px;
    width: 25%;
  }

  #div_input_form{
    display: block;
  }

  #but_pi{
    border: 1px solid black;
    width: 16.45vw;
    height: 5vh;
    font-size: 20px;
    font-family: 'Stinger Fit Trial', sans-serif;
    margin-left: 1.9vw;
    margin-top: 3vh;
    transition: background-color 0.3s ease;
  }

  .but_pi_basic{
    background-color: #4d8330;
  }

  .but_pi_pro{
    background-color: #ffac24;
  }

  .but_pi_work{
    background-color: #5a72a7;
  }


  #but_pi:hover{
    background-color: #348478;
    cursor: pointer;
  }

  #h2_profile{
    font-family: 'Stinger Fit Trial', sans-serif;
    font-size: 1.93vw;
  }

  #div_personal_information{
    margin-top: 6vh;
    margin-left: 2vw;
    margin-right: 1vw;
    width: 100%;
  }

  #div_upForm{
    display: flex;
  }

  #img_pic{
    width: 100%;
    height: 100%;
    border-radius: 50%; /* Rende il bordo circolare */
    background-color: #e8f4fc; /* Imposta il colore di sfondo bianco */
    overflow: hidden;
  }

  #div_pic{
    width: 30%;
    margin-left: 20%;
  }

  #button_logout{
    width: 4vw;
    border-radius: 20%;
    border: none;
    transition: background-color 0.3s ease;
  }

  .but_basic{
    background-color: #b0e464;
  }

  .but_pro{
    background-color: #ffdc5c;
  }

  .but_work{
    background-color: #83aefc;
  }

  #button_logout:hover{
    background-color: #844034;
    cursor: pointer;
  }

  #div_logut_pic{
    display: flex;
    justify-content: left;
    margin-top: 1vh;
    margin-left: 1vh;
    width: 100%;
  }

  #div_logout{
    width: 4vw;
  }

  #img_logout{
    width: 100%;
    height: 100%;
  }

  #div_logo{
    display: flex;
    justify-content: left;
    margin-top: 1vh;
    margin-left: 1vh;
    width: 4vw;
  }

  #div_profile {
    position: fixed;
    width: 30vw;
    height: 100%;
    border-right: 2px solid black;
  }

  .prof_basic {
    background-color: #b0e464;
  }

  .prof_pro  {
    background-color: #ffdc5c;
  }

  .prof_work{
    background-color: #83aefc;
  }

</style>