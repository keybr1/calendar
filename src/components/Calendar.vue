<template>
  <v-container class="d-flex justify-center">
    <Loader v-if="loading" />
    <v-row 
      class="d-flex flex-column align-center"
      v-else
    >
      <div>
        <v-date-picker
          v-model="dates"
          multiple
          no-title
          class="mt-4"
          min="2016-06-15"
          max="2022-03-20"
          elevation="15"
        >
        </v-date-picker>

        <div class="d-flex justify-end mt-2">
          
            <v-btn
              class="mr-2"
              elevation="2"
            >RESET</v-btn>
          
          
            <v-btn
              class="mr-3"
              color="primary"
              elevation="11"
              @click="saveDates"
            >SAVE</v-btn>
          
        </div>
      </div>
    </v-row>
  </v-container>
</template>

<script>
import Loader from './Loader';
export default {
    name: 'Calendar',

    data() {
      return {
        dates: [],
        arrayFreeDays: null,
        loading: true,
      }
    },
    components: {
      Loader
    },
    methods: {
      async saveDates() {
        console.log(this.dates);
        const data = this.createObjDays(this.dates)
        console.log(data);
        
          const res = await fetch('http://test.unit.homestretch.ch/save', {
            method: 'POST',
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(data)
          }).then((res) => {
            if(res.status >= 400 && res.status < 600) {
              throw new Error("Bad res from server");
            }
            return res
          }).then(newRes => {
            console.log(newRes.status);
            const newData = await newRes.json()
            console.log(newData.message);
          }).catch((error) => {
            console.log(error)
          });
      },
      createObjDays(arr) {
        let datesObj = []
        for (let i = 0; i < arr.length; i++) {
          let customObject = {
            date: arr[i],
            value: true
          }
        datesObj.push(customObject);
        }
      console.log(datesObj);
      return datesObj
      }
    },
    async mounted() {
      try {
        const res = await fetch('http://test.unit.homestretch.ch/')
        if (!res.ok) {
          throw new Error(res.status);
        }
        this.arrayFreeDays = await res.json()
        this.dates = this.arrayFreeDays
        console.log(this.arrayFreeDays);
        this.createObjDays(this.arrayFreeDays)
        this.loading = false
      } catch(e) {
        console.log(e);
      }
    }
  }

</script>
