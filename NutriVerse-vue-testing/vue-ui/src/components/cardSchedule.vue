<script>
export default {
  name: "cardSchedule",
  data() {
    return {
      isExternalButtonDisabled: false,
      newComment: "",
    };
  },
  props: {
    url: String,
    typeSchedule: String,
    comments: [],
    typeUser: false,
  },
  methods: {
    openUrl() {
      if (!this.isExternalButtonDisabled) window.open(this.url, "_blank");
    },
    disableExternalButton() {
      this.isExternalButtonDisabled = true;
    },
    enableExternalButton() {
      this.isExternalButtonDisabled = false;
    },
    removePlan() {
      this.$emit("removePlan", this.typeSchedule);
    },
    addComment() {
      this.comments.push({message: this.newComment, date: new Date()})
      this.$emit("addComment", this.typeSchedule, this.newComment);
      this.newComment=""
    },
  },
  created() {
    console.log(this.comments);
  },
};
</script>

<template>
  <div
    :class="{
      container_card_sched_diet: typeSchedule === 'Diet',
      container_card_sched_WK: typeSchedule === 'Workout',
    }"
    @click="openUrl"
  >
    <button
      class="close_button"
      @click="removePlan"
      @mouseover="disableExternalButton"
      @mouseout="enableExternalButton"
    >
      <img src="@/assets/closeButton.png" style="width: 100%; height: 100%" />
    </button>
    <img
      src="@/assets/Diet_Icon.png"
      v-if="typeSchedule === 'Diet'"
      class="Schedule_img"
    />
    <img
      src="@/assets/Workout_Icon.png"
      v-if="typeSchedule === 'Workout'"
      class="Schedule_img"
    />
    <div class="comments_d" v-if="typeSchedule === 'Diet'">
      <div v-for="comment in comments" :key="comment.date" class="comment">
        {{ comment.message }}
      </div>
    </div>
    <div class="comments_w" v-if="typeSchedule === 'Workout'">
      <div v-for="comment in comments" :key="comment.date" class="comment">
        {{ comment.message }}
      </div>
    </div>
    <div v-if="!typeUser" class="div_in_com">
      <input
        style="font-size: 18px; border-radius: 5px 0 0 5px"
        v-if="!this.typeUser"
        v-model="newComment"
        @mouseover="disableExternalButton"
        @mouseout="enableExternalButton"
        placeholder="write your comment here"
        @keydown.enter="addComment"
      />
      <button
          style="font-size: 18px; border-radius: 0 5px 5px 0; background-color: white"
        v-if="!this.typeUser"
        @mouseover="disableExternalButton"
        @mouseout="enableExternalButton"
        @click="addComment"
      >
        >
      </button>
    </div>
  </div>
</template>

<style scoped>
.div_in_com {
  position: fixed;
  margin-top: 58vh;
}

.close_button {
  margin-left: -18vw;
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

.Schedule_img {
  border-radius: 50%;
  background-color: black;
  margin-top: 3vh;
}

.container_card_sched_diet,
.container_card_sched_WK {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  background-color: #ffb700;
  border: black 2px solid;
  width: 20%;
  height: 60vh;
  margin-top: 4vh;
  margin-left: 4vh;
  padding: 2vh;
  transition: background-color 0.3s ease;
  cursor: pointer;
  overflow-y: auto;
}

.container_card_sched_diet:hover {
  background-color: #ffd161;
}

.container_card_sched_WK {
  background-color: #00a6ff;
}

.container_card_sched_WK:hover {
  background-color: #63c8ff;
}

.comments_d {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  height: 20vh;
  margin-top: 2vh;
  text-align: left;
  overflow: scroll;
  align-content: center;
  background-color: #fae7b9;
  border-radius: 10px;
}

.comments_w {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  height: 20vh;
  margin-top: 2vh;
  text-align: left;
  overflow: scroll;
  align-content: center;
  background-color: #bae8fd;
  border-radius: 10px;
}

.comment {
  background: white;
  border: 1px solid #ccc;
  padding: 1vh;
  margin-bottom: 1vh;
  border-radius: 5px;
  width: 75%;
}
</style>
