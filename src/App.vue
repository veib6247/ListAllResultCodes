<script setup>
import { reactive, onMounted } from 'vue'

// the one variable to rule them all
const result = reactive({
  url: 'https://eu-test.oppwa.com/v1/resultcodes',
  data: '',
  error: false,
})

// fetch the list from oppwa's API
async function fetchList() {
  // reset state
  result.error = false

  try {
    // fetch!
    const rawResults = await fetch(result.url, {
      method: 'GET',
    })
    // parse
    result.data = await rawResults.json()
  } catch (error) {
    result.error = true
    console.error(error)
  }
}

// on app component mounted, run the function
onMounted(() => {
  fetchList()
})
</script>

<template>
  <section class="section">
    <div class="container">
      <h1 class="title">Result Codes</h1>
      <p class="subtitle">
        A list of all available result codes updated real-time from OPPWA's API.
      </p>

      <!-- only display table if data is available -->
      <transition>
        <div class="table-container" v-if="result.data">
          <table class="table is-striped is-hoverable is-fullwidth">
            <thead>
              <tr>
                <th>Code</th>
                <th>Description</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="result in result.data.resultCodes">
                <td class="is-family-monospace">{{ result.code }}</td>
                <td>{{ result.description }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </transition>

      <!-- if data is blank and error boolean is true -->
      <transition>
        <div
          class="notification is-danger is-light"
          v-if="result.data === '' && result.error"
        >
          Failed to fetch transaciton list.
        </div>
      </transition>

      <!-- display progress bar while data is loading -->
      <transition>
        <progress
          class="progress is-small is-primary"
          max="100"
          v-if="result.data === '' && !result.error"
        >
          15%
        </progress>
      </transition>
    </div>
  </section>
</template>

<style>
/* Generic fast transitions */
.v-enter-active {
  transition: opacity 0.5s ease;
}
.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
