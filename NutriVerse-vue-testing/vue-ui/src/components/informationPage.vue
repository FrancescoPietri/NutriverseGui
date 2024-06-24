<script>
import LoginPage from "@/components/loginPage.vue";
export default {
  name: "informationPage",
  components: { LoginPage },
  data() {
    return {
      displayLogin: false,
      nText:
        "Welcome to Nutriverse, the ultimate solution tailored specifically for nutritionists seeking seamless client interaction. In the dynamic world of nutrition, where personalized guidance and constant communication are key, Nutriverse stands out as the ideal platform for professionals like you.\n" +
        "\n" +
        "Imagine a comprehensive toolkit designed to elevate your practice to new heights. With Nutriverse, you not only streamline your workflow but also enhance the client experience like never before. Say goodbye to cumbersome paperwork and scattered communication channels. Our intuitive interface centralizes all client data, making it effortlessly accessible whenever and wherever you need it.\n" +
        "\n" +
        "But Nutriverse is more than just a data management system. It's your virtual partner, empowering you to forge stronger connections with your clients. Engage in real-time conversations, share personalized meal plans, track progress, and celebrate milestones together—all within a secure and user-friendly environment.\n" +
        "\n" +
        "What truly sets Nutriverse apart is its commitment to innovation. We're constantly evolving to meet the evolving needs of nutritionists and their clients. From advanced analytics tools to customizable templates, we provide the resources you need to stay ahead in an ever-changing industry.\n" +
        "\n" +
        "Join the Nutriverse community today and experience the difference for yourself. Elevate your practice, deepen client relationships, and unlock new possibilities with Nutriverse—the perfect companion for nutritionists who demand excellence.",
    };
  },
  methods: {
    showLogin() {
      this.displayLogin = true;
    },
    hideLogin() {
      this.displayLogin = false;
    },
    logged(password) {
      this.$emit("logged", password);
    },
  },
};
</script>

<template>
  <div id="div_inf_outer">
    <transition name="slide">
      <login-page
        v-if="displayLogin === true"
        @hideLogin="hideLogin"
        @logged="logged"
      />
    </transition>
    <div id="div_title">
      <div id="div_ham">
        <button id="button_ham" @click="showLogin">
          <img id="hamlogin" src="@/assets/ham.png" alt="" />
        </button>
      </div>
      <div id="div_logo_title">
        <img src="@/assets/NutriverseLogo.png" alt="" />
        <h1>utriVerse</h1>
      </div>
      <div id="empty"></div>
    </div>

    <div id="div_information">
      <div class="c_img_inf">
        <img
          style="width: 100%; height: 100%"
          src="@/assets/ptPromo.png"
          alt=""
        />
      </div>
      <div id="div_pt">
        <h2>Welcome to Nutriverse</h2>
        <p>
          Unlock the full potential of your health and fitness journey with Nutriverse,
          your ultimate destination for personalized nutrition and training schedules.
          Designed by leading nutritionists and personal trainers, our platform provides
          you with expert-crafted plans tailored to your unique needs and goals.
        </p>
        <p>
          At Nutriverse, we believe in the power of personalized guidance. Whether you're
          aiming to build muscle, lose weight, improve your endurance, or simply lead a
          healthier lifestyle, our professionals have created schedules that ensure you
          achieve optimal results. With Nutriverse, you gain access to:
        </p>
        <ul>
          <li><b>Custom Nutrition Plans</b>: Diets curated by top nutritionists to fuel your body the right way.</li>
          <li><b>Expert Training Programs</b>: Workouts designed by personal trainers to maximize your performance and efficiency.</li>
          <li><b>Integrated Chat</b>: stay in touch with your Nutritionist or Personal Trainer thanks to a fast and reliable chat!</li>
          <li><b>AI Support</b>: Upgrade to premium or professional to get access to a personalize AI Chatbot!</li>
        </ul>
        <p>
          Start your journey towards a healthier, fitter you with Nutriverse today. Discover the science
          of nutrition and the art of training, and watch as your goals become reality.
        </p>
        <h3>Welcome to a new universe of health and fitness. Welcome to Nutriverse.</h3>
      </div>
      <div class="c_img_inf">
        <img
          style="width: 100%; height: 100%"
          src="@/assets/nFood.png"
          alt=""
        />
      </div>
      <div id="div_n">
        <h2>Our Plans</h2>
        <table id="table_plan">
          <thead>
          <tr>
            <th>Plan</th>
            <th>Price (year)</th>
            <th>Features</th>
          </tr>
          </thead>
          <tbody>
          <tr>
            <td>Premium Basic</td>
            <td>60€</td>
            <td>Grant access to AI chat bot</td>
          </tr>
          <tr>
            <td>Professional</td>
            <td>240€</td>
            <td>Grant access to AI chat bot and the Nutritionist/Personal Trainer title</td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style scoped>
#table_plan th, #table_plan td {
  border: 1px solid black;
  padding: 12px 15px;
  text-align: left;
}

#table_plan th {
  background-color: #58a43c;
  color: #134300;
  text-transform: uppercase;
  letter-spacing: 0.1em;
}

#table_plan tr{
  background-color: white;
}

#table_plan td {
  color: black;
}

.c_img_inf {
  display: flex;
  width: 10vh;
  height: 10vh;
  margin-top: 5vh;
  margin-left: 5vh;
  margin-right: 5vh;
}

.slide-enter-active {
  animation: slide-in 0.5s ease;
}

.slide-leave-active {
  animation: slide-out 0.5s ease;
}

@keyframes slide-in {
  from {
    transform: translateX(-100%);
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
    transform: translateX(-100%);
  }
}

#button_ham:hover #hamlogin {
  content: url("@/assets/ham1.png");
  cursor: pointer;
}

#button_ham {
  background-color: transparent;
  border: none;
}

#div_inf_outer {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

#div_information {
  font-family: "Merriweather", serif;
  display: flex;
  height: 70vh;
  font-size: 20px;
  justify-content: space-around;
}

#div_n {
  width: 35%;
  height: 70%;
  text-align: justify;
  margin-top: 5vh;
  margin-right: 15vh;
  font-size: 25px;
}

#div_pt{
  width: 35%;
  height: 70%;
  text-align: justify;
  margin-top: 5vh;
  margin-right: 15vh;
  font-size: 18px;
}

#div_title {
  background-color: #b0e464;
  box-sizing: border-box;
  width: 100%;
  height: 30vh; /* 30% of the viewport height */
  display: flex;
  align-items: center; /* Vertically center the content */
  justify-content: space-between; /* Distribute children evenly */
  padding: 10px; /* Add a bit of margin from the border */
  font-family: "Stinger Fit Trial", sans-serif;
}

#div_title > :first-child {
  align-self: flex-start; /* Align the first child to the left */
}

#div_title > :nth-child(2) {
  align-self: center; /* Align the second child to the center */
  margin-left: -100px; /* Move the element a bit to the left */
}

#div_title > :last-child {
  align-self: flex-end; /* Align the third child to the right */
}

#div_ham img {
  width: 100px;
  height: auto;
  margin-right: 20px;
}

#div_logo_title {
  display: flex;
  justify-content: center; /* Center horizontally */
  align-items: center; /* Center vertically */
}

#div_logo_title img {
  width: 120px;
  height: auto;
}

#div_logo_title h1 {
  color: #58a43c;
  font-size: 10vh; /* Responsive font size */
  margin: 0; /* Remove any default margin */
}
</style>
