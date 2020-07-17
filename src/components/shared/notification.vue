<template lang="pug">
  .container
    .columns
      .column.is-5.is-offset-4
        slot(name="body").notification.is-danger
           p Algo anduvo mal
</template>

</script>
<style lang="scss" scoped>
.notification{
  margin: 10px;
}
</style>
