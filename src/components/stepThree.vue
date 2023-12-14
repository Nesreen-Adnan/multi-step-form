<template lang="pug">
.step-three
    p.title Pick add-ons
    p Add-ons help enhance your gaming experience.
    .helpers 
        .box.flex(v-for="(helper, index) in helpers")
            input.transition.border(type="checkbox", name="helpers", value=1, @input="(helper.active = !helper.active)")
            .info 
                .name.bold.cap {{ helper.name }}
                .desc {{ helper.desc }}
            .price(v-if="option == 1", :data-price="helper.price.monthly") {{ '+$'+helper.price.monthly+'/mo' }}
            .price(v-else-if="option == 2", :data-price="helper.price.yearly") {{ '+$'+helper.price.yearly+'/yr' }} 
    button.back-btn(@click="$emit('back')") go back
    button.next-btn(@click="$emit('next'); calcHelpers()") next step
</template>
<script>
export default {
    inject: ["option", "helpers"],
    methods: {
        calcHelpers() {
            let price = 0;
            this.helpers.forEach((helper) => {
                helper.active? price += (this.option == 1)? helper.price.monthly: helper.price.yearly : price;
            })
            // this.helpersPrice = price;
            this.$emit('updateHelpers', this.helpers);
            this.$emit('calcTotal', price);
        }
    },
}
</script>