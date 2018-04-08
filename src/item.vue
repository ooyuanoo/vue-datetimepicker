<template>
    <div class="scroll-part">
        <ul @touchstart="startSelect($event, 0)" @touchmove="moveSelect($event, 0)"  @touchend="endSelect($event, 0)" ref="scrollUl">
            <li v-for="item in items">
                <span v-text="item"></span>
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        props: {
            items: Array
        },
        data () {
            return {
                startY: '',
                endY: 0,
                lastScrollY: 0,
                scrollGap: 0
            }
        },
        methods: {
            startSelect (e){
                e.preventDefault();
                this.startY = e.changedTouches[0].pageY;
            },
            moveSelect (e){
                e.preventDefault();

                let curY = e.changedTouches[0].pageY,
                    scrollLength = this.startY - curY,
                    scrollValue = 0,
                    isScrollUp = false;

                if(scrollLength > 0){
                    scrollValue = - scrollLength + this.lastScrollY;
                    isScrollUp = true;
                }else{
                    scrollValue = this.lastScrollY - scrollLength;
                    isScrollUp = false;
                }

                if (this.isScrollToTop(scrollValue, isScrollUp)) {
                  scrollValue = 0;
                }
                if (this.isScrollToBottom(scrollValue, isScrollUp)) {
                  scrollValue = -this.maxScrollTop;
                }
                e.currentTarget.style.transform = `translateY(${scrollValue}px)`;
                e.currentTarget.style['transition-duration'] = `0s`;


//                let transformValue = 0,
//                    // 拉动距离
//                    length = this.startY - e.changedTouches[0].pageY,
//                    // 手势往上拉
//                    isScrollUp = length > 0;
//
//                console.log(this.startY, length, this.lastScrollY)
//                // 顶部不能往下拉了
//                if (this.lastScrollY < 0 && !isScrollUp) {
//                  this.lastScrollY = 0;
//                  return;
//                }
//
//               // 底部不能往上拉了
//                if (this.lastScrollY >= this.maxScrollTop && isScrollUp) {
//                  transformValue = -this.maxScrollTop;
//                  return;
//                }
//
//                transformValue = -this.lastScrollY - length;
//                if (Math.abs(transformValue) > 50) {
//                  transformValue = Math.floor(transformValue / 50) *  50
//                } else {
//                    return;
//                }
//                // 容错
//                if (transformValue > 0) {
//                  transformValue = 0;
//                }
//                if (transformValue < -this.maxScrollTop ) {
//                  transformValue = -this.maxScrollTop;
//                }
//                this.lastScrollY = -transformValue
////                if(this.lastScrollY >= this.maxScrollTop){
////                    transformValue = -this.maxScrollTop;
////                } else {
////                    if(this.startY - e.changedTouches[0].pageY > 0){
////                      transformValue = -((this.startY - e.changedTouches[0].pageY) + this.lastScrollY);
////                    }else{
////                      transformValue = this.lastScrollY + (this.startY - e.changedTouches[0].pageY);
////                    }
////                    this.lastScrollY += this.startY - e.changedTouches[0].pageY
////                }
//                e.currentTarget.style.transform = `translateY(${transformValue}px)`;
//
//              console.log(this.startY, length, this.lastScrollY)
            },
            endSelect (e){
                e.preventDefault();

                let elHeight = this.$refs.scrollUl.children[0].offsetHeight,
                    isUp = this.endY > 0;

                this.endY = this.startY - e.changedTouches[0].pageY;
                this.lastScrollY -= this.endY;

                if(isUp){
                    this.lastScrollY = Math.ceil(this.lastScrollY / elHeight) * elHeight;
                }else{
                    this.lastScrollY = Math.floor(this.lastScrollY / elHeight) * elHeight;
                }

                if (this.isScrollToTop(this.lastScrollY, isUp)) {
                  this.lastScrollY = 0;
                }
                if (this.isScrollToBottom(this.lastScrollY, isUp)) {
                  this.lastScrollY = -this.maxScrollTop;
                }
                e.currentTarget.style.transform = `translateY(${this.lastScrollY}px)`;
                e.currentTarget.style['transition-duration'] = `.3s`;
            },
            isScrollToTop (scrollValue, up){
                return scrollValue > 0 && !up
            },
            isScrollToBottom (scrollValue, up){
                return scrollValue < -this.maxScrollTop && up
            }
        },
        computed: {
          maxScrollTop() {
              return this.$refs.scrollUl.offsetHeight - this.$el.offsetHeight + 25;
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
        height 175px
        ul
            padding 0
            transition-timing-function: cubic-bezier(0.1, 0.57, 0.1, 1);
            transition-duration: 0ms;
        li
            line-height 50px
            list-style none

</style>
