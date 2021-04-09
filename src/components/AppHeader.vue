<template>
  <header>{{ title }}</header>
</template>

<script>
export default {
  name: "AppHeader",
  data() {
    return {
      title: "大原 Download Command Maker",
    };
  },
};
</script>

<style scoped>
header {
  width: 100%;
  height: 90px;
  display: flex;
  align-items: center;
  border-bottom: 1px solid gray;
  padding: 0 32px;
  background: darkblue;
  color: white;
  font-size: 24px;
}
</style>
