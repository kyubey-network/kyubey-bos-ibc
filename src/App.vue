<template>
    <div id="app">
        <div class="container">
            <div class="page-content  navbar-light bg-light">
                <nav class="navbar ">
                    <a class="navbar-brand" href="#">
                        <img src="/imgs/logo.png" width="30" height="30" class="d-inline-block align-top" alt>
                        Kyubey BOS IBC
                    </a>
                </nav>
                <router-view></router-view>
                <div class="row">
                    <div class="col">
                        <span class="text-center top-text-tip">根据<a href="https://github.com/boscore/Documentation/blob/master/IBC/README_CN.md" target="_blank">官方IBC协议</a>,网站不收取任何手续费</span>
                    </div>
                </div>
                <div class="row">
                    <div class="col">
                        <span class="text-center top-text-tip"><a href="https://bos.kyubey.net" target="_blank">bos.kyubey.net</a> BOS链上的免费去中心化交易所</span>
                    </div>
                </div>
            </div>
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
