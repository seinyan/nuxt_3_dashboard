<template>
  <div>
    <h1 class="layout-auth-title">Авторизация</h1>

    <p v-show="error" class="layout-auth-error-msg">
      Неправильный логин или пароль!
    </p>

    <el-form
      label-position="top"
      ref="ruleFormRef"
      :model="form"
      :rules="rules"
      @keyup.enter="submitForm"
      status-icon>
      <el-form-item label="E-mail" prop="username">
        <el-input v-model="form.username" placeholder="E-mail"/>
      </el-form-item>
      <el-form-item label="Пароль" prop="password">
        <el-input v-model="form.password" placeholder="Пароль"/>
      </el-form-item>
      <el-form-item prop="agree">
        <el-checkbox label="Согласне на обработку персональных данных" v-model="form.agree"/>
      </el-form-item>
      <el-form-item>
        <div class="flex-center-row w-100">
          <el-button type="primary" :disabled="!form.agree" @click="submitForm">Войти</el-button>
        </div>
      </el-form-item>
    </el-form>

    <div class="layout-auth-footer-links">
      <NuxtLink :to="{ name: 'restore' }">Забыли пароль?</NuxtLink>
      <NuxtLink :to="{ name: 'register' }">Регистрация</NuxtLink>
    </div>

  </div>
</template>

<script lang="ts" setup>
definePageMeta({
  layout: 'auth'
})
import {useRouter} from "#app";
import { reactive, ref } from 'vue'
import type { FormInstance, FormRules } from 'element-plus'
import { emailRule, required, useValidateForm } from "~/utils/element.utils";
import {Login, AuthForm} from "~/composables/auth.api";

const ruleFormRef = ref<FormInstance>()
const error = ref<boolean>(false)
const form: AuthForm = reactive<AuthForm>(new AuthForm())

const rules = reactive<FormRules>({
  username: [required, emailRule],
  password: [required],
})

const submitForm = async () => {
  if (await useValidateForm(ruleFormRef.value)) {
    const res = await Login(form)
    error.value = !res
    if (res) {
      return await useRouter().push({name: 'index'})
    }
  }
}

</script>
