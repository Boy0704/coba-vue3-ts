<template>
  <div v-if="currentTutorial.id" class="edit-form">
    <h4>Tutorial</h4>
    <form>
      <div class="form-group">
        <label for="title">Title</label>
        <input type="text" class="form-control" id="title" v-model="currentTutorial.title" />
      </div>
      <div class="form-group">
        <label for="description">Description</label>
        <input
          type="text"
          class="form-control"
          id="description"
          v-model="currentTutorial.description"
        />
      </div>

      <div class="form-group">
        <label><strong>Status:</strong></label>
        {{ currentTutorial.published ? 'Published' : 'Pending' }}
      </div>
    </form>

    <button
      class="btn btn-primary mr-2"
      v-if="currentTutorial.published"
      @click="updatePublished(false)"
    >
      UnPublish
    </button>
    <button v-else class="btn btn-primary mr-2" @click="updatePublished(true)">Publish</button>

    <button class="btn btn-danger mr-2" @click="deleteTutorial">Delete</button>

    <button type="submit" class="btn btn-success" @click="updateTutorial">Update</button>
    <p>{{ message }}</p>
  </div>

  <div v-else>
    <br />
    <p>Please click on a Tutorial...</p>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import TutorialDataService from '@/services/TutorialDataService'
import Tutorial from '@/types/Tutorial'
import ResponseData from '@/types/ResponseData'

const currentTutorial = ref<Tutorial>({} as Tutorial)
const message = ref('')
const router = useRouter()
const route = useRoute()

const getTutorial = (id: number) => {
  TutorialDataService.get(id)
    .then((response: ResponseData) => {
      currentTutorial.value = response.data
      console.log(response.data)
    })
    .catch((e: Error) => {
      console.log(e)
    })
}

const updatePublished = (status: boolean) => {
  const data = {
    id: currentTutorial.value.id,
    title: currentTutorial.value.title,
    description: currentTutorial.value.description,
    published: status
  }

  TutorialDataService.update(currentTutorial.value.id, data)
    .then((response: ResponseData) => {
      console.log(response.data)
      currentTutorial.value.published = status
      message.value = 'The status was updated successfully!'
    })
    .catch((e: Error) => {
      console.log(e)
    })
}

const updateTutorial = () => {
  TutorialDataService.update(currentTutorial.value.id, currentTutorial.value)
    .then((response: ResponseData) => {
      console.log(response.data)
      message.value = 'The tutorial was updated successfully!'
    })
    .catch((e: Error) => {
      console.log(e)
    })
}

const deleteTutorial = () => {
  TutorialDataService.delete(currentTutorial.value.id)
    .then((response: ResponseData) => {
      console.log(response.data)
      router.push({ name: 'tutorials' })
    })
    .catch((e: Error) => {
      console.log(e)
    })
}

onMounted(() => {
  message.value = ''
  getTutorial(Number(route.params.id))
})
</script>

<style>
.edit-form {
  max-width: 300px;
  margin: auto;
}
</style>
