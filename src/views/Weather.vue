<template>
  <v-container fluid fill-height>
    <v-row align="center" justify="center">
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
      for (let i = 0; i < res.data.records.location.length; i++) {
        if (
          citys.includes(
            res.data.records.location[i].parameter[0].parameterValue
          ) == false
        ) {
          citys.push(res.data.records.location[i].parameter[0].parameterValue)
        }
      }
      this.city = citys
    })
  },
  data: function() {
    return {
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
      let api =
        'https://opendata.cwb.gov.tw/api/v1/rest/datastore/O-A0001-001?Authorization=rdec-key-123-45678-011121314'
      this.axios.get(api).then((res) => {
        for (let i = 0; i < res.data.records.location.length; i++) {
          if (
            res.data.records.location[i].parameter[0].parameterValue ==
            this.selcity
          ) {
            dists.push(res.data.records.location[i].parameter[2].parameterValue)
          }
        }
        this.dist = dists
      })
    },
    showplace() {
      let places = new Array()
      let api =
        'https://opendata.cwb.gov.tw/api/v1/rest/datastore/O-A0001-001?Authorization=rdec-key-123-45678-011121314'
      this.axios.get(api).then((res) => {
        for (let i = 0; i < res.data.records.location.length; i++) {
          if (
            res.data.records.location[i].parameter[2].parameterValue ==
            this.seldist
          ) {
            places.push(res.data.records.location[i].locationName)
          }
        }
        this.place = places
      })
    }
  }
}
</script>
