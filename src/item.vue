<template>
    <div class="scroll-part" :class="['date-scroll-part-' + name]">
        <ul @touchstart="startSelect($event, 0)" @touchmove="moveSelect($event, 0)"  @touchend="endSelect($event, 0)" ref="scrollUl">
            <li v-for="item, index in items" :class="{'active': index === selectedIndex}">
                <span v-text="item"></span>
            </li>
        </ul>
        <div class="box-line"></div>
    </div>
</template>

<script>
    export default {
        props: {
            items: Array,
            value: '',
            name: Number,
            month: ''
        },
        data () {
            return {
                startY: '',
                endY: 0,
                lastScrollY: 0,
                scrollGap: 0,
                startTime: 0,
                liHeight: '',
                maxScrollTop: ''
            }
        },
        watch: {
            value(val, old){
                this.initNow();
            }
        },
        computed: {
            selectedIndex () {
                let index = 0;

                if(!this.lastScrollY){
                    index = 1
                }else if(this.lastScrollY === this.liHeight){
                    index = 0;
                }else if(this.lastScrollY + this.liHeight === this.maxScrollTop){
                    index = Math.abs(this.lastScrollY / this.liHeight)
                }else{
                    index = Math.abs(this.lastScrollY / this.liHeight) + 1
                }

                return index;
            }
        },
        mounted (){
            this.liHeight = this.$refs.scrollUl.children[0].offsetHeight;
            this.maxScrollTop = this.$refs.scrollUl.offsetHeight - this.$el.offsetHeight;
            this.initNow();
        },
        methods: {
            initNow (){
                let index = this.items.indexOf(this.value);

                if(index < 1){
                    this.lastScrollY = this.liHeight
                }else{
                    this.lastScrollY = - (index - 1) * this.liHeight;
                }

                this.transformScroll(this.$refs.scrollUl, 0, this.lastScrollY)
            },
            startSelect (e){
                e.preventDefault();
                this.maxScrollTop = this.$refs.scrollUl.offsetHeight - this.$el.offsetHeight;
                this.startY = e.changedTouches[0].pageY;
                this.startTime = new Date().getTime();
            },
            moveSelect (e){
                e.preventDefault();

                let curY = e.changedTouches[0].pageY,
                    scrollLength = this.startY - curY,
                    scrollValue = 0,
                    isScrollUp = false;

                if(scrollLength > 0){ //往上滑
                    scrollValue = - scrollLength + this.lastScrollY;
                    isScrollUp = true;
                }else{ //往下滑
                    scrollValue = this.lastScrollY - scrollLength;
                    isScrollUp = false;
                }

                //滑到顶部
                if (this.isScrollToTop(scrollValue, isScrollUp)) {
                  scrollValue = this.liHeight;
                }

                //滑到底部
                if (this.isScrollToBottom(scrollValue, isScrollUp)) {
                  scrollValue = -this.maxScrollTop - this.liHeight;
                }

                this.transformScroll(e.currentTarget, 0, scrollValue);
            },
            endSelect (e){
                e.preventDefault();

                let elHeight = this.liHeight,
                    isUp,
                    timeGap = new Date().getTime() - this.startTime,
                    ratio = 1;

                this.endY = this.startY - e.changedTouches[0].pageY;
                isUp = this.endY > 0;
                this.lastScrollY -= this.endY;

                //加个速率
                ratio = this.setRatio(timeGap, this.endY, ratio);

                if(isUp){
                    this.lastScrollY = Math.ceil(this.lastScrollY / elHeight) * elHeight - ratio * elHeight;
                }else{
                    this.lastScrollY = Math.floor(this.lastScrollY / elHeight) * elHeight + ratio * elHeight;
                }

                if (this.isScrollToTop(this.lastScrollY, isUp)) {
                  this.lastScrollY = this.liHeight;
                }

                if (this.isScrollToBottom(this.lastScrollY, isUp)) {
                  this.lastScrollY = -this.maxScrollTop - this.liHeight;
                }

                this.transformScroll(e.currentTarget, .2, this.lastScrollY);
            },
            transformScroll (el, duration, scrollValue){
                el.style.transform = `translateY(${scrollValue}px)`;
                el.style['transition-duration'] = `${duration}s`;

                this.$emit('changeSelect', this.selectedIndex);
            },
            isScrollToTop (scrollValue, up){
                return scrollValue >  this.liHeight && !up
            },
            isScrollToBottom (scrollValue, up){
                return scrollValue < -this.maxScrollTop - this.liHeight && up
            },
            setRatio (timeGap, endY, ratio){
                if(endY >= 300){
                    ratio = 4
                }else if(endY >= 200){
                    ratio = 3
                }else if(endY >= 100){
                    ratio = 2
                }

                return ratio
            }
        }
    }
</script>

<style lang="stylus" scoped>
    .scroll-part
        width 20%
        display inline-block
        float left
        text-align center
        overflow hidden
        height 150px
        position relative
        ul
            padding 0
            transition-timing-function: cubic-bezier(0.1, 0.57, 0.1, 1);
            transition-duration: 0ms;
            margin 0
        li
            line-height 50px
            list-style none
            opacity .5
            font-size 14px
            &.active
                opacity 1
                font-size 16px

    .box-line
        position absolute
        z-index -1
        top 50px
        height 50px
        left 10%
        width 80%
        border-top 1px solid #00A0E9
        border-bottom 1px solid #00A0E9
        box-sizing border-box
</style>
