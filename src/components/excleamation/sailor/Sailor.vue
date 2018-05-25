<template>
  <div class="sailor">
    <Header></Header>
    <Model></Model>
  </div>
</template>

<script>
import Header from '../../common/Header'
import Model from '../../common/Model'
export default {
  name: 'Sailor',
  data () {
    return {}
  },
  components: {
    Header,
    Model
  }
}
</script>