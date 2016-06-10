<template>
  <div id="wrapper">
    <!-- header -->
    <div id="header">
      <h1>Drag Drop Sandbox</h1>
    </div>
    <hr/>

    <drag-drop-view></drag-drop-view>


    <!-- main view -->
<!--     <router-view
      class="view"
      keep-alive
      transition
      transition-mode="out-in">
    </router-view> -->
  </div>
</template>

<script>
  import dragDropView from './DragDropView.vue';

  export default {
    components: {
      'drag-drop-view': dragDropView
    },
    ready: function() {
      console.log('app.vue ready')
    }
  }

</script>
