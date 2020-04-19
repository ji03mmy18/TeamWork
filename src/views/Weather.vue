<template>
  <v-container fluid fill-height>
    <v-row align="center" justify="space-around">
      <v-card outlined>
        <h1>地點選擇</h1>
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
        <v-select
          :items="place"
          label="請選擇測站"
          v-model="selplace"
          v-on:change="showdata()"
        ></v-select>
      </v-card>
      <v-card outlined>
        <h1>測站資訊</h1>
        <p>地點：{{ dataset.place }}</p>
        <br />
        <p>目前溫度：{{ dataset.temp }}</p>
        <p>本日最高：{{ dataset.temp_high }}</p>
        <p>本日最低：{{ dataset.temp_low }}</p>
        <br />
        <p>相對濕度：{{ dataset.humd * 100 }}%</p>
        <p>當地氣壓：{{ dataset.pres }}</p>
        <p>海拔高度：{{ dataset.ele }}M</p>
        <br />
        <p>經度：{{ dataset.lat }}</p>
        <p>緯度：{{ dataset.lon }}</p>
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
      place: [],
      selplace: '',
      dataset: {
        place: '',
        temp: '',
        temp_low: '',
        temp_high: '',
        humd: '',
        pres: '',
        ele: '',
        lat: '',
        lon: ''
      }
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
    },
    showdata() {
      for (let i = 0; i < this.apidata.length; i++) {
        if (this.apidata[i].locationName == this.selplace) {
          this.dataset.place = this.apidata[i].locationName
          this.dataset.temp = this.apidata[i].weatherElement[3].elementValue
          this.dataset.temp_low = this.apidata[
            i
          ].weatherElement[12].elementValue
          this.dataset.temp_high = this.apidata[
            i
          ].weatherElement[10].elementValue
          this.dataset.humd = this.apidata[i].weatherElement[4].elementValue
          this.dataset.pres = this.apidata[i].weatherElement[5].elementValue
          this.dataset.ele = this.apidata[i].weatherElement[0].elementValue
          this.dataset.lat = this.apidata[i].lat
          this.dataset.lon = this.apidata[i].lon
        }
      }
    }
  }
}
</script>
