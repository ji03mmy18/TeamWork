<template>
  <v-container fluid fill-height>
    <v-row justify="center">
      <v-card outlined class="d-flex">
        <v-card-title class="display-1 pr-12">地點選擇</v-card-title>
        <v-select
          class="pr-6"
          :items="city"
          label="請選擇縣市"
          v-model="selcity"
          v-on:change="showdist()"
        ></v-select>
        <v-select
          class="pr-6"
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
    </v-row>
    <v-row align="center" justify="space-around">
      <v-card outlined v-show="get_status">
        <v-list-item two-line>
          <v-list-item-content>
            <v-list-item-title class="headline">{{
              dataset.place
            }}</v-list-item-title>
            <v-list-item-subtitle>{{ dataset.time }}</v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
        <v-card-text>
          <v-row align="center">
            <v-col class="display-3">{{ dataset.temp }}&deg;C</v-col>
            <v-list-item-subtitle
              >{{ dataset.temp_high }}/{{
                dataset.temp_low
              }}</v-list-item-subtitle
            >
          </v-row>
        </v-card-text>

        <v-list-item>
          <v-list-item-icon>
            <v-icon>mdi-send</v-icon>
          </v-list-item-icon>
          <v-list-item-subtitle>{{ dataset.wdsd }} m/h</v-list-item-subtitle>
        </v-list-item>

        <v-divider></v-divider>

        <v-card-actions>
          <v-btn
            text
            block
            :href="
              `https://maps.google.com/maps/place/${dataset.lat},${dataset.lon}`
            "
            link
            target="_blank"
            >站點地圖</v-btn
          >
        </v-card-actions>
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
      status: false,
      dataset: {
        place: '',
        time: '',
        temp: '',
        temp_low: '',
        temp_high: '',
        wdsd: '',
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
          this.dataset.time = this.apidata[i].time.obsTime
          this.dataset.temp = this.apidata[i].weatherElement[3].elementValue
          this.dataset.temp_low = this.apidata[
            i
          ].weatherElement[12].elementValue
          this.dataset.temp_high = this.apidata[
            i
          ].weatherElement[10].elementValue
          this.dataset.wdsd = this.apidata[i].weatherElement[2].elementValue
          this.dataset.lat = this.apidata[i].lat
          this.dataset.lon = this.apidata[i].lon
        }
      }
    }
  },
  computed: {
    get_status() {
      if (this.selplace) {
        return true
      } else {
        return false
      }
    }
  }
}
</script>
