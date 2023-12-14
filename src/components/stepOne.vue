<template lang="pug">
.step-one
    p.title Personal info
    p please provide your name, email address, and phone number 
    form(action="", method="get") 
        Transition
            p.error(v-if="formError") {{ formError }}
        - var inputsInfo = [{label: "name", placeHolder: "e.g.Stephen King"}, {label: "email address", type:"email", placeHolder: "e.g.stephenking@lorem.com"}, {label: "phone number", type: "number", placeHolder: "e.g.+1234567890"}];
        each info in inputsInfo 
            - var inputId = info.label.split(" ").join("-");
            label.cap(for=`#${inputId}`)= info.label 
            input.border.transition(type=info.type||"text", id=`#${inputId}`, name=info.label, placeholder=info.placeHolder)
            if info.type == "email" 
                Transition
                    p.error(v-show="invalidEmail") *The email address is not formatted correctly.
            if info.type == "number" 
                Transition
                    p.error(v-show="invalidNumber") *The phone number is invalid.
    button.next-btn(@click="check") next step
</template>
<script>
export default {
    data() {
        return {
            formError: null,
            invalidEmail: null,
            invalidNumber: null,
        }
    },
    methods: {
        check() {
            let inputs = [...document.querySelectorAll(".step-one input")],
                emptyInputs = 0,
                emptyInputsNames = [];
            this.invalidEmail = null;
            this.invalidNumber = null;
            this.formError = null;
            inputs.forEach((input) => {
                input.classList.remove("error");
                if (input.value == "") {
                    input.classList.add("error");
                    emptyInputs++;
                    emptyInputsNames.push(this.cappitalize(input.name));
                } else if (input.type == "email") {
                    if (!/\w@\w+[.]\w/g.test(input.value)) {
                        this.invalidEmail = true;
                        input.classList.add("error");
                    }
                } else if (input.type == "number") {
                    if (!/^\d{10}$/.test(input.value)) {
                        this.invalidNumber = true;
                        input.classList.add("error");
                    }
                }
            });
            if (emptyInputs == inputs.length) {
                this.formError = "These fields are required";
            } else if (emptyInputs) {
                this.formError = `*${emptyInputsNames.join(", and ")} \
                ${emptyInputs == 1 ? " feild is" : " fields are"} required.`;
            } else if (!this.invalidEmail && !this.invalidNumber) {
                this.$emit('next');
            }
        },
        cappitalize(text) {
            let arr = text.split(" "),
                arr2 = [];
            arr.forEach((el) => {
                arr2.push(el[0].toUpperCase() + el.substr(1));
            });
            return arr2.join(" ");
        },
    }
}
</script>
<style lang="scss">
input {
  display: block;
  border-radius: var(--smallRaduis);
  padding: 10px;
  margin: 10px 0 20px;
  width: 100%;
  caret-color: var(--marineBlue);
}
</style>