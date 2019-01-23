<template>
    <div class="page-content  navbar-light bg-light">
        <nav class="navbar ">
            <a class="navbar-brand" href="#">
                <img src="/imgs/logo.png" width="30" height="30" class="d-inline-block align-top" alt>
                Kyubey BOS IBC
            </a>
            <span class="navbar-text text-left">根据官方IBC协议,兑换BOS链上EOS锚定币,网站不收取任何手续费</span>
        </nav>

        <div class="row  mt-3" v-if="!isEosLogin">
            <div class="card col" style="width: 18rem;">
                <ul class="list-group list-group-flush">
                    <li class="list-group-item">
                        <div class=" row">
                            <div class="d-flex col-sm-12 align-content-center flex-wrap  padding0">
                                <div class=" text-left login-type  flex-center">EOS:</div>
                                <div class="col text-right padding0">
                                    <button type="button" class="btn btn-success" @click="scatterLogin">请登录</button>
                                </div>
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>

        <div class="row  mt-3" v-if="isEosLogin">
            <div class="card col" style="width: 18rem;">
                <ul class="list-group list-group-flush">
                    <li class="list-group-item">
                        <div class=" row">
                            <div class="d-flex col-sm-12 align-content-center flex-wrap padding0">
                                <div class=" text-left login-type  flex-center">EOS:</div>
                                <div class="col text-left ml-3">
                                    <p class="login-info">账号：qinxiaowen11</p>
                                    <p class="login-info">余额：1.8821 EOS</p>
                                </div>
                            </div>
                        </div>
                    </li>
                    <li class="list-group-item">
                        <div class="progress mb-2">
                            <div class="progress-bar bg-warning" role="progressbar" style="width: 75%" aria-valuenow="75" aria-valuemin="0" aria-valuemax="100"></div>
                        </div>
                        <div class="d-flex justify-content-between"><span>CPU 已用25.66%</span><span>383.06ms / 1493s</span></div>
                    </li>
                    <li class="list-group-item">
                        <div class=" text-left">
                            <button type="button" class="btn btn-success mr-3">刷新</button>
                            <button type="button" class="btn btn-primary" @click="logoutEOSScatter">退出</button>
                        </div>
                    </li>
                </ul>
            </div>
        </div>

        <div class="row  mt-3">
            <div class="card col" style="width: 18rem;">
                <ul class="list-group list-group-flush">
                    <li class="list-group-item">
                        <div class="row">
                            <div class=" text-left login-type mr-1">BOS:</div>
                            <input type="email" class="form-control col mr-2" placeholder="BOS账号">
                            <button type="button" class="btn btn-success"><i class="el-icon-search"></i></button>
                        </div>
                    </li>
                    <li class="list-group-item">
                        <div class="row">
                            <div class="col">
                                <span>账号：</span><span>qinxiaowen11</span>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col">
                                <span>锚定币：</span><span>12.2122 EOS</span>
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>

        <div class="row  mt-3 mb-3">
            <div class="card col" style="width: 18rem;">
                <ul class="list-group list-group-flush">
                    <li class="list-group-item">
                        <div class="row">
                            <span class="mb-1 ml-1 exchange-tip">请输入你想要兑换的数量</span>
                        </div>
                        <div class="row">
                            <el-input-number v-model="defaultBuyVal" :precision="4" :step="1" style="width:100%;" :min="0.2" :max="1000" label="描述文字"></el-input-number>
                        </div>
                        <div class="row mt-3">
                            <button type="button" class="btn btn-outline-secondary col ml-1 mr-1" @click="defaultBuyVal=10">10</button>
                            <button type="button" class="btn btn-outline-secondary col ml-1 mr-1" @click="defaultBuyVal=100">100</button>
                            <button type="button" class="btn btn-outline-secondary col ml-1 mr-1" @click="defaultBuyVal=500">500</button>
                            <button type="button" class="btn btn-outline-secondary col ml-1 mr-1" @click="defaultBuyVal=1000">1000</button>
                        </div>
                        <div class="row mt-3">
                            <span class="mb-1 ml-1 exchange-tip">将会转入锚定币到abxcuser账号中</span>
                        </div>
                        <div class="row">
                            <button type="button" class="btn btn-success col">交易</button>
                        </div>
                    </li>

                </ul>
            </div>
        </div>

    </div>
</template>
<script>
    import ScatterJS from 'scatterjs-core';
    import ScatterEOS from 'scatterjs-plugin-eosjs';
    import Eos from 'eosjs';

    // Don't forget to tell ScatterJS which plugins you are using.
    ScatterJS.plugins(new ScatterEOS());

    export default {
        data() {
            return {
                defaultBuyVal: 1,
                eosNetwork: {
                    blockchain: 'eos',
                    protocol: 'https',
                    host: 'nodes.get-scatter.com',
                    port: 443,
                    chainId: 'aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906'
                },
                eosScatter: null,
                isEosLogin: false,
                eos: null,
                eosInfo: {
                    name: null,
                    balance: 0,
                    cpu_all: 0,
                    cpu_used: 0,

                }
            };
        },
        methods: {
            scatterLogin() {
                var _this = this;
                ScatterJS.scatter.connect('kyubey').then(connected => {
                    if (!connected) return false;

                    _this.eosScatter = ScatterJS.scatter;

                    _this.eosScatter.login(_this.eosRequiredFields).then(() => {
                        const account = _this.eosScatter.identity.accounts.find(x => x.blockchain === 'eos');
                        _this.eos = _this.eosScatter.eos(_this.eosNetwork, Eos, {});
                        _this.isEosLogin = true;

                    }).catch(error => {
                        console.error(error);
                    });
                });
            },
            logoutEOSScatter() {
                var _this = this;
                _this.isEosLogin = false;
                _this.eosScatter.logout();
            },
            loadEOSInfo: function () {
                this.eos.getAccount('qinxiaowen11').then((result) => {
                    debugger;
                    console.log(result);
                }).catch((err) => {
                    debugger;
                    console.log(err);
                });
            }
        },
        watch: {
            isEosLogin: function (newVal, old) {
                if (newVal == true) {
                    this.loadEOSInfo();
                }
            }
        },
        computed: {
            eosRequiredFields: function () {
                return { accounts: [this.eosNetwork] };
            }
        },
        created: function () {
            var _this = this;
            ScatterJS.scatter.connect('kyubey').then(connected => {
                if (!connected) return false;

                _this.eosScatter = ScatterJS.scatter;

                ScatterJS.scatter.checkLogin().then(identity => {
                    _this.isEosLogin = true;
                    _this.eos = _this.eosScatter.eos(_this.eosNetwork, Eos, {});
                }).catch(error => {
                    //...
                })
            });



        }
    };
</script>

<style scoped>
    .page-content { width: 100%; height: 600px; }
    .navbar-brand { color: #383970; }
    .navbar-text { color: #383970; }
    .login-type { font-size: 24px; }
    .login-info { margin: 0; line-height: 36px; }
    .flex-center { display: flex; align-items: center; }
    .row { margin: 0; }
    .list-group-item { padding-left: 0; padding-right: 0; }
    .padding0 { padding: 0 !important; }
    .exchange-tip { font-size: 14px; }
</style>
