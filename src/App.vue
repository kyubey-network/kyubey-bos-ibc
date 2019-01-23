<template>
    <div id="app">
        <div class="container">
            <ibc-index />
        </div>
    </div>
</template>
<script>
    import "bootstrap/dist/css/bootstrap.min.css";

    import IBC_index from "./components/IBCExchange.vue";


    export default {
        name: "app",
        components: {
            "ibc-index": IBC_index
        }
    };
</script>
<style>

    #app { font-family: "Avenir", Helvetica, Arial, sans-serif; -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale; text-align: center; color: #2c3e50; }
    body { background-color: #f6f7f8; }
</style>
