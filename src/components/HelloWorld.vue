<script>
import axios from 'axios'
import fuzzysort from 'fuzzysort';

export default {
  data() {
    return {
      sortBy: 'sort_by',
      data: [],
      searchCountry: '',
      searchResult: [],
      totalCountry: '',
      currentPage: 1,   
      previousBtn: false,
      nextBtn: true,
      countryPerPage: 25,
      displayCountry: [],
      searchTotalPage: 1000000,
    }
  },

  created() {
    this.fetchCountry();
  },

  methods: {
    async fetchCountry() {
      const response = await axios.get(`https://restcountries.com/v3.1/all`)
      this.data = response.data
      this.searchResult = response.data

      this.totalCountry = response.data.length
      this.gotoPage(1)
    },

    search() {
      if(this.searchCountry.trim() === '') {
        this.searchResult = this.data
        this.totalCountry = this.data.length
        this.searchTotalPage = Math.ceil(this.totalCountry/this.countryPerPage)
        this.gotoPage(1)
      } else {
        const options = {
          keys: ['name.common'], // specify the keys you want to search in
        };

        // Use fuzzysort to perform fuzzy sorting
        const results = fuzzysort.go(this.searchCountry, this.data, options);

        // Extract the sorted items from the results
        const sortedItems = results.map(result => result.obj);

        this.searchResult = sortedItems;
        this.totalCountry = sortedItems.length
        this.searchTotalPage = Math.ceil(this.totalCountry/this.countryPerPage)
        this.gotoPage(1)
      }
    },

    gotoPage(page) {
      let goto = 0
      let until = this.countryPerPage 

      if(page !== 1) {
        goto = (page - 1) * this.countryPerPage 
        until = goto + this.countryPerPage
      }

      if(this.totalCountry < (page - this.currentPage) * this.countryPerPage) {
        this.displayCountry = this.searchResult.slice(goto, this.totalCountry)
      } else {
        this.displayCountry = this.searchResult.slice(goto, until)
      }

      this.currentPage = page
      if(this.currentPage !== 1) {
        this.previousBtn = true
      } else {
        this.previousBtn = false
      }

      if(this.currentPage == Math.ceil(this.totalCountry/this.countryPerPage)) {
        this.nextBtn = false 
      } else {
        this.nextBtn = true
      }
    },

    sortCountries() {
      if(this.sortBy === "ASC") {
        let sortData = this.data
        let sort = sortData.slice().sort((a, b) => a.name.common.localeCompare(b.name.common));
        this.searchResult = sort

        this.totalCountry = sort.length
        this.gotoPage(1)
      } else if(this.sortBy === "DESC") {
        let sortData = this.data
        let sort = sortData.slice().sort((a, b) => b.name.common.localeCompare(a.name.common));
        this.searchResult = sort

        this.totalCountry = sort.length
        this.gotoPage(1)
      } else {
        this.searchResult = this.data

        this.totalCountry = this.data.length
        this.gotoPage(1)
      }
    }
  }
}

</script>

<template>
  <header class="bg-[#ffda71]">
    <div class="flex justify-between gap-[50px] px-[50px] py-[20px] ">
      <!-- <div class="flex gap-[20px] items-center">
      <img src="../../src/assets/world-icon.svg" class="w-[70px] h-[70px]" />
      <div class="text-[28px] font-semibold">Countries Catalog</div>
    </div> -->

      <!-- <div class="flex">
      <input class="border border-solid border-black"/>
      <div>Search</div>
    </div> -->

      <select class="px-[10px] py-[5px] rounded-[10px] border-solid border border-[#6046fd]" v-model="sortBy"
        @change="sortCountries">
        <option value="sort_by" disabled>Sort By</option>
        <option value="default">Deafult</option>
        <option value="ASC">A-Z</option>
        <option value="DESC">Z-A</option>
      </select>

      <div class="w-full md:w-1/3">
        <form @submit.prevent="search" role="search">
          <div class="border w-full flex border-[#6046fd] border-solid rounded-[8px]">
            <input id="search" type="search" placeholder="Search..." autofocus v-model="searchCountry"
              class="outline-none w-full px-[16px] py-[5px] rounded-tl-[8px] rounded-bl-[8px]" />
            <button type="submit" class="bg-[#6046fd] px-[16px] py-[5px] rounded-tr-[7px] rounded-br-[7px]">
              <i class="fa-solid fa-magnifying-glass text-white"></i>
            </button>
          </div>

        </form>
      </div>
    </div>
  </header>

  <div class="px-[30px] py-[30px] grid grid-cols sm:grid-cols-2 lg:grid-cols-3 2xl:grid-cols-4 gap-[30px]">
    <div class="" v-for="data in displayCountry" :key="data.cnn3">
      <div class="w-full bg-white border border-gray-200 rounded-lg shadow dark:bg-gray-800 dark:border-gray-700">
        <div class="w-full h-[170px] bg-[#eaeaea]">
          <img class="rounded-t-lg w-full h-full object-contain" :src="data.flags.png" alt="" />
        </div>

        <div class="p-5">
          <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{{ data.name.official }}</h5>
          <p class="mb-2 font-normal text-gray-700 dark:text-gray-400"><span class="font-semibold">cca2/cca3:</span> {{
            data.cca2 }}/{{ data.cca3 }}</p>
          <p class="mb-2 font-normal text-gray-700 dark:text-gray-400"><span class="font-semibold">Calling Code:</span>
          <div v-for="(suffixes, id) in data.idd.suffixes" :key="id" class="inline">
            <span v-if="id != 0">, </span>
            {{ data.idd.root }}{{ suffixes }}
          </div>
          </p>

          <div v-for="(nativeName, id) in data.name.nativeName" :key="id">
            <p class="font-normal text-gray-700 dark:text-gray-400"><span class="font-semibold">{{ id }}:</span> {{
              nativeName.official }}</p>
          </div>

          <div class="mt-2">
            <div class="font-semibold text-gray-700 dark:text-gray-400 inline">Alternative name: </div>
            <div v-for="(altSpellings, id) in data.altSpellings" :key="id" class="inline mt-2">
              <p class="font-normal inline text-gray-700 dark:text-gray-400"><span v-if="id != 0"> - </span>{{
                altSpellings
              }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="container md:mb-8 pt-8 px-4 mx-auto flex justify-center select-none">
    <button :disabled="!previousBtn" :class="{ 'block border px-4 py-2 rounded-l hover:bg-gray-200 text-gray-600' : previousBtn, 'block border px-4 py-2 rounded-l text-gray-300' : !previousBtn }" @click="gotoPage(this.currentPage - 1)">&larr;</button>

    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 1, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 1}" v-if="searchTotalPage >= 1" @click="gotoPage(1)">1</button>
    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 2, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 2}" v-if="searchTotalPage >= 2" @click="gotoPage(2)">2</button>
    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 3, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 3}" v-if="searchTotalPage >= 3" @click="gotoPage(3)">3</button>
    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 4, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 4}" v-if="searchTotalPage >= 4" @click="gotoPage(4)">4</button>
    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 5, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 5}" v-if="searchTotalPage >= 5" @click="gotoPage(5)">5</button>
    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 6, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 6}" v-if="searchTotalPage >= 6" @click="gotoPage(6)">6</button>
    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 7, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 7}" v-if="searchTotalPage >= 7" @click="gotoPage(7)">7</button>
    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 8, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 8}" v-if="searchTotalPage >= 8" @click="gotoPage(8)">8</button>
    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 9, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 9}" v-if="searchTotalPage >= 9" @click="gotoPage(9)">9</button>
    <button :class="{ 'block border px-4 py-2 hover:bg-gray-200 text-gray-600' : currentPage !== 10, 'block border px-4 py-2 bg-indigo-500 hover:bg-indigo-400 text-white' : currentPage === 10}" v-if="searchTotalPage >= 10" @click="gotoPage(10)">10</button>

    <button :disabled="!nextBtn" :class="{ 'block border px-4 py-2 rounded-r hover:bg-gray-200 text-gray-600' : nextBtn, 'block border px-4 py-2 rounded-r text-gray-300' : !nextBtn }"  rel="next" @click="gotoPage(this.currentPage + 1)">&rarr;</button>
  </div>
</template>