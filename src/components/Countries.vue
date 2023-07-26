<script setup>
import {ref, reactive} from 'vue'


const countries = reactive([]);
const filterdCountries = reactive([])

let totalCountries = ref(0)
let totalFilteredCountries = ref(0)

function getAllCountries(){
  fetch('https://restcountries.com/v3.1/all')
  .then(response => response.json())
  .then(data => {
    countries.length = 0
    filterdCountries.length = 0
    data.forEach(country => {
      countries.push(country)
      filterdCountries.push(country)
    })

    totalCountries = countries.length
    totalFilteredCountries = filterdCountries.length
  })
  .catch(error => {
    console.log('Error getting country data: ', error)
  })
}

function clearCountries(){
  countries.length = 0
  filterdCountries.length = 0
}

function filterCountry(firstLetter){
  filterdCountries.length = 0
  countries.filter(c => c.name.common.startsWith(firstLetter)).forEach(element => {
    filterdCountries.push(element)    
  })

  totalFilteredCountries = filterdCountries.length
}

let text = '';

function searchCountry(text){
  filterdCountries.length = 0
  countries.filter(c => c.name.common.toLowerCase().includes(text.toLowerCase())).forEach(element => {
    filterdCountries.push(element)
  })

  totalFilteredCountries = filterdCountries.length
}

const filterButtons = reactive([])
for(let i = 65; i <= 90; i++){
  filterButtons.push(String.fromCharCode(i))
}

</script>

<template>
  <div class="container-fluid">
    <div class="card">
      <div class="card-header">
        <div class="row mb-1">
          <div class="col-md-4 offset-md-4">
            <button @click="getAllCountries()" class="btn btn-primary btn-sm mx-1">Get All Countries</button>
            <button @click="clearCountries()" class="btn btn-secondary btn-sm">Clear Countries</button>
          </div>
        </div>
        <div class="row">
          <div class="col-md-9">
            <button @click="filterCountry(button)" v-for="(button, index) in filterButtons" :key="index" class="btn btn-primary btn-sm mx-1">{{ button }}</button>
          </div>
          <div class="col-md-3">
            <input v-model="text" @keyup="searchCountry(text)" type="text" class="form-control" placeholder="Search...">
          </div>
        </div>

        <div class="row">
          <p>Shown {{ totalFilteredCountries }} of {{ totalCountries }} Countries</p>
        </div>
        
      </div>
      <div class="card-body">
        <div class="row row-cols-1 row-cols-md-5 g-4">
          <div v-for="(country, index) in filterdCountries" :key="index" class="col">
            <div class="card h-100">
              <img :src="country.flags.png" class="card-img-top" style="height:120px" :alt="country.flags.alt">
              <div class="card-body">
                <h5 class="card-title">{{ country.name.common }}</h5>
                <p class="card-text">{{ country.name.official }}</p>
                <p class="card-text">Capital: {{ country.capital }}</p>
                <button class="btn btn-primary btn-sm">View Details</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
