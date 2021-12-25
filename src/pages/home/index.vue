<template>
  <div>
    Show me the weather
  </div>
  <button @click="getGEO">
    By my location
  </button>
  <div>
    or
  </div>
  <div>
    <input v-model="cityName" placeholder="By city name" @keydown.enter="makeAPICallByCity"/>
    <button @click="makeAPICallByCity">
      Show
    </button>
  </div>
  <div>
    <div v-if="showSpinner">
      SPINNER
    </div>
    <div v-else>
      {{weatherFetchResult}}
    </div>
  </div>

</template>

<script>
import { reactive, ref } from 'vue'
import axios from 'axios'

export default {
  name: 'index',
  setup () {
    const showSpinner = ref(false)
    const cityName = ref('')
    let coords = reactive({})
    const apikey = ref('4894b527cae585bef8e05223b5a54412')
    // TODO выпили АПИ ключ
    const weatherFetchResult = ref({})

    const getLocationData = new Promise((resolve) => {
      getCurrentGeoLocation()
      resolve(coords)
    })

    function getGEO () {
      getLocationData.then(showSpinner.value = true)
        // eslint-disable-next-line no-unused-expressions
        .catch((error) => { error.message })
    }

    function getCurrentGeoLocation () {
      if ('geolocation' in navigator) {
        navigator.geolocation.getCurrentPosition(function (position) {
          coords = { latitude: position.coords.latitude, longitude: position.coords.longitude }
          makeAPICallByGeo()
          // console.log('getCurrentGeoLocation fired') TODO Почему стреляет
        })
      } else {
        console.warn('Location service is not supported by browser')
      }
    }
    function makeAPICallByCity () {
      axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${cityName.value}&appid=${apikey.value}`).then(
        (response) => { weatherFetchResult.value = response.data }).catch((error) => {
        console.warn(error.message)
      })
    };
    function makeAPICallByGeo () {
      showSpinner.value = false
      axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${coords.latitude}&lon=${coords.longitude}&appid=${apikey.value}`).then(
        (response) => {
          console.log(response.data)
          weatherFetchResult.value = response.data
        }).catch((error) => {
        console.warn(error.message)
      })
    };

    return {
      showSpinner,
      coords,
      cityName,
      makeAPICallByCity,
      weatherFetchResult,
      getGEO
    }
  }
}
</script>

<style scoped></style>
