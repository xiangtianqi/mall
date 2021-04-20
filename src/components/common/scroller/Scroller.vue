<template>
  <div ref="wrapper">
    <div>
      <slot></slot>
    </div>
  </div>
</template>

<script>
    import BScroll from 'better-scroll'

    export default {
        name: "scroller",
        data() {
            return {
                scroller:{
                    type:Object,
                    default() {
                        return {}
                    }
                }
            }
        },
        //接收父组件来的值
        props:{
            probeType:{
                type:Number,
                default:0
            }
        },
        mounted() {
            this.scroller = new BScroll(this.$refs.wrapper, {
                probeType:this.probeType,
                click:true,
                pullUpLoad:true
            })

            this.scroller.on('scroll', (postion) => {
                //console.log(postion);
                this.$emit('scroll', postion)
            })

            this.scroller.on('pullingup', () => {
              console.log('xxxx');
            })

        },
        methods:{
            scrollTo(x, y, time=300) {
                this.scroller.scrollTo(0, 0, time)
            },

        }
    }
</script>

<style scoped>

</style>
