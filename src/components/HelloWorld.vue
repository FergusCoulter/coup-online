<script setup lang="ts">

import {socket} from "../../socket.js";
import {supabase} from "../../supabase.js";
import {NButton,NForm, NInput,NFormItem} from 'naive-ui'
import type {FormInst} from 'naive-ui'
import {ref} from "vue";

const formRef = ref<FormInst | null >(null);
const formValue = ref({
  email: "",
  password: "",
});
const rules = {
  email:{
    required: true,
    message: "Please enter a valid email",
    trigger: "blur"
  },
  password: {
    required: true,
    message: "Please enter your password",
    trigger: "blur"
  }

};
function handleValidateClick(e) {
  e.preventDefault()
  formRef.value?.validate(async (errors) => {
    if (!errors) {

      const { data, error } = await supabase.auth.signInWithPassword({
        email: formValue.value.email,
        password: formValue.value.password,
      });

      if (error) {
        console.error(error.message);
      } else {
        console.log('Logged in:', data);
        socket.emit('login', data.session.access_token);
      }
    }
    else {
      console.log(errors)

    }
  })}



</script>

<template>
  <n-form
      ref="formRef"
      inline
      :label-width="80"
      :model="formValue"
      :rules="rules"
  >
    <n-form-item label="Email" path="email">
      <n-input v-model:value="formValue.email" placeholder="Input Email" />
    </n-form-item>
    <n-form-item label="Age" path="password">
      <n-input v-model:value="formValue.password" type="password" placeholder="Input Password" />
    </n-form-item>
    <n-form-item>
      <n-button @click="handleValidateClick">
        Validate
      </n-button>
    </n-form-item>
  </n-form>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
