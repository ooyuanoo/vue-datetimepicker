<template>
    <div class="date-time-picker" ref="timepicker">
        <div class="section-part title clearfix">
            <span v-for="item in pageText.title" v-text="item"></span>
        </div>
        <div class="section-part select clearfix">
            <item :items="yearArr"></item>
            <item :items="monthArr"></item>
            <item :items="dateArr"></item>
            <item :items="hoursArr"></item>
            <item :items="minutesArr"></item>
        </div>
        <div class="section-part footer clearfix">
            <button type="button" class="btn btn-clear" v-text="pageText.btn[0]"></button>
            <button type="button" class="btn btn-ok" v-text="pageText.btn[1]"></button>
        </div>
    </div>
</template>

<script>
    import { lang } from './lang';
    import item from './item.vue'

    export default {
        props: ['langType'],
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

    .clearfix:after
        content: ".";
        display: block;
        height: 0;
        clear: both;
        visibility: hidden;
</style>
