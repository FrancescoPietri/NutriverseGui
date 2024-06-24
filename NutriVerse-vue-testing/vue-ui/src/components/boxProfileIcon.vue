<script>
export default {
  methods: {
    openStats() {
      this.$emit("openStats", this.email, this.auth_token);
    },
  },
  props: {
    email: "",
    auth_token: "",
    typeAcc: "",
  },
};
</script>

<template>
  <div id="div_pic">
    <img
      v-if="typeAcc === 0"
      alt="Error"
      id="img_pic"
      src="@/assets/defaultPIC.png"
      @click.right="openStats"
    />
    <img
      v-if="typeAcc === 1"
      alt="Error"
      id="img_pic"
      src="@/assets/defaultPIC.png"
      @click.right="openStats"
    />
    <img
      v-if="typeAcc === 2"
      alt="Error"
      id="img_pic"
      src="@/assets/Pro_Icon.png"
      style="border: 4px solid black"
      @click.right="openStats"
    />
  </div>
</template>

<style scoped>
#div_pic {
  width: 30%;
  margin-left: 20%;
}
#img_pic {
  width: 100%;
  height: 100%;
  border-radius: 50%; /* Rende il bordo circolare */
  background-color: #e8f4fc; /* Imposta il colore di sfondo bianco */
  overflow: hidden;
}
</style>
