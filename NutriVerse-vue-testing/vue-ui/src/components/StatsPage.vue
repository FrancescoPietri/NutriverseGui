<script>
import ProfileContainer from "@/components/profileContainer.vue";
import WeightChart from "@/components/weightChart.vue";

export default {
  name: "StatsPage",
  components: { ProfileContainer, WeightChart },
  data() {
    return {
      weights: [],
      auth_token: "",
      userN: "",
      height: "",
      age: "",
      gender: "",
      typeAcc: "",
      emailS: "",
      weight: "",
      prof: "",
    };
  },

  watch: {
    emailS(newVal) {
      localStorage.setItem("emailS", JSON.stringify(newVal));
    },
  },

  methods: {
    async function_query(method, uri, data) {
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

    async function_data(email) {
      return await this.function_query("GET", `subscription/${email}`);
    },

    async function_seeStats(email) {
      return await this.function_query("GET", `statistics/${email}`);
    },

    getCookie(name) {
      const value = `; ${document.cookie}`;
      const parts = value.split(`; ${name}=`);
      if (parts.length === 2) return parts.pop().split(";").shift();
    },

    back() {
      this.$emit("back");
    },
  },

  created() {
    if (this.email !== "") {
      this.emailS = this.email;
    } else {
      this.emailS = JSON.parse(localStorage.getItem("emailS"));
    }
    this.auth_token = this.getCookie("auth_token");
    this.function_seeStats(this.emailS).then((json) => {
      console.log(json);
      this.weights = json.weights;
    });

    this.function_data(this.emailS).then((json) => {
      if (json.status === 200) {
        console.log(json);
        const data = json.user;
        this.userN = data.name;
        this.height = data.height;
        this.age = data.age;
        this.gender = data.gender;
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
    });
  },

  props: {
    email: "",
  },
};
</script>

<template>
  <div id="out_container">
    <div id="div_profile">
      <ProfileContainer
        :email="emailS"
        :typeAcc="typeAcc"
        :user-n="userN"
        :age="age"
        :gender="gender"
        :height="height"
        :weight="weight"
        :prof="prof"
        :is-stats-page="true"
        @back="back"
      />
    </div>
    <div id="div_charts">
      <weight-chart :data="weights"></weight-chart>
    </div>
  </div>
</template>

<style scoped>
#out_container {
  display: flex;
}

#div_profile {
  flex: 0 0 30%;
}

#div_charts {
  flex: 0 0 70%;
  position: relative;
  width: 70vw;
  margin-top: 12vh;
}
</style>
