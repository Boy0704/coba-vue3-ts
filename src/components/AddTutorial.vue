<template>
  <div class="submit-form">
    <div v-if="!submitted">
      <div class="form-group">
        <label for="title">Title</label>
        <input
          type="text"
          class="form-control"
          id="title"
          required
          v-model="tutorial.title"
          name="title"
        />
      </div>

      <div class="form-group">
        <label for="description">Description</label>
        <input
          class="form-control"
          id="description"
          required
          v-model="tutorial.description"
          name="description"
        />
      </div>

      <button @click="saveTutorial" class="btn btn-success">Submit</button>
    </div>

    <div v-else>
      <h4>You submitted successfully!</h4>
      <button class="btn btn-success" @click="newTutorial">Add</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import TutorialDataService from '@/services/TutorialDataService'
import Tutorial from '@/types/Tutorial'
import ResponseData from '@/types/ResponseData'

const tutorial = ref<Tutorial>({} as Tutorial)
const submitted = ref<boolean>(false)

const saveTutorial = () => {
  TutorialDataService.create(tutorial.value)
    .then((response: ResponseData) => {
      tutorial.value.id = response.data.id
      console.log(response.data)
      submitted.value = true
    })
    .catch((e: Error) => {
      console.log(e)
    })
}

const newTutorial = () => {
  submitted.value = false
  tutorial.value = {} as Tutorial
}
</script>

<style>
.submit-form {
  max-width: 300px;
  margin: auto;
}
</style>
