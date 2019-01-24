<template>
    <div id="app">
        <div class="container">
            <router-view></router-view>
        </div>
    </div>
</template>
<script>
    import "bootstrap/dist/css/bootstrap.min.css";

    export default {
        name: "app",
        components: {
            
        }
    };
</script>
<style>
    #app { font-family: "Avenir", Helvetica, Arial, sans-serif; -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale; text-align: center; color: #2c3e50; }
    body { background-color: #f6f7f8; }
</style>
