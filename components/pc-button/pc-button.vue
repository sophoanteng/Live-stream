<!-- Usage
<pc-button @on-click="LoginFunction">Text</pc-button> -->

<template>
  <button
    class="primary-button text-[14px] font-bold font-[Helvetica] rounded-[6px] text-center py-[12px] px-[14px] text-white bg-gradient-to-r from-[#9672fb] to-[#d772fb] hover:bg-gradient-to-r hover:from-[#d772fb] hover:to-[#d772fb]"
    @click="$emit('on-click')"
  >
    <slot />
  </button>
</template>

<script>
export default {
  name: "pc-button",
};
</script>
