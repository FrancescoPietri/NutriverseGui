<script>
export default {
  name: "cardSchedule",
  props:{
    url:String,
    typeSchedule:String,
    comments:{}
  },
  methods: {
    openUrl(){
      window.open(this.url, '_blank');
    }
  }
}

</script>

<template>
  <div :class="{ 'container_card_sched_diet': typeSchedule==='Diet', 'container_card_sched_WK': typeSchedule==='Workout' }" @click="openUrl">
    <img src="@/assets/Diet_Icon.png" v-if="typeSchedule==='Diet'" class="Schedule_img">
    <img src="@/assets/Workout_Icon.png" v-if="typeSchedule==='Workout'" class="Schedule_img">
    <div class="comments">
      <div v-for="(comment, index) in comments" :key="index" class="comment">
        {{ comment }}
      </div>
    </div>
  </div>
</template>


<style scoped>
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

.comments {
  width: 100%;
  margin-top: 2vh;
  text-align: left;
}

.comment {
  background: white;
  border: 1px solid #ccc;
  padding: 1vh;
  margin-bottom: 1vh;
  border-radius: 5px;
}
</style>
