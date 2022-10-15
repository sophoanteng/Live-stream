<template>
  <!-- Example use 
    <pc-avatar :src="'https://www.w3schools.com/howto/img_avatar2.png'" :width="'120px'" :height="'120px'"></pc-avatar> -->
  <img
    :src="src"
    class="rounded-full object-cover"
    alt=""
  />
</template>

<script>
const app = getApp();
export default {
  name: "pc-avatar",
  data() {
    return {};
  },
  props: {
    src: {
      type: String,
      default() {
        return "";
      },
    },
    width: {
      type: String,
      default() {
        return "56px";
      },
    },
    height: {
      type: String,
      default() {
        return "56px";
      },
    },
  },
  methods: {},
  created() {},
};
</script>

<style>
.avatar {
  vertical-align: middle;
  border-radius: 50%;
}
</style>
