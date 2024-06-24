<script>
export default {
  name: "cardProfile",
  props: {
    email: String,
    auth_token: "",
  },
  methods: {
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

    async function_accept(acceptEmail, ADRequest) {
      return await this.function_query_post("PUT", `subscription`, {
        acceptEmail,
        ADRequest,
      });
    },
    acceptRequest() {
      this.function_accept(this.email, true).then(json=>{this.$emit("reloadReq")});
    },
    refuseRequest() {
      this.function_accept(this.email, false).then(json=>{this.$emit("reloadReq")});
    },
  },
};
</script>

<template>
  <button class="button_card_profile">
    <div id="container_card">
      <h2>New Request!</h2>
      <div id="div_card_pic">
        <img src="@/assets/defaultPIC.png" />
      </div>
      <div id="div_info_card">
        <div class="show_info">
          <h2 class="h2_card" style="font-family: verdana">
            email: {{ email }}
          </h2>
        </div>
        <div>
          <button class="selReq" @click="acceptRequest">Yes</button>
          <button class="selReq" @click="refuseRequest">No</button>
        </div>
      </div>
    </div>
  </button>
</template>

<style scoped>

.selReq{
  background-color: white;
  transition: background-color 0.3s ease;
  width: 80px;
  height: 35px;
}

.selReq:hover{
  background-color: #c4adef;
  cursor: pointer;
}

.button_card_profile {
  background-color: transparent;
  border: none;
  height: 35vh;
}

.close_button {
  margin-top: 1vh;
  margin-left: -11.5vw;
  border-radius: 30%;
  border: none;
  background-color: transparent;
  width: 2vw;
  transition: background-color 0.3s ease;
}

.close_button:hover {
  background-color: #844034;
  cursor: pointer;
}

#div_info_card {
  margin-top: 2vh;
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
  font-family: "Stinger Fit Trial", sans-serif;
  font-size: 16px;
}

#div_card_pic {
  display: flex;
  width: 100%;
  height: 20vh;
  border-radius: 50%;
  margin-top: -1vh;
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
  background-color: #9064e4;
  border: 1px solid black;
  align-items: center; /* Per centrare verticalmente */
  justify-content: center; /* Per centrare orizzontalmente */
  transition: background-color 0.3s ease;
  cursor: pointer;
}
</style>
