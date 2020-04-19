<template>
  <v-container fluid fill-height>
    <v-row align="center" justify="space-around">
      <v-card>
        <h1>Hello World :D</h1>
        <v-select
          :items="city"
          label="請選擇縣市"
          v-model="selcity"
          v-on:change="showdist()"
        ></v-select>
        <v-select
          :items="dist"
          label="請選擇鄉鎮市區"
          v-model="seldist"
          v-on:change="showplace()"
        ></v-select>
        <v-select :items="place" label="請選擇地點"></v-select>
      </v-card>
      <v-card>
        <p>Hello YoMin :D</p>
      </v-card>
    </v-row>
  </v-container>
</template>

<script>
export default {
  created: function() {
    let citys = new Array()
    let api =
      'https://opendata.cwb.gov.tw/api/v1/rest/datastore/O-A0001-001?Authorization=rdec-key-123-45678-011121314'
    this.axios.get(api).then((res) => {
      this.apidata = res.data.records.location
      for (let i = 0; i < this.apidata.length; i++) {
        if (
          citys.includes(this.apidata[i].parameter[0].parameterValue) == false
        ) {
          citys.push(this.apidata[i].parameter[0].parameterValue)
        }
      }
      this.city = citys
    })
  },
  data: function() {
    return {
      apidata: [],
      city: [],
      selcity: '',
      dist: [],
      seldist: '',
      place: []
    }
  },
  methods: {
    getMessage() {
      return 'hao123'
    },
    showdist() {
      let dists = new Array()
      for (let i = 0; i < this.apidata.length; i++) {
        if (this.apidata[i].parameter[0].parameterValue == this.selcity) {
          dists.push(this.apidata[i].parameter[2].parameterValue)
        }
      }
      this.dist = dists
    },
    showplace() {
      let places = new Array()
      for (let i = 0; i < this.apidata.length; i++) {
        if (this.apidata[i].parameter[2].parameterValue == this.seldist) {
          places.push(this.apidata[i].locationName)
        }
      }
      this.place = places
    }
  }
}
</script>
