<template>
    <div class="sailor">
        <Header></Header>
        <WorldMap></WorldMap>
    </div>
</template>
<script>
import Header from '../../common/Header'
import WorldMap from '../../common/WorldMap'
export default {
  name: 'World',
  data () {
    return {}
  },
  components: {
    Header,
    WorldMap
  }
}
</script>
