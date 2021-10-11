<template lang="pug">
  .container
    .columns
      .column.is-5.is-offset-4
        .notification(
          :class="numOfTracks > 0 ? 'is-success' : 'is-danger'")
          slot(name="body")
</template>

<script>
export default({
  props: {
    numOfTracks: {
      type: Number, required: true
    }
  }
})
</script>


<style lang="scss" scoped>
  .notification {
    margin: 10px
  }

</style>
