<template>
  <div class="container">
    <div class="p-20">
      <h3 class="mb-10">Formulaire</h3>
      <form @submit="mySubmit">
        <input v-model="nameValue" class="mr-10" type="text" placeholder="Prénom" />
        <input v-model="emailValue" class="mr-10" type="text" placeholder="Email" />
        <button class="btn btn-primary">Sauvegarder</button>
      </form>
    </div>
    <div class="p-20">
      <h3>Liste des utilisateurs</h3>
      <ul>
        <li v-for="user in state.users">
          <p class="mr-10">{{ user.name }} - {{ user.email }}</p>
          <button @click="deleteUser(user._id)" type="button" class="btn btn-danger">
            Supprimer
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useForm, useField } from 'vee-validate'
import { reactive } from 'vue'

interface User {
  name: string
  email: string
  createdAt?: string
  _id?: string
}

const state = reactive<{ users: User[] }>({
  users: [],
})

const { handleSubmit, resetForm } = useForm()

const mySubmit = handleSubmit(async (value) => {
  try {
    const response = await fetch('https://restapi.fr/api/vueusers', {
      method: 'POST',
      body: JSON.stringify(value),
      headers: {
        'Content-Type': 'application/json',
      },
    })
    const user: User = await response.json()
    state.users.push(user)
    resetForm()
  } catch (err) {
    console.error(err)
  }
})

const { value: emailValue } = useField('email')
const { value: nameValue } = useField('name')

async function fetchUsers() {
  try {
    const response = await fetch('https://restapi.fr/api/vueusers')
    const users: User | User[] = await response.json()
    if (users) {
      state.users = Array.isArray(users) ? users : [users]
    }
  } catch (err) {
    console.error(err)
  }
}

async function deleteUser(userId?: string) {
  try {
    if (userId) {
      await fetch(`https://restapi.fr/api/vueusers?id=${userId}`, {
        method: 'DELETE',
      })
      state.users = state.users.filter((user) => user._id !== userId)
    }
  } catch (err) {
    console.error(err)
  }
}

fetchUsers()
</script>

<style lang="scss">
@use './assets/scss/base.scss' as *;
</style>
