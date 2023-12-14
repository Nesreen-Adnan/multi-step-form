<template lang="pug">
.step-four
    .confirm(v-show="!finish")
        p.title Finishing up
        p double-check everything looks OK before confirming.
        .receipt 
            .items
                .plan 
                    .name.cap {{ planName + ' ' + planOption}}
                        a(@click="$emit('changeStep', 2)") change
                    .price {{ planPrice }}
                ul.helpers 
                    template(v-for="helper in helpers")
                        li(v-if="helper.active")
                            .name {{ helper.name }}
                            .price(v-if="option == 1") {{ '$'+helper.price.monthly+'/mo' }}
                            .price(v-else-if="option == 2") {{ '$'+helper.price.yearly+'/yr' }}
            .total
                .title {{ 'Total (per ' + (option==1?'month':'year') + ')' }}
                .value {{ total }}
        button.confirm-btn(@click="finish = true") confirm
        button.back-btn(@click="$emit('back')") go back
    .finish(v-show="finish")
        img(src='../../public/icon-thank-you.svg')
        p.title Thank you!
        p thanks for confirming your subscription! We hope you have fun using our platform. If you ever need support, please feel free to email us at support@loremgaming.com.
</template>
<script>
export default {
    inject: ["option", "helpers"],
    props: ["planName", "planPrice", "planOption", "total"],
    data() {
        return {
            finish: false,
        }
    },
}
</script>