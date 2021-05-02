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
      

    <!-- <ul class="px-3 list-disc">
      <li v-for="people in peoples" :key="people.url">
        {{ people.name }} - Height: {{ people.height }} Mass: {{ people.mass }}
      </li>
    </ul> -->

    <ul class="px-3 list-disc">
      <li v-for="item in peoples" :key="item.id">
        {{ item[0] }}
      </li>
    </ul>

    

  </div>
</template>

<script>
import axios from "axios";
import { debounce } from "lodash";
import products from './data.json'


export default {
  data: () => ({
    keyword: "",
    peoples: [],
    items: products
  }),

  methods: {
    cosine(A, B){
      var dotproduct=0;
      var mA=0;
      var mB=0;
      for(var i = 0; i < A.length; i++){ // here you missed the i++
          dotproduct += (A[i] * B[i]);
          mA += (A[i]*A[i]);
          mB += (B[i]*B[i]);
      }
      mA = Math.sqrt(mA);
      mB = Math.sqrt(mB);
      var similarity = (dotproduct)/((mA)*(mB)) // here you needed extra brackets
      return similarity;

    },
    checkName() {
      console.log(`Checking name: ${this.keyword}`);

        axios({
            method: 'post',
            headers: {'content-type': 'application/json'},
            url: 'http://127.0.0.1:8125/encode',
            data: { "id": 123,"texts": [this.keyword], "is_tokenized": false}
          })
          .then((response) => {
            
              console.log('Response status', response);
              if (response.data.status == 200){
                // console.log('result', response.data.result[0]);
                let query_vec = response.data.result[0]

                var result = []

                if (this.keyword.length > 5){

                  var keys = Object.keys(this.items[0]);
                  var values =  Object.values(this.items[0])

                  for(var i = 0; i < keys.length; i++){
                    console.log("key", keys[i]);
                    var similarity = this.cosine(values[i], query_vec);
                    if (similarity > 0.4){
                      result.push([keys[i], this.cosine(values[i], query_vec)])
                    }
                  }

                  result.sort(function(a,b) {
                      return b[1]-a[1]
                  });

                  // console.log("Result", result.slice(0,3));
                  this.peoples = result.slice(0,4);

              }
            }
          })
          .catch(err => {
            console.log(err);
          });

      }

        // axios
        // .get("https://swapi.dev/api/people/", {
        //   params: {
        //     search: this.keyword
        //   }
        // })
        // .then(res => {
        //   // console.log(res.data.results);
        //   this.peoples = res.data.results;
        // })
        // .catch(err => {
        //   console.log(err);
        // });

      // }

      

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