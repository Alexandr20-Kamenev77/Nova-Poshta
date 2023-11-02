<template>
  <div class="example">
    <div class="block">
      <p class="text">Пошук за назвою міста:</p>
      <input v-model="citySearch" class="search" @input="filterCities" placeholder="Пошук міста" />
    </div>
    <div class="block">
      <p class="text">Місто:</p>
      <select v-model="city" @change="selectWarehouses" class="select">
        <option value="" disabled selected>Населений пункт</option>
        <option v-for="c in filteredCities" :key="c.CityID">{{ c.Description }}</option>
      </select>
    </div>
    <div class="block">
      <p class="text">Поштове відділення:</p>
      <select v-model="warehouse" class="select">
        <option disabled selected value="">Відділення</option>
        <option v-for="w in warehouses" :key="w.Ref">{{ w.Description }}</option>
      </select>
    </div>
    <div class="block">
      <p class="text">Ви обрали: <br>
        Населений пункт: <span class="selected">{{ city }}</span> <br>
        Поштове відділення: <span class="selected">{{ warehouse }}</span>
      </p>
    </div>
  </div>
</template>
<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';
const API_KEY = '36cc21acfa04b10c261dfb74c8ae9b31';
const API_URL = 'https://api.novaposhta.ua/v2.0/json/';
export default {
  data() {
    return {
      cities: [],
      city: '',
      warehouses: [],
      warehouse: '',
      citySearch: '',
      filteredCities: [],
    };
  },
  methods: {
    async selectWarehouses() {
      try {
        const res = await axios.post(API_URL, {
          apiKey: API_KEY,
          modelName: 'Address',
          calledMethod: 'getWarehouses',
          methodProperties: {
            CityName: this.city,
          },
        });
        this.warehouses = res.data.data;
      } catch (error) {
        console.error(error);
      }
    },
    filterCities() {
      const searchText = this.citySearch.toLowerCase();
      this.filteredCities = this.cities.filter(c => c.Description.toLowerCase().includes(searchText));
    },
  },
  async mounted() {
    try {
      const res = await axios.post(API_URL, {
        apiKey: API_KEY,
        modelName: 'Address',
        calledMethod: 'getCities',
        methodProperties: {},
      });
      this.cities = res.data.data;
    } catch (error) {
      console.error(error);
    }
  },
};
</script>