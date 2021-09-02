<template>
  <div class="vSelect">
      <p class="vSelect__title"
         @click="areOptionsVisible = !areOptionsVisible"
         :class="{active: areOptionsVisible}"
      >{{ selected }}</p>
      <div
          class="vSelect__options"
          v-if="areOptionsVisible"
      >
        <p
          v-for="(option, index) in options"
          :key="index"
          @click="selectOption(option)"

        >
          {{ option.name }}
        </p>
      </div>
  </div>
</template>

<script>
export default{
  name: 'v-select',
  props: {
    options: {
      type: Array,
      default(){
        return []
      }
    },
    selected: {
      type: String,
      default: '',
    }
  },
  data(){
    return{
      areOptionsVisible: false,
    }
  },
  methods: {
    selectOption(opt){
      this.$emit('getOption', opt);
      this.areOptionsVisible = false;
    }
  }
}
</script>

<style lang="scss">
    @import "src/assets/css/mixins";
    .vSelect{
      cursor: pointer;
      position: relative;
      background: rgba(255, 255, 255, 0.85);
      border: 1px solid #B8B8B8;
      border-radius: 5px;
      max-width: 255px;
      width: 100%;
      padding: 10px 0px 10px 15px;
      &.invalid{
        border: 1px solid red;
        background: rgba(255, 0, 0, 0.15);
      }
      &__title{
        &:after{
          @include pseudo;
          width: 10px;
          height: 2px;
          background: #C0C0C0;
          transform: rotate(45deg);
          top: 18px;
          right: 20px;
          transition: .4s all ease;
        }
        &:before{
          @include pseudo;
          width: 10px;
          height: 2px;
          background: #C0C0C0;
          top: 18px;
          transform: rotate(-45deg);
          right: 13px;
          transition: .4s all ease;
        }
        &.active:after{
          transform: rotate(140deg);
        }
        &.active:before{
          transform: rotate(-140deg);
        }
      }
      &__options{
        background: white;
        z-index: 20;
        border: 1px solid #B8B8B8;
        position: absolute;
        top: 35px;
        right: 0;
        width: 100%;
        p{
          padding: 10px 0px 10px 15px;
          margin: 0;
          border-bottom: 1px solid #B8B8B8;
          transition: .2s all ease;
        }
        p:hover{
          background: #EBFDFF;
        }
      }
      @media(max-width:420px){
        &{
          max-width: 155px;
        }
        &__title{
          font-size: 14px;
          word-break: break-all;
          width: 110px;
        }
        &__options{
          top: 48px;
        }
      }
    }
</style>