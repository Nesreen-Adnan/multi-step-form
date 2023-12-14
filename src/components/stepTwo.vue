<template lang="pug">
.step-two(:key="componentKey")
    p.title Select your plan 
    p you have the option of monthly or yearly billing.
    .plans.monthly-plans.flex(v-show="option == 1")
        - var plansInfo = [{name: "arcade", price: 9}, {name: "advanced", price: 12}, {name: "pro", price: 15}]
        each planInfo, index in plansInfo 
            - var price = `$${planInfo.price}/mo`;
            mixin planContent()
                img(src=`../../public/icon-${planInfo.name}.svg`)
                .info 
                    .name.cap= planInfo.name
                    .price=price
            if index == 0
                .plan.box(ref="firstPlan", data-num=index + 1, @click="active", data-price=price)
                    +planContent()
            else
                .plan.box(data-num=index + 1, @click="active", data-price=price)
                    +planContent()
    .plans.yearly-plans.flex(v-show="option == 2")
        - var plansInfo = [{name: "arcade", price: 30}, {name: "advanced", price: 40}, {name: "pro", price: 55}]
        each planInfo, index in plansInfo
            - var price = `$${planInfo.price}/yr`;
            .plan.box(data-num=index + 4, @click="active", data-price=price)
                img(src=`../../public/icon-${planInfo.name}.svg`)
                .info 
                    .name.cap= planInfo.name 
                    .price=price
    .options
        .option.active.cap monthly
        .selector
            input(type="range", :name="option", v-model="option", @change="toggle($event)", min=1, max=2)
        .option.cap yearly
    button.back-btn(@click="$emit('back')") go back
    button.next-btn(:class="{'unavailable': !chooseOption}", @click="(chooseOption) ? $emit('next') : ''") next step
</template>
<script>
export default {
  inject: ["option", "plan"],
  data() {
    return {
      componentKey: 0,
      chooseOption: true,
    }
  },
  mounted() {
    this.$refs.firstPlan.classList.add("active");
  },
  methods: {
    active(e) {
      let el;
      if (e.target.classList.contains("plan")) {
        el = e.target;
      } else if (e.target.parentElement.classList.contains("plan")) {
        el = e.target.parentElement;
      } else {
        el = e.target.parentElement.parentElement;
      }
      let plansParents = [...document.querySelectorAll(".plans")],
        plans = [];
      plansParents.forEach((parent) => {
        plans.push(...parent.children);
      });
      plans.forEach((plan) => plan.classList.remove("active"));
      el.classList.add("active");
      this.chooseOption = true;
      this.$emit('modifyPlan', el.dataset.num);
    },
    toggle(e) {
      this.componentKey += 1;
      this.chooseOption = false;
      this.$emit('modifyOption', e.target.value)
    }
  },
};
</script>
<style lang="scss" scoped>
.unavailable {
  opacity: .6;
  cursor: not-allowed;
}
</style>
