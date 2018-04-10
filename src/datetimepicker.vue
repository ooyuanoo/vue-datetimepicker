<template>
    <div class="date-time-picker" ref="timepicker">
        <div class="section-part title clearfix">
            <span v-for="item in pageText.title" v-text="item"></span>
        </div>
        <div class="section-part select clearfix">
            <item :name="1" :items="yearArr" :cur="curTime.year" v-on:changeSelect="getYearSelected"></item>
            <item :name="2" :items="monthArr" :cur="curTime.month" v-on:changeSelect="getMonthSelected"></item>
            <item :name="3" :items="dateArr" :month="curTime.month" :cur="curTime.date" v-on:changeSelect="getDateSelected"></item>
            <item :name="4" :items="hoursArr" :cur="curTime.hour" v-on:changeSelect="getHourSelected"></item>
            <item :name="5" :items="minutesArr" :cur="curTime.minute" v-on:changeSelect="getMinuteSelected"></item>
        </div>
        <div class="section-part footer clearfix">
            <button type="button" class="btn btn-clear" v-text="pageText.btn[0]" @click="selectTimeClick($event, true)"></button>
            <button type="button" class="btn btn-ok" v-text="pageText.btn[1]" @click="selectTimeClick"></button>
        </div>
    </div>
</template>

<script>
    import { lang } from './lang';
    import item from './item.vue'

    export default {
        props: ['langType', 'isDateSelect'],
        name: 'dateTimePicker',
        components: {
            item
        },
        data () {
            return {
                pageText: lang[this.langType || 'en'],
                yearArr: [],
                monthArr: [],
                dateArr: [],
                hoursArr: [],
                minutesArr: [],
                startY: '',
                endY: 0,
                lastScrollY: 0,
                curTime: {},
                curSelectTime: {},
                date: ''
            }
        },
        created (){
            this.yearArr = this.initData(1900, 2099);
            this.monthArr = this.initData(1, 12);
            this.dateArr = this.initData(1, 30);
            this.hoursArr = this.initData(0, 23, true);
            this.minutesArr = this.initData(0, 59, true);
            this.curTime = this.initDate();
        },
        mounted (){
            document.documentElement.style.overflow = 'hidden';
        },
        methods: {
            initData (start, end, isFix2){
                let temp = [];

                for(let i = start; i <= end; i++){
                    if(isFix2){
                        temp.push(i > 9 ? i : '0' + i)
                    }else{
                        temp.push(i)
                    }
                }

                return temp;
            },
            initDate (){
                let now = new Date();

                return {
                    year: now.getFullYear(),
                    month: now.getMonth() + 1,
                    date: now.getDate(),
                    hour: now.getHours() > 9 ? now.getHours() : '0' + now.getHours(),
                    minute: now.getMinutes() > 9 ? now.getMinutes() : '0' + now.getMinutes()
                }
            },
            getYearSelected (val){
                this.curSelectTime.year = this.yearArr[val];
                this.setMonthDay();
            },
            getMonthSelected (val){
                this.curSelectTime.month = this.monthArr[val];
                this.setMonthDay();
            },
            getDateSelected (val){
                this.curSelectTime.date = this.dateArr[val];
                this.setMonthDay();
            },
            getHourSelected (val){
                this.curSelectTime.hour = this.hoursArr[Number(val)];
            },
            getMinuteSelected (val){
                this.curSelectTime.minute = this.minutesArr[Number(val)];
            },
            selectTimeClick ($event, isClear){
                this.date = this.curSelectTime.year + '-' + this.curSelectTime.month + '-' + this.curSelectTime.date + ' ' + this.curSelectTime.hour + ':' + this.curSelectTime.minute;

                if(isClear){
                    this.$emit('dateChange', '')
                }else{
                    this.$emit('dateChange', this.date)
                }
            },
            setMonthDay (){ //设置年月
                if(this.curSelectTime && this.curSelectTime.month){
                    if(this.curSelectTime.month === 2){
                        if(this.curSelectTime.year % 4 === 0){
                            this.dateArr = this.initData(1, 29);
                        }else{
                            this.dateArr = this.initData(1, 28);
                        }
                    }else if((this.curSelectTime.month <= 7 && this.curSelectTime.month % 2) ||
                        (this.curSelectTime.month > 7 && this.curSelectTime.month % 2)){
                        this.dateArr = this.initData(1, 31);
                    }else{
                        this.dateArr = this.initData(1, 30);
                    }
                }
            }
        }
    }
</script>

<style lang="stylus" scoped>
    .date-time-picker
        position fixed
        left 0
        bottom 0
        width 100%
        padding 15px
        box-sizing border-box
        background #fff
        font-size 16px
        z-index 99999

        .select
            width 100%
            >div
                width 20%
                display inline-block
                float left
                text-align center
                transition transform .1s
            ul
                padding 0
            li
                line-height 50px
                list-style none
        .title
            border-bottom 1px solid #ddd
            >span
                display inline-block
                width 20%
                line-height 50px
                text-align center

    .footer
        border-top 1px solid #ddd
        button
            font-size 16px
            display inline-block
            float left
            width 50%
            text-align center
            height 50px
            line-height 50px
            background #fff

    .clearfix:after
        content: ".";
        display: block;
        height: 0;
        clear: both;
        visibility: hidden;
</style>
