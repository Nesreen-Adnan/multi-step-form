<template lang="pug">
.container(:class="{'large-mode': largeMode}")
  ul.sidebar(:class="{'large-mode': largeMode}")
    - var steps = ["your info", "select plan", "add-ons", "summary"];
    each step, index in steps
      - var num = index + 1;
        li(id=`step-${num}`, class={active: num == 1})
          .num.flex.bold= num
          .heading.up(v-if="largeMode")
            .step-num= `step ${num}`
            .step-title.bold= step
  .content.relative(:class="{'large-mode': largeMode}")
    step-one(v-show="step == 1", @next="next")
    step-two(v-show="step == 2", @modifyPlan="modifyPlan", @modifyOption="modifyOption", @next="next()", @back="back()")
    step-three(v-show="step == 3", @calcTotal="calcTotal", @next="next()", @back="back()")
    step-four(v-show="step == 4", :planName="planName", :planPrice="planPrice", :planOption="planOption", :total="total", @changeStep="changeStep", @next="next()", @back="back()")
</template>
<script>
import stepOne from './components/stepOne.vue';
import stepTwo from './components/stepTwo.vue';
import stepThree from './components/stepThree.vue';
import stepFour from './components/stepFour.vue';
import { computed } from 'vue';
export default {
  data() {
    return {
      largeMode: false,
      step: 1,
      option: 1,
      plan: 1,
      helpers: [
        {
          name: "online service",
          desc: "access to multiplayer games",
          price: { monthly: 1, yearly: 6 },
          active: false
        },
        {
          name: "larger storage",
          desc: "extra 1TB of cloud save",
          price: { monthly: 2, yearly: 12 },
          active: false
        },
        {
          name: "customizable profile",
          desc: "custom theme on your profile",
          price: { monthly: 2, yearly: 12 },
          active: false
        },
      ],
      planName: 'arcade',
      planOption: '(monthly)',
      planPrice: '$9/mo',
      planPriceNum: 9,
      helpersPrice: null,
      total: '$9/mo',
      finish: false,
    };
  },
  provide() {
    return {
      option: computed(() => this.option),
      helpers: computed(() => this.helpers),
      plan: computed(() => this.plan)
    }
  },
  beforeMount() {
    this.checkSize();
  },
  mounted() {
    window.onresize = () => this.checkSize();
  },
  methods: {
    next() {
      ++this.step;
    },
    back() {
      --this.step;
    },
    toggleActive(e) {
      let helper = e.target.parentElement;
      helper.classList.toggle("active");
      if (helper.classList.contains("active")) {
        this.helpers.push(e.target.value);
        this.helpersPrice.push(e.target.parentElement.children[2].textContent);
      } else {
        this.helpers.push(e.target.value);
        this.helpersPrice.push(e.target.parentElement.children[2].textContent);
      }
    },
    calcTotal(helpersPrice) {
      this.total = `+$${this.planPriceNum + helpersPrice}/${(this.option==1)?'mo':'yr'}`;
    },
    checkSize() {
      if (document.body.scrollWidth >= 768) {
        this.largeMode = true;
        document.documentElement.style.setProperty("--largeMode", true);
      } else {
        this.largeMode = false;
        document.documentElement.style.setProperty("--largeMode", false);
      }
    },
    modifyOption(value) {
      this.option = value;
    }, 
    changeStep(newStep) {
      this.step = newStep;
    }, 
    updateHelpers(updateHelpers) {
      this.helpers = updateHelpers;
    },
    modifyPlan(value) {
      this.plan = value;
    }
  },
  watch: {
    step() {
      let activeLi = document.getElementById(`step-${this.step}`);
      [...activeLi.parentElement.children].forEach((el) =>
        el.classList.remove("active")
      );
      activeLi.classList.add("active");
    },
    plan() {
      this.planOption = this.option == 1 ? '(monthly)' : '(yearly)';
      if (this.plan == 1 || this.plan == 4) {
        this.planName = 'arcade';
      } else if (this.plan == 2 || this.plan == 5) {
        this.planName = 'advanced';
      } else {
        this.planName = 'pro';
      }
      this.planPrice = document.querySelectorAll(".plans")[this.option - 1].children[this.option == 1? this.plan - 1: this.plan - 4].dataset.price;
    },
    planPrice() {
      this.planPriceNum = parseInt(this.planPrice.slice(1));
      this.calcTotal();
    },
  },
  components: {
    stepOne,
    stepTwo,
    stepThree,
    stepFour
  }
};
</script>
<style lang="scss">
// Start Global
@mixin flex($justify: center, $align: center, $gap: 10px) {
  display: flex;
  justify-content: $justify;
  align-items: $align;
  gap: $gap;
}
.flex {
  @include flex();
}
@mixin border($mainColor: var(--coolGray), $hoverColor: var(--marineBlue)) {
  border: 1px solid $mainColor;
  &:focus,
  &:hover {
    border-color: $hoverColor;
  }
}
.border {
  @include border();
}
.box {
  @include border();
  transition: 0.3s;
  &.active {
    background-color: var(--magnolia);
  }
}
.next-btn,
.back-btn, 
.confirm-btn {
  position: absolute;
  bottom: 35px;
  font-weight: bold;
  cursor: pointer;
  transition: 0.3s;
  text-transform: capitalize;
  letter-spacing: 1px;
}
.next-btn,
.confirm-btn {
  right: 0;
  background-color: var(--marineBlue);
  color: var(--white);
  padding: 15px 25px;
  border-radius: var(--smallRadius);
  &:hover {
    background-color: var(--marineBlue2);
  }
}
.back-btn {
  left: 0;
  background-color: transparent;
  color: var(--marineBlue);
  padding: 15px 0;
  &:hover {
    color: var(--marineBlue2);
  }
}
.opacity-0 {
  opacity: 0;
}
@mixin transition($time: 0.3s) {
  transition: $time;
}
.transition {
  @include transition();
}
.error:not(p) {
  &,
  &:hover {
    border: 1px solid var(--strawberryRed);
  }
}
p.error {
  color: var(--strawberryRed);
  font-size: 11px;
  font-style: italic;
  margin: -15px 0 8px;
}
.v-enter-from,
.v-leave-to {
  opacity: 0;
}
.v-enter-active,
.v-leave-active {
  transition: 0.6s;
}
//End Global
.container {
  background-color: var(--white);
  padding: 20px;
  border-radius: var(--radius);
  max-width: 375px;
  position: relative;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  &.large-mode {
    @include flex(space-between, flex-start, 30px);
    max-width: 900px;
    .next-btn,
    .confirm-btn {
      right: var(--paddingTop);
    }
  }
  .sidebar {
    width: 100%;
    background-image: url("../public/bg-sidebar-mobile.svg");
    &.large-mode {
      background-image: url("../public/bg-sidebar-desktop.svg");
      min-height: var(--minHeight);
      width: 274px;
    }
    &:not(.large-mode) {
      @include flex(center, center, 20px);
    }
    padding: var(--paddingTop) 30px;
    border-radius: 10px;
    list-style: none;
    li {
      @include flex(space-between, center, 20px);
      color: var(--white);
      margin-bottom: 30px;
      letter-spacing: 1px;
      .num {
        width: 35px;
        height: 35px;
        border-radius: 50%;
        border: 1px solid var(--white);
        transition: 0.3s;
      }
      .heading {
        flex: 1;
        .step-num {
          color: var(--pastelBlue);
        }
      }
      &.active {
        .num {
          background-color: var(--lightBlue);
          color: var(--marineBlue);
        }
      }
    }
  }
  .content {
    height: var(--minHeight);
    max-width: 500px;
    padding-top: var(--paddingTop);
    &.large-mode {
      padding-right: 40px;
    }
    p.title {
      font-size: 35px;
      font-weight: bold;
      & + p {
        color: var(--coolGray);
        margin: 10px 0 30px;
      }
    }
    label,
    p.title {
      color: var(--marineBlue);
    }
    .step-two {
      .plan {
        @include flex(space-between, flex-start);
        flex-direction: column;
        border-radius: var(--smallRadius);
        min-height: 140px;
        flex: 1;
        cursor: pointer;
        border-radius: var(--smallRadius);
        padding: 15px 10px;
        border-width: 1.5px;
        .name {
          color: var(--marineBlue);
          font-weight: bold;
        }
        .price {
          color: var(--coolGray);
        }
      }
      .options {
        @include flex(center, center, 20px);
        background-color: var(--magnolia);
        padding: 20px;
        margin-top: 30px;
        border-radius: var(--smallRadius);
        .option {
          color: var(--coolGray);
          transition: 0.3s;
          font-weight: bold;
          &.active {
            color: var(--marineBlue);
          }
        }
        .selector {
          position: relative;
          background-color: var(--marineBlue);
          border-radius: 25px;
          width: 38px;
          height: 22px;
          input {
            position: absolute;
            left: -6px;
            top: -17px;
            width: 50px;
            accent-color: var(--white);
            appearance: none;
            background-color: transparent;
            cursor: pointer;
          }
        }
      }
    }
    .step-three {
      .box {
        margin-bottom: 15px;
        border-radius: var(--smallRadius);
        padding: 10px;
        [type="checkbox"] {
          position: relative;
          $CheckboxSize: 18px;
          width: $CheckboxSize;
          height: $CheckboxSize;
          appearance: none;
          border-radius: var(--smallRadius);
          margin: 0;
          cursor: pointer;
          background-color: #fff;
          &::before {
            content: "✓";
            position: absolute;
            left: 50%;
            top: 50%;
            width: 100%;
            height: 100%;
            font-weight: bold;
            text-align: center;
            transform: translate(-50%, -50%);
            opacity: 0;
            transition: 0.3s;
            color: #fff;
          }
          &:checked {
            background-color: var(--purplishBlue);
            &::before {
              opacity: 1;
            }
          }
        }
        .info {
          flex: 1;
          .name {
            color: var(--marineBlue);
          }
        }
      }
    }
    .step-four {
      .receipt {
          color: var(--coolGray); 
        > * {
          padding: 20px;
        }
        .items {
          background-color: var(--alabaster);
          border-radius: var(--smallRadius);
          .plan {
            @include flex(space-between);
            padding-bottom: 20px;
            color: var(--marineBlue);
            font-weight: bold;
            border-bottom: 1px solid var(--coolGray);
            a {
              display: block;
              cursor: pointer;
              text-decoration: underline;
              transition: .3s;
              color: var(--coolGray);
              &:hover, &:focus {
                color: var(--purplishBlue);
              }
            }
          }
          .helpers {
            li {
              @include flex(space-between);
              margin-top: 20px;
              .price {
                color: var(--marineBlue);
              }
            }
          }
        }
        .total {
          @include flex(space-between);
          padding-top: 20px;
          .value {
            color: var(--purplishBlue);
            font-weight: bold;
            font-size: 27px;
          }
        }
        .back-btn {
          color: var(--coolGray);
        }
      }
      .finish {
        margin: 50% 0;
        transform: translateY(-50%);
        text-align: center;
        .title {
          margin: 25px 0 15px;
        }
      }
    }
  }
}
</style>