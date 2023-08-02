<template>
  <div class="container p-4">
    <div class="box">
      <div class="content">
        <h3 class="title is-4">Input</h3>
      </div>

      <form class="px-4">
        <div class="field">
          <label for="provinceInput" class="label">Province</label>
          <div class="control has-icons-left">
            <span class="icon is-left is-small">
              <img :src="pinIcon" alt="" />
            </span>
            <div class="select">
              <select name="province" id="provinceInput" v-model="province">
                <option value="" disabled selected>Select your province</option>
                <option v-for="(province, index) in provinces" :key="index">{{ province }}</option>
              </select>
            </div>
          </div>
        </div>
        <div class="field">
          <label for="municipalityInput" class="label">Municipality</label>
          <div class="control has-icons-left">
            <span class="icon is-left is-small">
              <img :src="pinIcon" alt="" />
            </span>
            <div class="select">
              <select
                v-model="municipality"
                @change="getBarangays"
                name="municipality"
                id="municipalityInput"
              >
                <option value="" disabled selected>Select your municipality</option>
                <option v-for="(municipality, index) in municipalities" :key="index">
                  {{ municipality }}
                </option>
              </select>
            </div>
          </div>
        </div>
        <div class="field">
          <label for="barangayInput" class="label">Barangay</label>
          <div class="control has-icons-left">
            <span class="icon is-left is-small">
              <img :src="pinIcon" alt="" />
            </span>
            <div class="select">
              <select v-model="barangay" name="barangay" id="barangayInput">
                <option value="" disabled selected>Select your barangay</option>
                <option v-for="(brgy, index) in barangays" :key="index">
                  {{ brgy }}
                </option>
              </select>
            </div>
          </div>
        </div>

        <div class="field">
          <div class="control">
            <label for="addressInput" class="label">Address</label>
            <input
              @input="getSuggestions"
              v-model="searchText"
              class="input"
              type="text"
              id="addressInput"
              list="addresses"
            />
            <datalist id="addresses">
              <option
                v-for="(suggestion, index) in suggestions"
                :value="suggestion.address"
                :key="index"
              >
                {{ suggestion.address }}
              </option>
            </datalist>
          </div>
        </div>
        <div class="field is-grouped">
          <div class="control">
            <button type="submit" class="button is-link">Submit</button>
          </div>
          <div class="control">
            <button type="reset" class="button is-link is-light">Reset</button>
          </div>
        </div>
      </form>
      <figure class="image p-4">
        <iframe
          width="600"
          height="450"
          style="border: 0"
          loading="lazy"
          allowfullscreen
          referrerpolicy="no-referrer-when-downgrade"
          src="https://www.google.com/maps/embed/v1/place?key=AIzaSyAwn13ThmiYHeKpMG63f24oiVVsGjP_oWc
    &q=Space+Needle,Seattle+WA"
        >
        </iframe>
      </figure>
    </div>
  </div>
</template>
<script>
import pinIcon from '@/assets/map-pin.svg'
import { ref } from 'vue'

export default {
  name: 'AddressForm',
  setup() {
    let options = ref([])
    const optionsUrl =
      'https://demo.myruntime.com/website/fulfillmentClustersService/api/getPhilClusters/myruntimeWeb'
    //fetches options for provinces and municipalities
    fetch(optionsUrl)
      .then((res) => res.json())
      .then((data) => {
        options.value = data.data
      })
    return {
      options,
      pinIcon
    }
  },
  data() {
    return {
      province: '',
      municipality: '',
      barangays: [],
      barangay: '',
      searchText: '',
      suggestions: []
    }
  },
  computed: {
    // options for province
    provinces() {
      return this.options ? this.options['parentOptions'] : ['No provinces found']
    },
    // options for municipality
    municipalities() {
      return this.province
        ? [...this.options['childOptions'][this.province]].sort()
        : ['No municipalities found']
    }
  },
  methods: {
    getBarangays() {
      console.log('Retrieveing barangays')
      let url = `https://demo.myruntime.com/website/fulfillmentClustersService/api/getPhilClusterOptions/myruntimeWeb?parentOption=${this.province}&childOption=${this.municipality}`
      fetch(url)
        .then((res) => res.json())
        .then((data) => {
          this.barangays = data.data
        })
    },
    getSuggestions() {
      let url = `http://localhost:3000/autocomplete?province=${this.province}&municipality=${this.municipality}&barangay=${this.barangay}&searchText=${this.searchText}`
      fetch(url)
        .then((res) => res.json())
        .then((data) => {
          this.suggestions = data
        })
    }
  }
}
</script>

<style scoped></style>
