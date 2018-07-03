<template>
    <div class="vue_get_address_io_wrapper">
        <div class="search_input">
            <input type="text" v-model="searchPostCode" />
            <button class="search_submit" v-on:click.prevent="find" v-if="canSearch">Search Postcode</button>
        </div>
        <div class="search_results">
            <select v-model="selectedResult" v-on:change="selectAddress">
                <option v-if="!this.searching && this.searchResults.length <= 0" disabled :value=null>Please Search for a Postcode</option>
                <option v-else-if="this.searching" disabled :value=null>Searching, Please Wait</option>
                <option v-else v-for="(address, key) in searchResults" v-bind:key="key" v-bind:value="{ address: address}">
                    {{ address[0] }}, {{ address[1] }}, {{ address[3] }}, {{ address[4] }}, {{ address[5] }}
                </option>
            </select>
        </div>
    </div>
</template>
<script>
import axios from 'axios';

export default {
    props:{
        apiKey: {
            type: String,
            required: true
        },
        format: {
            type: Boolean,
            required: false,
            default: true,
        },
        sort: {
            type: Boolean,
            required: false,
            default: true,
        }
    },
    data() {
        return {
            searchPostCode: null,
            searchResults: [],
            selectedResult: null,
            searchLat: null,
            searchLong: null,
            searching: false,
        }
    },
    computed: {
        canSearch: function() {
            if(this.searchPostCode !== null && this.searchPostCode.length > 0 )
            {
                return true;
            }

            return false;
        }
    },
    watch: {
      searchPostcode: function(value){
          if(!value) {
            this.searchPostcode = this.formatPostcode(value);
          }
      }
    },
    methods: {
        dailyUsage: function(){

        },
        find: function(){

            this.searching = true
            let postcode = this.searchPostCode.toLowerCase().replace(/\s/g,'');

            axios.get('https://api.getAddress.io/find/' + postcode, {
                params: {
                     'api-key': this.apiKey,
                     'format':this.format,
                     'sort':this.sort
                }
            })
            .then( response => {
                this.searchLat = response.data.latitude,
                this.searchLong = response.data.longitude,
                this.searchResults = response.data.addresses

                setTimeout(() => {
                    this.searching = false;
                }, 2000)
            })
            .catch( error => {
                console.log("Error in Vue getAddress: " + error);
            })
        },
        formatPostcode: function(value){
            value = value.toUpperCase();
            if(value !== null){
                var parts = value.match(/^([A-Z]{1,2}\d{1,2}[A-Z]?)\s*(\d[A-Z]{2})$/);
                if(parts !== null){
                    parts.shift();
                    return parts.join(' ');
                }
                return value;
            }
        },
        selectAddress: function(){
            let address = this.selectedResult.address;
            address.push("United Kingdom");
            address.push(this.formatPostcode(this.searchPostCode));
            this.$emit('address-selected', address);
        }
    }
}
</script>
<style>
.vue_get_address_io_wrapper{
    min-width: 100%;
}

.vue_get_address_io_wrapper .search_input{
    display: flex;
    flex-direction: row;
    margin-top: 10px;
    margin-bottom: 10px;
}

.vue_get_address_io_wrapper .search_input input{
    flex: 3;
}
.vue_get_address_io_wrapper .search_input button{
    flex: 1;
    margin-left: 10px;
    border-radius: 4px;
    border: 1px solid #4BB543;
    background-color: #4BB543;
    color: #ffffff;
}

.vue_get_address_io_wrapper .search_results{
    margin-top: 10px;
    margin-bottom: 10px;
}
</style>