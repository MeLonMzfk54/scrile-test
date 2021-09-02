<template>
  <div class="main">
    <div class="modal__bg"></div>
    <div class="modal">
      <div class="modal__close" @click="closeForm">
        <div></div>
      </div>
      <div class="modal__inner">
        <div class="modal__title">Title form</div>
        <form action="#" method="#" class="modal__form" @submit.prevent="sendForm">
          <div class="modal__item">
            <input v-model.trim="firstName" type="text" class="modal__inp modal__name"
                   :class="{ invalid: $v.firstName.$dirty && !$v.firstName.required}"
                   name="nameInp" id="nameInp" placeholder="name">
            <label for="nameInp">First name *</label>
            <small class="error"
                   v-if="$v.firstName.$dirty && !$v.firstName.required">Please fill in first name.</small>
          </div>

          <div class="modal__item">
            <input v-model.trim="lastName" type="text" class="modal__inp modal__text"
                   :class="{ invalid: $v.lastName.$dirty && !$v.lastName.required}"
                   name="surnameInp" id="surnameInp" placeholder="surname">
            <label for="surnameInp">Last name *</label>
            <small class="error"
                   v-if="$v.lastName.$dirty && !$v.lastName.required">Please fill in last name.</small>
          </div>

          <div class="modal__item">
            <input v-model.trim="email" type="email" class="modal__inp modal__email"
                   :class="{ invalid: ($v.email.$dirty && !$v.email.required) || ($v.email.$dirty && !$v.email.email)}"
                   name="mailInp" id="mailInp" placeholder="email">
            <label for="mailInp">user@gmail.com *</label>
            <small class="error"
                   v-if="($v.email.$dirty && !$v.email.required)">Please fill in email.</small>
            <small class="error"
                   v-if="($v.email.$dirty && !$v.email.email)">Please enter a valid email.</small>
          </div>

          <div class="modal__prod">
            <p>Product type *</p>
              <v-select
                :options="options"
                :selected="selected"
                class="modal__select"
                :class="{invalid: $v.value.$dirty && !$v.value.required}"
                @getOption="getOption"
              >
              </v-select>
          </div>
          <small class="error error_right"
                 v-if="$v.value.$dirty && !$v.value.required">Please select product type.</small>
          <div class="modal__additinal"
               v-for="(additinal, index) in additinals"
               :key="index"
          >
            <p>{{ additinal.name }}</p>
            <label class="modal__switch">
              <input type="checkbox" v-model="additinal.checked">
              <span class="modal__check"></span>
            </label>
          </div>
          <div class="modal__item">
            <textarea v-model="review" name="modalArea" id="modalArea" class="modal__comment" cols="30" rows="5" placeholder="comment"></textarea>
            <label for="modalArea">Type your comment</label>
          </div>
          <div class="modal__price">
            <p>Total price</p>
            <p>$<span class="modal__cost">{{ totalPrice }}</span></p>
          </div>
          <button type="submit" class="modal__submit">Send form</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import vSelect from "@/components/v-select"
import $ from "jquery"
import {required, email} from "vuelidate/lib/validators"
  export default{
    data(){
      return{
        firstName: '',
        lastName: '',
        email: '',
        options: [
          {name: 'Product 50$', value: 50},
          {name: 'Product 100$', value: 100},
          {name: 'Product 300$', value: 300},
        ],
        additinals: [
          {name: "Additinal feature for $100", value: 100, checked: false},
          {name: "Additinal feature for $200", value: 200, checked: false},
        ],
        selected: "Select product type",
        value: '',
        review: '',
      }
    },
    validations: {
      firstName: { required },
      lastName: { required },
      email: { required, email },
      value: { required },
    },
    computed: {
      totalAdditinals(){
        return this.additinals.filter((el) => el.checked).map((el) => el.name);
      },
      totalPrice(){
        return this.value + this.additinals.reduce(function (acc, currVal){
            return currVal.checked ? acc + currVal.value : acc + 0;
        },0);
      },
      orderData(){
        return {
          "firstName": this.firstName,
          "lastName": this.lastName,
          "email": this.email,
          "product": this.selected,
          "comment": this.review,
          "totalPrice": this.totalPrice,
          "totalAdditinals": this.totalAdditinals,
        }
      },
    },
    components: {
      vSelect
    },
    mounted(){

    },
    methods:{
      getOption(option){
        this.selected = option.name;
        this.value = option.value;
      },
      closeForm(){
        $(".modal").fadeOut();
        $(".modal__bg").fadeOut();
      },
      sendForm(){
        if(this.$v.$invalid){
          this.$v.$touch();
          return
        }
        console.log("Отправка данных");
        console.log(this.orderData);
      },
    }
  }
</script>

<style lang="scss" scoped>
    @import "src/assets/css/mixins";
    .form{
        overflow: hidden;
        min-height: 100%;
        width: 100%;
    }
    .error{
      color: red;
      &_right{
        display: block;
        text-align: right;
        margin-bottom: 15px;
        margin-right: 95px;
      }
      @media(max-width:420px){
        &_right{
          margin-right: 0;
        }
      }
    }
    /*
    * Modal styles
    **/
    .modal {
      width: 100%;
      position: absolute;
      transform: translate(-50%,-50%);
      background: white;
      z-index: 50;
      top: 50%;
      left: 50%;
      padding: 25px;
      max-width: 580px;
      &__bg{
        position: fixed;
        background: rgba(0, 0, 0, 0.8);
        width: 100%;
        height: 100%;
        left: 0;
        top: 0;
        z-index: 49;
      }
      // .modal__close

      &__close {
        position: absolute;
        right: 10px;
        top: 0px;
        cursor: pointer;
        div{
          width: 30px;
          height: 30px;
          position: relative;
          top: 10px;
          &::before,
          &::after{
            @include pseudo;
            width: 25px;
            height: 1px;
            top: 12.5px;
            left: 3px;
            background: #5B5B5B;
          }
          &:after{
            transform: rotate(45deg);
          }
          &:before{
            transform: rotate(-45deg);
          }
        }
      }


      // .modal__title

      &__title {
        font-weight: 600;
        font-size: 30px;
        margin-bottom: 20px;
      }
      &__select{
        margin-bottom: 15px;
        &.invalid{
          margin-bottom: 10px;
        }
      }
      &__item{
        position: relative;
        margin-bottom: 15px;
        label{
          position: absolute;
          left: 15px;
          color: #787B80;
          top: 43px;
          transition: all 0.5s ease;
          top: 25px;
          font-size: 15px;
        }
        input{
          margin-bottom: 10px;
          display: block;
          border: 1px solid #C3C3C3;
          width: 100%;
          padding: 20px 0px 20px 15px;
          border-radius: 5px;
          font-size: 15px;
          font-weight: 600;
          @include input-placeholder{
            opacity: 0;
          }
          &:focus{
            box-shadow: 0px 0px 9px 6px rgba(177,212,241,0.8);
          }
          &.invalid{
            background: rgba(255, 0, 0, 0.15);
            border: 1px solid red;
          }
          &.invalid + label{
            color: red;
          }
        }
        textarea{
          margin-bottom: 30px;
          border: 1px solid #C3C3C3;
          width: 100%;
          border-radius: 5px;
          font-size: 15px;
          resize: vertical;
          padding: 20px 0px 20px 15px;
          font-weight: 600;
          @include input-placeholder{
            opacity: 0;
          }
          &:focus{
            box-shadow: 0px 0px 9px 6px rgba(177,212,241,0.8);
          }
        }
        input:focus + label,
        input:not(:placeholder-shown) + label,
        textarea:focus + label,
        textarea:not(:placeholder-shown) + label{
          top: 8px;
          left: 15px;
          font-size: 10px;
        }

      }
      &__prod {
        display: flex;
        justify-content: space-between;
        align-items: center;
        p{
          margin-bottom: 15px;
        }
      }

      // .modal__additinal

      &__additinal {
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-bottom: 15px;
      }

      // .modal__switch

      &__switch {
        position: relative;
        display: inline-block;
        width: 50px;
        height: 25px;
        input{
          display:none;
          &:checked + .modal__check{
            background-color: #00A131;
          }
          &:checked + .modal__check:before{
            transform: translateX(26.5px);
          }
        }
      }

      // .modal__check

      &__check {
        position: absolute;
        cursor: pointer;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        border: 3px solid #CEBCBD;
        background-color: #CEBCBD;
        -webkit-transition: .2s;
        transition: .2s;
        border-radius: 34px;

        &:before{
          position: absolute;
          content: "";
          height: 18px;
          width: 18px;
          left: 0px;
          bottom: 1px;
          background: linear-gradient(207deg, rgba(0,0,0,1) 0%, rgba(227,180,180,1) 48%, rgba(0,0,0,1) 100%);
          -webkit-transition: .2s;
          transition: .2s;
          border-radius: 50%;
        }
      }

      // .modal__comment

      &__comment {
      }

      // .modal__price

      &__price {
        display: flex;
        align-items: center;
        justify-content: space-between;
      }

      // .modal__cost

      &__cost {
      }

      &__submit{
        margin-top: 30px;
        background: #448AE0;
        color: white;
        font-weight: 600;
        padding: 15px 25px;
        border-radius: 5px;
        font-size: 16px;
        position: relative;
        left: 50%;
        transform: translate(-50%, 0);
      }

      @media(max-width:420px){
        &__close{
          top: 30px;
        }
        &__item{
          input{
            padding: 10px 0px 10px 15px;
            font-size: 14px;
          }
          textarea{
            font-size: 14px;
          }
          label{
            font-size: 14px;
            top: 14px;
          }
          input:focus + label,
          input:not(:placeholder-shown) + label,
          textarea:focus + label,
          textarea:not(:placeholder-shown) + label{
            top: 2px;
            left: 15px;
            font-size: 9px;
          }
        }
      }
      @media(max-width:360px){
        &{
          top: 53%;
        }
      }
      @media(max-width:330px){
        &{
          top: 60%;
        }
      }
    }
</style>