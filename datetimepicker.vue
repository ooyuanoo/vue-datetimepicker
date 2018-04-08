<template>
    <div class="date-time-picker" ref="timepicker">
        <div class="section-part select clearfix">
            <div class="year scroll-part" @touchstart="startSelect($event, 0)" @touchmove="moveSelect($event, 0)"  @touchend="endSelect($event, 0)" ref="year">
                <ul>
                    <li v-for="item in yearArr">
                        <span v-text="item"></span>
                    </li>
                </ul>
            </div>
            <div class="month scroll-part" @touchstart="startSelect($event, 1)" @touchmove="moveSelect($event, 1)"  @touchend="endSelect($event, 1)" ref="month">
                <ul>
                    <li v-for="item in monthArr">
                        <span v-text="item"></span>
                    </li>
                </ul>
            </div>
            <div class="date scroll-part" @touchstart="startSelect($event, 2)" @touchmove="moveSelect($event, 2)"  @touchend="endSelect($event, 2)" ref="day">
                <ul>
                    <li v-for="item in dateArr">
                        <span v-text="item"></span>
                    </li>
                </ul>
            </div>
            <div class="hour scroll-part"  @touchstart="startSelect($event, 3)" @touchmove="moveSelect($event, 3)"  @touchend="endSelect($event, 3)" ref="hour">
                <ul>
                    <li v-for="item in hoursArr">
                        <span v-text="item"></span>
                    </li>
                </ul>
            </div>
            <div class="minutes scroll-part"  @touchstart="startSelect($event, 4)" @touchmove="moveSelect($event, 4)"  @touchend="endSelect($event, 4)" ref="minutes">
                <ul>
                    <li v-for="item in minutesArr">
                        <span v-text="item"></span>
                    </li>
                </ul>
            </div>
        </div>
        <div class="section-part footer">
            <button type="button" class="btn btn-clear" v-text="pageText[0]"></button>
            <button type="button" class="btn btn-ok" v-text="pageText[1]"></button>
        </div>
    </div>
</template>

<script>
    import { lang } from './lang';

    export default {
        props: ['langType'],
        name: 'dateTimePicker',
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
                lastScrollY: 0
            }
        },
        created (){
            this.yearArr = this.initData(1900, 2099);
            this.monthArr = this.initData(1, 12);
            this.dateArr = this.initData(1, 28);
            this.hoursArr = this.initData(0, 23, true);
            this.minutesArr = this.initData(0, 59, true);
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
            startSelect (e){
                e.preventDefault();
                this.startY = e.changedTouches[0].pageY;
            },
            moveSelect (e){
                e.preventDefault();

                let maxScrollTop = e.currentTarget.offsetHeight - this.$refs.timepicker.offsetHeight + 25;

                if(this.lastScrollY >= maxScrollTop){
                    e.currentTarget.style.transform = `translateY(-${maxScrollTop}px)`;
                    return false;
                }

                if(this.startY - e.changedTouches[0].pageY > 0){
                    e.currentTarget.style.transform = `translateY(${-((this.startY - e.changedTouches[0].pageY) + this.lastScrollY)}px)`;
                }else{
                    e.currentTarget.style.transform = `translateY(${this.lastScrollY + (this.startY - e.changedTouches[0].pageY)}px)`;
                }

                this.lastScrollY += this.startY - e.changedTouches[0].pageY
            },
            endSelect (e){
                e.preventDefault();
                this.endY = this.startY - e.changedTouches[0].pageY
            }
        }
    }
</script>

<style lang="stylus" scoped="true">
    .date-time-picker
        position fixed
        left 0
        bottom 0
        height 175px
        width 100%
        padding 15px
        box-sizing border-box
        background #fff
        font-size 16px
        z-index 99999

        .select
            width 100%
            overflow hidden
            >div
                width 20%
                display inline-block
                float left
                text-align center
                transition transform .1s
            li
                line-height 50px

    .clearfix:after
        content: ".";
        display: block;
        height: 0;
        clear: both;
        visibility: hidden;
</style>