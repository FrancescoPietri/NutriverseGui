<script>
export default {
  name: "ProfileContainer",
  props: {
    typeAcc: Number
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
            <img alt="Error" id="img_logout" src="@/assets/logout.png">
          </button>
        </div>
        <div id="div_pic">
          <img alt="Error" id="img_pic" src="@/assets/defaultPIC.png"  />
        </div>

        <div id="radio_buttons" v-if="typeAcc===2">
          <input type="radio" id="radio_N" name="account_type">
          <label for="radio_basic">N</label><br>
          <input type="radio" id="radio_PT" name="account_type">
          <label for="radio_pro">PT</label><br>
          <input type="radio" id="radio_A" name="account_type">
          <label for="radio_work">N/PT</label><br>
        </div>

      </div>
      <div id="div_personal_information">
        <div>
          <form>
            <div id="div_upForm">
              <h2 id="h2_profile">Profile:</h2>
              <button id="but_pi" :class="{ 'but_pi_basic': typeAcc===0, 'but_pi_pro' : typeAcc===1 , 'but_pi_work' : typeAcc===2}" @click="updateProfile" >UPDATE</button>
            </div>
            <div id="div_input_form">
              <div class="div_inner_form">
                <h2 class="h2_form">Name: </h2>
                <input class="input_form" type="text" name="name" placeholder="Name">
              </div>
              <div class="div_inner_form">
                <h2 class="h2_form">Surname: </h2>
                <input class="input_form" type="text" name="surname" placeholder="Surname">
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
                  <input class="input_form_down" style="width: 20%"  type="text" name="Weight" placeholder="Weight">
                  <h2 class="h2_form_down" style="width: 20%; margin-left: 1vw" >Height: </h2>
                  <input class="input_form_down" style="width: 20%" type="text" name="Height" placeholder="Height">
                </div>
                <div class="div_inner_form">
                  <h2 class="h2_form_down" style="width: 20%">Age: </h2>
                  <input class="input_form_down" style="width: 20%" type="text" name="Age" placeholder="Age">
                  <h2 class="h2_form_down" style="width: 20%; margin-left: 1vw">Gender: </h2>
                  <select class="input_form_down" style="width: 21.5%" name="Gender">
                    <option value="" disabled selected>Gender</option>
                    <option value="male">Male</option>
                    <option value="female">Female</option>
                    <option value="other">Other</option>
                  </select>
                </div>
                  <div class="div_inner_form">
                    <h2 class="h2_form">Description: </h2>
                    <textarea id="form_textarea"></textarea>
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