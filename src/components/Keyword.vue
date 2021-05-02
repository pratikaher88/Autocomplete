<template>
  <div>

      <div class="relative mb-3 mr-10">
        <textarea cols="100" rows="3"
          v-model="keyword"
          class="block appearance-none w-full bg-gray-200 border border-gray-200 text-gray-700 py-3 px-4 pr-8 rounded leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
          id="name"/>
        <!-- <div
          class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
          <svg
            class="h-4 w-4"
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            stroke="#000000"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <circle cx="11" cy="11" r="8"></circle>
            <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
          </svg>
        </div> -->
    
    </div>

    <button
        @click.prevent="checkName"
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 ml-3 rounded">
        Search
    </button>
      

    <ul class="px-3 list-disc">
      <li v-for="people in peoples" :key="people.url">
        {{ people.name }} - Height: {{ people.height }} Mass: {{ people.mass }}
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";
import { debounce } from "lodash";
export default {
  data: () => ({
    keyword: "",
    peoples: []
  }),

  methods: {
    checkName() {
      console.log(`Checking name: ${this.keyword}`);
      axios
        .get("https://swapi.dev/api/people/", {
          params: {
            search: this.keyword
          }
        })
        .then(res => {
          // console.log(res.data.results);
          this.peoples = res.data.results;
        })
        .catch(err => {
          console.log(err);
        });
    }
  },
  created() {
    this.debounceName = debounce(this.checkName, 1000);
  },
  watch: {
    keyword() {
      if (!this.keyword) return;
      this.debounceName();
    }
  }
};
</script>

<style>

</style>