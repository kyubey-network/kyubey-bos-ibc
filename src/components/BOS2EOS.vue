﻿<template>
    <div>
        <div class="row">
            <div class="col">
                <span class="text-center">
                    EOS锚定币<i class="el-icon-d-arrow-right"></i>EOS
                    <button type="button" @click="switchExchange" v-if="!isMobile" class="btn btn-success btn-sm"> <i class="el-icon-sort"></i>切换</button>
                </span>
            </div>
        </div>
        <div class="row  mt-3" v-if="!isEosLogin">
            <div class="card col" style="width: 18rem;">
                <ul class="list-group list-group-flush">
                    <li class="list-group-item">
                        <div class=" row">
                            <div class="d-flex col-sm-12 align-content-center flex-wrap  padding0">
                                <div class=" text-left login-type  flex-center">BOS:</div>
                                <div class="col text-right padding0">
                                    <button type="button" class="btn btn-success" @click="scatterLogin">登录</button>
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
                                <div class=" text-left login-type  flex-center">BOS:</div>
                                <div class="col text-left ml-3">
                                    <p class="login-info">账号：{{eosInfo.name}}</p>
                                    <p class="login-info">余额：{{eosInfo.balance}} EOS锚定币</p>
                                </div>
                            </div>
                        </div>
                    </li>
                    <li class="list-group-item">
                        <div class="progress mb-2">
                            <div class="progress-bar bg-warning" role="progressbar" v-bind:style="{ width: eosInfo.cpu_percent + '%' }" v-bind:aria-valuenow="eosInfo.cpu_percent" aria-valuemin="0" aria-valuemax="100"></div>
                        </div>
                        <div class="d-flex justify-content-between"><span>CPU 已用 {{eosInfo.cpu_percent}}%</span><span>{{eosInfo.cpu_used | timeFilter}} / {{eosInfo.cpu_all | timeFilter}}</span></div>
                    </li>
                    <li class="list-group-item">
                        <div class=" text-left">
                            <button type="button" class="btn btn-success mr-3" @click="loadEOSInfo">刷新</button>
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
                            <div class=" text-left login-type mr-1">EOS:</div>
                            <input type="text" class="form-control col mr-2" v-model="searchBox" placeholder="EOS账号">
                            <button type="button" class="btn btn-success" @click="loadBOSInfo">确定</button>
                        </div>
                    </li>
                    <li class="list-group-item" v-if="bosInfo">
                        <div class="row">
                            <div class="col">
                                <span>账号：</span><span>{{bosInfo.name}}</span>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col">
                                <span>EOS：</span><span>{{bosInfo.balance}} EOS</span>
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
                            <el-input-number v-model="buyVal" :precision="4" :step="1" style="width:100%;" :min="0.2" :max="1000" label="描述文字"></el-input-number>
                        </div>
                        <div class="row mt-3">
                            <button type="button" class="btn btn-outline-secondary col ml-1 mr-1" @click="buyVal=10">10</button>
                            <button type="button" class="btn btn-outline-secondary col ml-1 mr-1" @click="buyVal=100">100</button>
                            <button type="button" class="btn btn-outline-secondary col ml-1 mr-1" @click="buyVal=500">500</button>
                            <button type="button" class="btn btn-outline-secondary col ml-1 mr-1" @click="buyVal=1000">1000</button>
                        </div>
                        <div class="row mt-3">
                            <span class="mb-1 ml-1 exchange-tip" v-if="bosInfo!=null&&bosInfo.name">将会转入EOS到{{bosInfo.name}}账号中</span>
                        </div>
                        <div class="row">
                            <button type="button" class="btn btn-success col" @click="exchange">兑换</button>
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
                exchangeConfirm: false,
                buyVal: 1,
                bosNetwork: {
                    blockchain: 'eos',
                    protocol: 'https',
                    host: 'nodes.get-scatter.com',
                    port: 443,
                    chainId: 'aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906'
                },
                eosNetwork: {
                    blockchain: 'eos',
                    protocol: 'https',
                    host: 'api.bossweden.org',
                    port: 443,
                    chainId: 'd5a3d18fbb3c084e3b1f3fa98c21014b5f3db536cc15d08f9f6479517c6a3d86'
                },
                eosScatter: null,
                bosScatter: null,
                eos: null,
                bos: null,
                eosAccount: null,
                eosInfo: {
                    name: null,
                    balance: 0,
                    cpu_all: 0,
                    cpu_used: 0,
                    cpu_percent: 0
                },
                searchBox: null,
                bosInfo: null
            };
        },
        filters: {
            timeFilter: function (value) {
                if (value > 1000000) {
                    return value / 1000000.0 + " s";
                }
                else if (value > 1000) {
                    return value / 1000.0 + " ms";
                }
                else {
                    return value + ' us';
                }
            }
        },
        methods: {
            switchExchange() {
                this.logoutEOSScatter();
                this.$router.push("/");
            },
            scatterLogin() {
                var _this = this;
                ScatterJS.scatter.connect('kyubey-bos').then(connected => {
                    if (!connected) {
                        this.$message({
                            message: '请安装Scatter钱包',
                            type: 'error'
                        });
                        return;
                    };

                    _this.eosScatter = ScatterJS.scatter;

                    _this.eosScatter.getIdentity(_this.eosRequiredFields).then(() => {

                        _this.eosAccount = _this.eosScatter.identity.accounts.find(x => x.blockchain === 'eos');

                    }).catch(error => {
                        if (error.code == 402) {
                            _this.$message({
                                message: '该钱包没有导入BOS账号',
                                type: 'error'
                            });
                        }
                        console.error(error);
                    });
                }).catch((err) => {
                    console.log(err);
                });
            },
            logoutEOSScatter() {
                var _this = this;
                _this.eosAccount = null;
                _this.eosInfo = {};
                _this.eosScatter.logout();
            },
            getEOSBalance: async function (account, symbol) {
                var balance = await this.eos().getCurrencyBalance('bosibc.io', account, symbol);
                if (balance.length > 0) {
                    return balance[0].getEOSBalanceObj().value;
                }
                return 0;
            },
            getBOSBalance: async function (account, symbol) {
                var balance = await this.bos().getCurrencyBalance('eosio.token', account, symbol);
                return balance;
            },
            loadEOSInfo: async function () {
                var _this = this;

                this.eos().getAccount(_this.eosAccount.name).then(async (result) => {
                    var cpu_all = result.cpu_limit.max;
                    var cpu_used = result.cpu_limit.used;
                    //var balance = result.core_liquid_balance.getEOSBalanceObj().value;
                    var balance = (await _this.getEOSBalance(_this.eosAccount.name, "EOS"));

                    var cpu_percent = (cpu_used * 100 / cpu_all).toFixed(2);
                    var name = result.account_name;

                    this.eosInfo = {
                        cpu_all,
                        cpu_used,
                        balance,
                        cpu_percent,
                        name
                    };
                }).catch((err) => {
                    console.error(err);
                });
            },
            loadBOSInfo: async function () {
                var _this = this;

                _this.bosInfo = null;
                if (!_this.searchBox)
                    this.$message({
                        message: '请输入有有效的值',
                        type: 'warning'
                    });


                this.bos().getAccount(_this.searchBox).then(async (result) => {
                    var beosBalanceInit = await _this.getBOSBalance(_this.searchBox, "EOS");

                    if (beosBalanceInit.length > 0) {
                        _this.bosInfo = {
                            name: _this.searchBox,
                            balance: beosBalanceInit[0].getEOSBalanceObj().value
                        }
                    }
                    else {
                        _this.bosInfo = {
                            name: _this.searchBox,
                            balance: 0
                        }
                    }
                }).catch((err) => {
                    this.$message({
                        message: '抱歉,没有找到该账号',
                        type: 'warning'
                    });
                    return;
                });

            },
            addExtensions: function () {
                String.prototype.getEOSBalanceObj = function () {
                    var arr = this.split(" ");

                    return {
                        value: arr[0],
                        unit: arr[1]
                    };
                }
            },
            exchangeSubmit: function () {
                var _this = this;

                //var tranferTotal = _this.buyVal.toFixed(4);

                const transactionOptions = { authorization: [`${_this.eosAccount.name}@${_this.eosAccount.authority}`] };


                //_this.eos().transfer(_this.eosAccount.name, 'bosibc.io', `${tranferTotal} EOS`, `${_this.bosInfo.name}@eos`, transactionOptions).then(trx => {
                //    this.$alert('请耐心等待4-5分钟<br/><a href="https://bos.eosx.io/tx/' + trx.transaction_id + '" target="_blank">查看详情</a>', '成功', {
                //        center: true,
                //        dangerouslyUseHTMLString: true
                //    });
                //}).catch(error => {
                //    this.$message({
                //        message: '交易失败',
                //        type: 'error'
                //    });
                //    console.log(error);
                //});


                _this.eos().contract('bosibc.io', { requiredFields: _this.eosRequiredFields })
                    .then(contract => {
                        return contract.transfer(
                            _this.eosAccount.name,
                            'bosibc.io',
                            _this.buyVal.toFixed(4) + ' EOS',
                            `${_this.bosInfo.name}@eos`,
                            transactionOptions);
                    })
                    .then((trx) => {
                        this.$alert('请耐心等待4-5分钟<br/><a href="https://bos.eosx.io/tx/' + trx.transaction_id + '" target="_blank">查看详情</a>', '成功', {
                            center: true,
                            dangerouslyUseHTMLString: true
                        });
                    })
                    .catch(error => {
                        this.$message({
                            message: '交易失败',
                            type: 'error'
                        });
                    });

            },
            exchange: function () {
                var _this = this;

                if (!_this.isEosLogin) {
                    this.$message({
                        message: '请登录BOS主网账号',
                        type: 'warning'
                    });
                    return;
                }
                if (!_this.bosInfo) {
                    this.$message({
                        message: '请填写EOS主网账号，并点击确定',
                        type: 'warning'
                    });
                    return;
                }
                if (_this.eosInfo.balance < _this.buyVal) {
                    this.$message({
                        message: '兑换的数量不能超过当前余额',
                        type: 'warning'
                    });
                    return;
                }
                this.$confirm(`此操作将由BOS官方收取0.1EOS手续费,并兑换${(_this.buyVal - 0.1)}EOS到${_this.bosInfo.name}账户, 是否继续?`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    customClass: 'mobile-confirm',
                    type: 'warning'
                }).then(() => {
                    _this.exchangeSubmit();
                }).catch(() => {
                });
            }
        },
        watch: {
            isEosLogin: function (newVal) {
                if (newVal == true) {
                    this.loadEOSInfo();
                }
            }
        },
        computed: {
            isMobile: function () {
                return window.innerWidth < 768;
            },
            eosRequiredFields: function () {
                var _this = this;
                return { accounts: [this.eosNetwork] };
            },
            isEosLogin: function () {
                var _this = this;
                if (typeof _this.eosAccount !== 'undefined'
                    && _this.eosAccount !== null
                    && _this.eosAccount.name != null) {
                    return true
                }
                return false;
            }
        },
        created: function () {
            var _this = this;
            _this.addExtensions();

            //eos
            ScatterJS.scatter.connect('kyubey-bos').then(connected => {
                if (!connected) return false;

                _this.eosScatter = ScatterJS.scatter;
                _this.eos = () => _this.eosScatter.eos(_this.eosNetwork, Eos, {});

                _this.eosScatter.getIdentity(_this.eosRequiredFields).then(() => {

                    _this.eosAccount = _this.eosScatter.identity.accounts.find(x => x.blockchain === 'eos');

                }).catch(error => {
                    if (error.code == 402) {
                        _this.$message({
                            message: '该钱包没有导入BOS账号',
                            type: 'error'
                        });
                    }
                    console.error(error);
                });
            });

            _this.bosScatter = ScatterJS.scatter;
            _this.bos = () => ScatterJS.scatter.eos(_this.bosNetwork, Eos, {});
        }
    };
</script>

<style>
    .top-text-tip { font-size: 12px; }
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
    .el-message-box { max-width: 90% !important; }
</style>
