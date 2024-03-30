<template>
  <div id="app">
    <div class="search">
      <div class="tit">
        <h1 class="title">Home Assignment</h1>
      </div>
      <div class="inputdiv">
        <div class="selectDiv">
          <select id="region" v-model="selectedRegion" @change="filterData">
            <option value="" selected>Select Region</option>
            <option v-for="region in regions" :key="region" :value="region">
              {{ region }}
            </option>
          </select>

          <select id="city" v-model="selectedCity" @change="filterData">
            <option value="">Select City</option>
            <option v-for="city in filteredCities" :key="city" :value="city">
              {{ city }}
            </option>
          </select>
        </div>

        <div>
          <input id="search" v-model="searchText" @input="filterData" placeholder="Search" />
        </div>
      </div>
    </div>
    <v-table height="400px" class="d-flex">
      <thead>
        <tr>
          <th class="text-left" v-for="title in titles" :key="title.text">{{ title.text }}</th>
        </tr>
      </thead>
      <tbody>
        <!-- //   responsible for rendering a table row (`<tr>`) for each company in the `filteredCompanies` array. -->
        <tr v-for="company in filteredCompanies" :key="company.store_id">
          <td class="item">{{ company.store_id }}</td>
          <td class="item">{{ company.store_title }}</td>
          <td class="item">{{ company.store_address }}</td>
          <td class="item">{{ company.city }}</td>
          <td class="item">{{ company.store_region }}</td>
        </tr>
      </tbody>
    </v-table>
  </div>
</template>

<script>
export default {
  // The `data()` method in a Vue.js component is used to define the initial data properties of the component. In this specific code snippet:
  data() {
    return {
      companies: [],
      regions: [],
      selectedRegion: '',
      selectedCity: '',
      searchText: '',
      filteredCompanies: [],
      filteredCities: [],
      titles: [
        { text: 'Id' },
        { text: 'Name' },
        { text: 'Address' },
        { text: 'City' },
        { text: 'Region' }
      ]
    }
  },
  // The `mounted()` lifecycle hook in a Vue.js component is a function that is called automatically by Vue when the component is mounted to the DOM.
  mounted() {
    this.fetchData()
  },
  methods: {
    // The `async fetchData()` method in the Vue.js component is responsible for fetching data from an external API endpoint.
    async fetchData() {
      try {
        const response = await fetch(
          'https://mcdonalds-live-engage-api-stage-1.azurewebsites.net/stores.json'
        )

        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`)
        }

        const data = await response.json()
        this.companies = data
        this.filteredCompanies = data
        this.populateRegions()
      } catch (error) {
        console.error('Error fetching data:', error)
      }
    },
    // The `populateRegions()` is responsible for populating the `regions` array with unique region names extracted from the `store_region` property of each company in the `companies` array.
    populateRegions() {
      const regions = []
      this.companies.forEach((company) => {
        if (!regions.includes(company.store_region)) {
          regions.push(company.store_region)
        }
      })
      // The code `this.regions = regions` is assigning the `regions` array, which contains unique region names extracted from the `store_region` property of each company in the `companies` array, to the `regions` data property in the Vue component. This allows the Vue component to store and access the list of unique regions available in the data.
      this.regions = regions
      this.populateCities()
    },
    // The `populateCities()` method is responsible for populating the `filteredCities` array with unique city names based on the selected region.
    populateCities() {
      const selectedRegionCompanies = this.companies.filter(
        (company) => company.store_region === this.selectedRegion
      )
      //  part of the `filterCities()` method in a Vue.js component. It is responsible for filtering the list of companies based on the selected region and extracting unique city names from the filtered list.
      const cities = []
      selectedRegionCompanies.forEach((company) => {
        const city = company.city.trim()
        if (!cities.includes(city)) {
          cities.push(city)
        }
      })
      this.filteredCities = cities
    },
    // The `filterData()`  updating the filtered data based on the selected region, city, and search text input.
    filterData() {
      this.filterCities()
      this.filterCompanies()
    },
    // The `filterCities()` method is filtering the list of companies based on the selected region. It uses the `filter()` method on the `this.companies` array to create a new array `selectedRegionCompanies` that only contains companies where the `store_region` property matches the `selectedRegion` value. This filtered list of companies is then used to extract unique city names, which are stored in the `filteredCities` array for display in the city dropdown select element .
    filterCities() {
      const selectedRegionCompanies = this.companies.filter(
        (company) => company.store_region === this.selectedRegion
      )
      // The code snippet you provided is part of the `filterCities()` method in a Vue.js component. This section of code is responsible for filtering the list of companies based on the selected region and extracting unique city names from the filtered list.
      const cities = []
      selectedRegionCompanies.forEach((company) => {
        const city = company.city.trim()
        if (!cities.includes(city)) {
          cities.push(city)
        }
      })
      this.filteredCities = cities
    },
    // The `filterCompanies()` method is responsible for filtering the list of companies based on the selected region, city, and search text input.
    filterCompanies() {
      this.filteredCompanies = this.companies.filter((company) => {
        //   part of the `filterCompanies()` . determining whether a company matches the selected region and city based on the user's input.
        const regionMatch = !this.selectedRegion || company.store_region === this.selectedRegion
        const cityMatch = !this.selectedCity || company.city.trim() === this.selectedCity
        const searchMatch =
          !this.searchText ||
          company.store_title.toLowerCase().includes(this.searchText.toLowerCase())
        return regionMatch && cityMatch && searchMatch
      })
    }
  }
}
</script>
