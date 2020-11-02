<template>
  <li>
    <p>
      {{ note }}
    </p>
  </li>
</template>

<script>
export default {
  props: {
    note: {
      type: String
    }
  }
};
</script>

<style scoped>
li {
  margin-bottom: 4px;
}

p {
  margin: 0;
  word-wrap: break-word;
}
</style>
