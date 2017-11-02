<template>

    <div id="city" v-if="showModel">
        <div class="bg"></div>
        <div class="city">
            <div>
                <div v-touch:swipeup="methodFunc" v-touch:swipedown="methodFunc" data-index="1">
                    <p v-for="(item,index) in province" v-if="province.length > 0" :class="{'active':index == 0}">
                        <span>{{item.value}}</span>
                    </p>
                    <p v-if="province.length == 0" class="active"><span>请选择</span></p>
                </div>
                <span class="line">-</span>
            </div>

            <div>
                <div v-touch:swipeup="methodFunc" v-touch:swipedown="methodFunc" data-index="2">
                    <p :class="{'active':index == 0}" v-for="(item,index) in city"
                       v-if="city.length > 0">
                        <span>{{item.value}}</span>
                    </p>
                    <p v-if="city.length == 0" class="active"><span>请选择</span></p>
                </div>
                <span class="line">-</span>
            </div>

            <div>
                <div v-touch:swipeup="methodFunc" v-touch:swipedown="methodFunc" data-index="3">
                    <p v-for="(item,index) in township" :class="{'active':index == 0}"
                       v-if="township.length > 0">
                        <span>{{item.value}}</span>
                    </p>
                    <p v-if="township.length == 0" class="active"><span>请选择</span></p>
                </div>
            </div>
        </div>
        <div class="determine" @click="determine">
            <p>确定</p>
        </div>
        <div class="cencel" @click="cencel">
            <p>取消</p>
        </div>

    </div>

</template>
<script>
    import {Toast} from 'mint-ui';
    import Bus from 'router/bus.js';
    export default {
        props  : ['consignmentPlace'],
        data(){
            return {
                showModel    : false,
                provinceIndex: 0,
                cityIndex    : 0,
                townshipIndex: 0,
                province     : [{
                    value: '省份',
                },
                    {
                        "id"   : "1",
                        "value": "大连"
                    },
                    {
                        "id"   : "2",
                        "value": "沈阳"
                    },
                    {
                        "id"   : "3",
                        "value": "北京"
                    }
                ],
                city         : [{
                    value: '城市'
                },
                    {
                        "id"   : "1",
                        "value": "大连"
                    },
                    {
                        "id"   : "2",
                        "value": "沈阳"
                    },
                    {
                        "id"   : "3",
                        "value": "北京"
                    }],
                township     : [{
                    value: '区县'
                },
                    {
                        "id"   : "1",
                        "value": "大连"
                    },
                    {
                        "id"   : "2",
                        "value": "沈阳"
                    },
                    {
                        "id"   : "3",
                        "value": "北京"
                    }]
            }
        },
        created(){
            Bus.$on('getCityData', data => {
                this.queryData(data.index)
            });
        },
        methods: {
            methodFunc(index, type){
                var self     = this;
                var nowIndex = index == 1 ? self.provinceIndex : index == 2 ? self.cityIndex : self.townshipIndex;
                $(".city").children('div').eq(index).find('div').animate({"top": "0rem"});
                $(".city").children('div').eq(index).find('div').children('p').removeClass('active');
                $(".city").children('div').eq(index).find('div').children('p').eq(0).addClass('active');
                if (type == 'swipeup') {
                    nowIndex += 1;
                    if ($(".city").children('div').eq(index - 1).find('p').length == nowIndex) {
                        return false;
                    }
                    if (index == 1) {
                        self.provinceIndex += 1;
                        self.cityIndex     = 0;
                        self.townshipIndex = 0;
                        $(".city").children('div').eq(2).find('div').animate({"top": "0rem"});
                        $(".city").children('div').eq(2).find('div').children('p').eq(0).addClass('active');
                    } else if (index == 2) {
                        self.cityIndex += 1;
                        self.townshipIndex = 0;
                    } else {
                        self.townshipIndex += 1;
                    }

                    $(".city").children('div').eq(index - 1).find('div').animate({"top": "-" + nowIndex * .7 + "rem"});

                } else if (type == 'swipedown') {

                    if (nowIndex == 0) {
                        return false;
                    }
                    nowIndex -= 1;

                    $(".city").children('div').eq(index).find('div').animate({"top": "0rem"});
                    $(".city").children('div').eq(index).find('div').children('p').removeClass('active');
                    $(".city").children('div').eq(index).find('div').children('p').eq(0).addClass('active');

                    if (index == 1) {
                        $(".city").children('div').eq(2).find('div').animate({"top": "0rem"});
                        $(".city").children('div').eq(2).find('div').children('p').eq(0).addClass('active');
                        self.provinceIndex -= 1;
                        self.cityIndex     = 0;
                        self.townshipIndex = 0;
                    } else if (index == 2) {
                        self.cityIndex -= 1;
                        self.townshipIndex = 0;
                    } else {
                        self.townshipIndex -= 1;
                    }

                    $(".city").children('div').eq(index - 1).find('div').animate({"top": "-" + nowIndex * .7 + "rem"});
                }
                $(".city").children('div').eq(index - 1).find('div').children('p').removeClass('active');
                $(".city").children('div').eq(index - 1).find('div').children('p').eq(nowIndex).addClass('active');

                var requestIndex = index == 1 ? 2 : index == 2 ? 3 : 3;
                if (index == 3) {
                    return false;
                }
                this.queryData(requestIndex)
            },
            queryData(index){

                var self       = this;
                self.showModel = true;
                var requestObj = {
                    id: ''
                }
                if (index == 2) {
                    requestObj.id = self.province[self.provinceIndex].id

                } else if (index == 3) {
                    requestObj.id = self.province[self.cityIndex].id
                }


                //    self.$store.dispatch('getCity', requestObj).then(() => {
                if (index == 1) {

                    self.$store.getters.getProvince.map(v => {
                        self.province.push(v)
                    })
                    self.city     = [{
                        value: '城市'
                    }]
                    self.township = [{
                        value: '区县'
                    }]
                } else if (index == 2) {
                    self.city     = [{
                        value: '城市'
                    }]
                    self.township = [{
                        value: '区县'
                    }]

                    if (!requestObj.id) {

                        return false;
                    }

                    self.$store.getters.getProvince.map(v => {
                        self.city.push(v)
                    })

                } else if (index == 3) {

                    self.township = [{
                        value: '区县'
                    }]
                    if (!requestObj.id) {

                        return false;
                    }
                    self.$store.getters.getProvince.map(v => {
                        self.township.push(v)
                    })

                }
                // })
            },
            cencel(){
                var self           = this;
                self.showModel     = false;
                self.provinceIndex = 0;
                self.cityIndex     = 0;
                self.townshipIndex = 0;
                self.province      = [{
                    value: '省份'
                }];
                self.city          = [{
                    value: '城市'
                }];
                self.township      = [{
                    value: '区县'
                }]
            },
            determine(){

                var self     = this;
                var value    = '';
                var id       = '';
                var province = self.province[self.provinceIndex].value;
                var city     = self.city[self.cityIndex].value;
                var township = self.township[self.townshipIndex].value;
                if (province != '省份' && city != '城市' && township != '区县') {
                    value                         = province + '-' + city + '-' + township;
                    id                            = self.township[self.townshipIndex].id;
                    self.consignmentPlace.address = value;
                    self.consignmentPlace.id      = id;
                    self.cencel();
                } else if (province == '省份') {
                    Toast('您还没有选择省份!')
                } else if (city == '城市') {
                    Toast('您还没有选择城市!')
                } else if (township == '区县') {
                    Toast('您还没有选择区县!')
                }


            }

        }
    }
</script>
<style scoped lang="less">
    #city {
        width: 100%;
        height: 100%;
        position: fixed;
        left: 0;
        top: 0;
        z-index: 100;
    }

    .bg {
        background: black;
        width: 100%;
        height: 100%;
        opacity: .4;
    }

    .city {
        position: fixed;
        bottom: 1.6rem;
        width: 100%;
        height: 3.5rem;
        overflow: hidden;
        background: white;
        display: flex;
        padding-bottom: .4rem;
        div {
            position: relative;
            height: 100%;
            line-height: .7rem;
            text-align: center;
            flex: 1;
            p {
                height: .7rem;
                font-size: .28rem;
                &.active {
                    font-weight: bold;
                }
            }
            p:first-child {
                margin: 1.6rem 0 0 0;
            }

            .line {
                position: absolute;
                right: 0;
                top: 1.6rem;
            }
        }

    }

    .cencel {
        width: 100%;
        height: .8rem;
        baclground: white;
        position: fixed;
        bottom: .0rem;
        left: 0;
        background: white;
        line-height: .8rem;
        text-align: center;
        border-top: 0.01rem solid #CCCCCC;
    }

    .determine {
        width: 100%;
        height: .8rem;
        baclground: white;
        position: fixed;
        bottom: .8rem;
        left: 0;
        background: white;
        line-height: .8rem;
        text-align: center;
        border-top: 0.01rem solid #CCCCCC;
    }
</style>