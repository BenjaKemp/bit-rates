<template>
<div class="container">
    <div>{{code}}</div>
    <div v-for="(value, key) in currency" :key="key" class="currency_details">
        <div class="currency_key">{{key}}</div>
        <div class="currency_values">{{value}}</div>
    </div>
</div>
</template>

<script>
export default {
  name: 'Currency',
  props: {
    currency: Object,
    code: String
  },
  data: () => {
    return {}
  },
}
</script>
<style lang="scss" scoped>

.container {
  margin: 10px;
  display: flex;
  flex-direction: row;
  border: solid black 1px;
  border-radius: 5px;
  justify-content: space-around;
  .currency_details {
    display:flex;
    flex-direction: column;
    border: 1px solid gray;
    font-size: 12px;
  }
}
</style>
