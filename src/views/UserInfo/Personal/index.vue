<template>
    <router-view></router-view>
</template>

<script>
// import PopupWin from "@/components/PopupWin"
export default {
  name: "personal",
  components: {
    // PopupWin,
  },
  data() {
    return {
     
    }
  },
  mounted() {
    if(localStorage && localStorage.loginUserInfo){
    }
  },
  methods: {

  }
};
</script>

<style lang="scss">
  @import "./index.scss";
</style>
