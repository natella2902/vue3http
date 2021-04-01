<template>
  <div class="container">
    <app-alert :alert="alert" @close="alert=null">
    </app-alert>
    <form class="card" @submit.prevent="createHuman">
      <h2>Работа с базой данных</h2>
      <div class="form-control">
        <label for="name"> Введите имя </label>
          <input id="name" type="text" v-model.trim="name">
        <button class="btn primary" :disabled="name.length === 0">Human create</button>
      </div>
    </form>
    <app-loader v-if="loading"> </app-loader>
    <app-people-list
        :people="people"
        @load="loadPeople"
        @remove="removePerson"
        v-else
    ></app-people-list>
  </div>
</template>

<script>


import AppPeopleList from "@/AppPeopleList"
import AppAlert from "@/AppAlert";
import AppLoader from "@/AppLoader";
import axios from 'axios'

export default {
  data() {
    return {
      name: '',
      people: [],
      alert: null,
      loading: false
    }
  },
  components: {
    AppPeopleList, AppAlert, AppLoader
  },
  mounted() {
    this.loadPeople()
  },
  methods: {
    async createHuman () {
      // https://vue-people-default-rtdb.firebaseio.com/people.json

      const response = await fetch('https://vue-people-default-rtdb.firebaseio.com/people.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          firstName: this.name
        })

      })

      const firebaseData = await response.json()

      this.people.push({
        firstName: this.name,
        id: firebaseData.name
      })

      this.name = ''

    },
    async loadPeople() {
      try {
        this.loading = true
        const { data } = await axios.get('https://vue-people-default-rtdb.firebaseio.com/people.json')
        if(!data) {
          this.loading = false
          throw new Error('Список людей пуст')
        }
        this.people = Object.keys(data).map(key => {
          return {
            id: key,
            ...data[key]
          }
        })
        this.loading = false
      } catch (e) {
        this.alert = {
          type: 'danger',
          title: 'Ошибка',
          text: e.message
        }
      }
    },
    async removePerson(id) {
      try {
        const name = this.people.find(person => person.id === id).firstName
        await axios.delete(`https://vue-people-default-rtdb.firebaseio.com/people/${id}.json`)
        this.people = this.people.filter(person => person.id !== id)
        this.alert = {
          type: 'primary',
          title: 'Успешно',
          text: `Успешно удален ${name}`
        }
      } catch (e) {

      }
    }
  }
}
</script>

<style scoped>

.form-control input {
  margin: 15px 0;
}

</style>
