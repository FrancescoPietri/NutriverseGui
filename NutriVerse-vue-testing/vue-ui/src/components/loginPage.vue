<script>
export default {
  name: "loginPage",
  data() {
    return {
      signUp: false,
      email: "",
      password: "",
      retypePassword: "",
    };
  },
  methods: {
    async function_query(method, uri, data) {
      console.log(
        "Querying " + "https://nutriverse.onrender.com/api/v1/" + uri,
      );
      const headers = {
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

    async function_login(email, password) {
      return await this.function_query("POST", "auth", { email, password });
    },

    async function_register(email, password) {
      return await this.function_query("POST", "user", { email, password });
    },

    signupRequest() {
      if (!this.signUp) {
        this.signUp = true;
      }
    },

    loginRequest() {
      if (this.signUp) {
        this.signUp = false;
      }
    },

    hidePage() {
      this.$emit("hideLogin");
    },

    async submitForm() {
      document
        .getElementsByName("send_b")[0]
        .attributes.setNamedItem(document.createAttribute("disabled"));
      try {
        if (this.signUp) {
          if (this.password !== this.retypePassword) {
            alert("Password and Retype Password should match");
            document
              .getElementsByName("send_b")[0]
              .attributes.removeNamedItem("disabled");
            return;
          }
          const email = document.getElementsByName("username")[0].value;
          const password = document.getElementsByName("password")[0].value;
          const response = await this.function_register(email, password);
          if (response !== "") {
            alert(response.message);
          }
        } else {
          const email = document.getElementsByName("username")[0].value;
          const password = document.getElementsByName("password")[0].value;
          const data = await this.function_login(email, password);
          if (data && data.token) {
            let expires = new Date();
            expires.setTime(expires.getTime() + 3600 * 1000);
            document.cookie = `auth_token=${data.token}; expires=${expires.toUTCString()}; path=/`;
            this.$emit("logged", password);
          } else {
            alert("Login failed");
          }
        }
      } catch (error) {
        console.log("Error:", error);
        alert("An error occurred. Please try again later.");
      }
      document
        .getElementsByName("send_b")[0]
        .attributes.removeNamedItem("disabled");
    },
  },
};
</script>

<template>
  <div
    id="login_menu"
    :class="{ signupBackground: signUp, loginBackground: !signUp }"
  >
    <div>
      <button
        @click="hidePage"
        id="closeButton"
        :class="{ closeSignupBG: signUp, closeLoginBG: !signUp }"
      >
        <img src="@/assets/closeButton.png" />
      </button>

      <div id="ico_container">
        <div id="log_ico">
          <img id="img_ico" src="@/assets/defaultPIC.png" />
        </div>
      </div>
    </div>

    <div id="div_form">
      <div class="outer_fdiv">
        <form id="login_form" onsubmit="return false">
          <input type="text" name="username" placeholder="Email" required />
          <input
            type="password"
            name="password"
            placeholder="Password"
            v-model="password"
            required
          />
          <div style="display: flex">
            <div v-if="!signUp" style="height: 85px"></div>
            <transition name="slide">
              <input
                type="password"
                name="retpas"
                placeholder="Retype Password"
                v-if="signUp"
                v-model="retypePassword"
                required
              />
            </transition>
          </div>
          <button
            name="send_b"
            :class="{ smenuButton: signUp, lmenuButton: !signUp }"
            @click="submitForm"
          >
            Send
          </button>
        </form>
      </div>
      <div class="outer_fdiv">
        <div id="div_butt_men">
          <button
            :class="{ smenuButton: signUp, lmenuButton: !signUp }"
            @click="loginRequest"
            :id="!signUp ? 'rad_log' : ''"
          >
            Login
          </button>
          <button
            :class="{ smenuButton: signUp, lmenuButton: !signUp }"
            @click="signupRequest"
            :id="signUp ? 'rad_sign' : ''"
          >
            Signup
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
#rad_log {
  background-color: #9064e4;
}

#rad_sign {
  background-color: #946860;
}

.slide-enter-active {
  animation: slide-in 0.5s ease;
}

.slide-leave-active {
  animation: slide-out 0.5s ease;
}

@keyframes slide-in {
  from {
    transform: translateX(-150%);
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
    transform: translateX(-150%);
  }
}

.closeSignupBG {
  background-color: #588c94;
}

.closeLoginBG {
  background-color: #4d8330;
}

.closeSignupBG:hover {
  background-color: #9bbabf;
}

.closeLoginBG:hover {
  background-color: #79b663;
}

.loginBackground {
  background-color: #4d8330;
}

.signupBackground {
  background-color: #588c94;
}

.loginBackground,
.signupBackground {
  transition: background-color 0.5s ease;
}

#div_butt_men {
  display: flex;
  justify-content: space-around;
  width: 70%;
  margin-top: -2%;
}

.outer_fdiv {
  display: flex;
  justify-content: center;
  align-items: center;
}

#div_form {
  vertical-align: top;
  justify-content: center;
  height: 55vh;
  width: 100%;
  margin-top: -10%;
}

#login_form {
  width: 70%;
  justify-content: center;
}

#login_form input:focus {
  outline: none;
  border-color: #4caf50;
}

#login_form input {
  display: block;
  padding: 15px;
  width: 100%;
  border: 1px solid black;
  border-radius: 5px;
  margin-bottom: 30px;
  font-size: 20px;
  text-align: center;
  box-sizing: border-box;
  background-color: #e8f4fc;
}

.lmenuButton {
  display: inline-block;
  padding: 5px;
  margin-bottom: 30px;
  width: 50%;
  background-color: #e8f4fc;
  border: 1px solid black;
  height: 70px;
  color: black;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  font-size: 20px;
}

.lmenuButton:hover {
  background-color: #80b663;
}

.lmenuButton:disabled {
  background-color: #910018;
}

.smenuButton:disabled {
  background-color: #910018;
}

.smenuButton {
  display: inline-block;
  padding: 5px;
  margin-bottom: 30px;
  width: 50%;
  background-color: #e8f4fc;
  border: 1px solid black;
  height: 70px;
  color: black;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  font-size: 20px;
}

.smenuButton:hover {
  background-color: #8aafb4;
}

#ico_container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 30vh;
  margin-bottom: 17%;
  margin-top: 8%;
}

#log_ico {
  width: 250px;
  height: 250px;
  border-radius: 50%;
  background-color: #e8f4fc;
  overflow: hidden;
}

#img_ico {
  width: 100%;
  height: auto;
}

#closeButton {
  height: 70px;
  width: 70px;
  position: absolute;
  top: 0;
  left: 0;
  margin: 10px;
  border: none;
  border-radius: 50%;
  overflow: hidden;
  transition: background-color 0.3s ease;
  align-content: center;
}

#closeButton img {
  width: 50px;
  height: auto;
}

#login_menu {
  position: fixed;
  justify-content: center;
  height: 100vh;
  width: 35%;
}
</style>
