<template>
  <div>
    <div @click="changeShow(true)">
      <slot><input disabled readonly v-model="value" ></slot>
    </div>
    <div class="date-time-picker" ref="timepicker" v-if="show">
      <div class="section-part title clearfix">
        <span v-for="item in pageText.title" v-text="item"></span>
      </div>
      <div class="section-part select clearfix">
        <item :name="1" :items="yearArr" v-model="curSelectTime.year"></item>
        <item :name="2" :items="monthArr" v-model="curSelectTime.month"></item>
        <item :name="3" :items="dateArr" v-model="curSelectTime.date"></item>
        <item :name="4" :items="hoursArr" v-model="curSelectTime.hour"></item>
        <item :name="5" :items="minutesArr" v-model="curSelectTime.minute"></item>
      </div>
      <div class="section-part footer clearfix">
        <button type="button" class="btn btn-clear" v-text="pageText.btn[0]" @click="selectTimeClick($event, true)"></button>
        <button type="button" class="btn btn-ok" v-text="pageText.btn[1]" @click="selectTimeClick"></button>
      </div>
    </div>
  </div>
</template>

<script>
  import { lang } from './lang';
  import item from './item.vue'

  export default {
    props: {
      langType: String,
      value: String
    },
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
        curSelectTime: {},
        show: false
      }
    },
    created (){
      this.yearArr = this.initData(1900, 2099);
      this.monthArr = this.initData(1, 12);
      this.dateArr = this.initData(1, 30);
      this.hoursArr = this.initData(0, 23, true);
      this.minutesArr = this.initData(0, 59, true);

      this.initDate();
      this.setMonthDay();
    },
    mounted (){
      document.documentElement.style.overflow = 'hidden';
    },
    watch: {
      'curSelectTime.year': 'setMonthDay',
      'curSelectTime.month': 'setMonthDay',
      'value': 'initDate'
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
        let date = this.value;

        if(typeof date === 'string'){
          date = date.replace(/-/g, '/');
        }

        let now = date ? new Date(date) : new Date();

        this.curSelectTime = {
          year: now.getFullYear(),
          month: now.getMonth() + 1,
          date: now.getDate(),
          hour: now.getHours() > 9 ? now.getHours() : '0' + now.getHours(),
          minute: now.getMinutes() > 9 ? now.getMinutes() : '0' + now.getMinutes()
        }
      },
      selectTimeClick ($event, isClear){
        let date = this.curSelectTime.year + '-' + this.curSelectTime.month + '-' + this.curSelectTime.date + ' ' + this.curSelectTime.hour + ':' + this.curSelectTime.minute;

        if(isClear){
          this.$emit('input', '')
        }else{
          this.$emit('input', date);
        }
        this.changeShow(false);
      },
      changeShow(value) {
        this.show = value;
//        this.$emit('update:show', value);
      },
      setMonthDay (){ //设置年月
        if(this.curSelectTime.month === 2){
          if(this.curSelectTime.year % 4 === 0 &&
            (this.curSelectTime.year % 100 !== 0 ||
            this.curSelectTime.year % 400 === 0)){
            this.dateArr = this.initData(1, 29);
          }else{
            this.dateArr = this.initData(1, 28);
          }
        }else if([4,6,9,11].indexOf(this.curSelectTime.month) > -1){
          this.dateArr = this.initData(1, 30);
        }else{
          this.dateArr = this.initData(1, 31);
        }

        if(this.curSelectTime.month === 2){
          if(this.curSelectTime.date > 28){
            this.curSelectTime.date = 28
          }
        }else if([4,6,9,11].indexOf(this.curSelectTime.month) > -1){
          if(this.curSelectTime.date > 30){
            this.curSelectTime.date = 30
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
      border none
      outline none

  .clearfix:after
    content: ".";
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
</style>
