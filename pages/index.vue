<template>
  <div>


    <div align="center" style="animation-name: fadeInUp; animation-fill-mode: both; animation-duration: 1s">
      <span class="text-h6" style="color: #007bff">BESCOM</span>
    </div>   

    <div style="margin-top: -8px;margin-bottom: 10px;color: #6c757d;" align="center">
      <span class="text-caption" style="font-weight: 200">List of Areas Scheduled for Power Outage in Bengaluru</span>
    </div>

    <div style="margin-top: -8px;margin-bottom: 10px;color: #6c757d;" align="center">
      <v-btn
        dense
        small
        rounded
        color="blue-grey"
        class="ma-2 white--text"
        href="https://t.me/glare_app"
        target="_blank"
      >
        Join our telegram group
        <v-icon
          right
          dark
        >
          mdi-send
        </v-icon>
      </v-btn>
    </div>      

    <div class="flex" style="animation-name: fadeInDown; animation-fill-mode: both; animation-duration: 2s">
        <v-select
          :items="items"
          v-model="date_selected"
          @input="updateData(date_selected)"
          label="Date"
          solo
          dense
        ></v-select>      

    <v-text-field
      filled
      clearable
      label="Search"
      :placeholder="search_placeholder"
      v-model="search"
      prepend-inner-icon="mdi-magnify"
      rounded
      dense
      style="margin-top: -15px;"
    >
    </v-text-field>
  
    </div>
  
    <div class="mb-6" style="animation-name: fadeInUp; animation-fill-mode: both; animation-duration: 1.4s">
      <v-simple-table class="elevation-3 rounded-lg">
        <template v-slot:default>
          <thead>
            <tr>
              <th class="text-left">Station</th>
              <th class="text-left">Time-Off</th>
              <th class="text-left">Areas Affected</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in filterData" :key="item['Sl No']">
              <td class="text-caption">
                
                <p class="text-wrap">
                  {{ item['Station Name'] }}
                </p>                
                </td>
              <td class="text-caption" style="color: #6c757d;" :style="`background-color: ${item['color_code']}`">
                {{item['From']}} - {{item['To']}}
                </td>
              <td class="text-caption">
                <p class="text-wrap">
                  {{ item['Areas affected'] }}
                </p>
                <!-- <span @click="openDialog(item)">See More</span> -->
                </td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
    </div>

    <v-overlay :value="overlay">
      <v-progress-circular indeterminate size="64"></v-progress-circular>
    </v-overlay>

  </div>
</template>

<script>
import axios from 'axios';
import schema from '../static/data/schema.json';

export default {
  name: "InspirePage",
  data() {
    return {
      expand: false,
      search_placeholder: '',
      search: '',
      date_selected: '',
      items: [],
      overlay: false,
      tdata: [],
      color_range: ['#A9FA91', '#BBFDA7', '#CAFFBA', '#D9FFCD', '#FF898B', '#FFA2A3', '#FEBFC0', '#FFD2D3']
    };
  },
  computed: {
    filterData(){
      if(this.search != null){
        return this.tdata.filter(item => {
          if(Object.keys(item).includes('Station Name') && Object.keys(item).includes('Areas affected')){
            if(item['Station Name'].length > 0 && item['Areas affected'].length > 0){
              return item['Station Name'].toLowerCase().includes(this.search.toLowerCase()) || item['Areas affected'].toLowerCase().includes(this.search.toLowerCase())
            }
          }
        });
      }else{
        return this.tdata;
      }
    },
  }, 
  methods: {
    getJsonData(dataset) {
      return import(`../static/data/${dataset}.json`).then(m => m.default || m);
    },
    async updateData(dataset){
      this.overlay = true;
      this.search = '';
      const response = await this.getJsonData(dataset);
      this.tdata = [];
      let color_code = 0;
      response.map(item => {
        if(Object.keys(item).includes('Station Name') && Object.keys(item).includes('Areas affected')){

          if(Object.keys(item).includes('From') && Object.keys(item).includes('To')){
            color_code = item['To'].split(':')[0] - item['From'].split(':')[0]
            color_code = color_code <= 7 ? this.color_range[color_code] : this.color_range[7];
          }
          item['color_code'] = color_code;
          this.tdata.push(item)
        }
      });

      setTimeout(() => {
        this.overlay = false;  
      }, 1000);
      
    }
  },

  created() {
    this.items = schema.date;
    let d = new Date();
    let current_date = d.getDate()+'-'+('0'+(d.getMonth()+1))+'-'+d.getFullYear().toString();
    this.date_selected = this.items.includes(current_date) ? current_date : this.items[0];
    this.updateData(this.date_selected);

    let places = ['Marathahalli', 'ADUGODI', 'Subramanyapura', 'Padmanabhanagar', 'BTM', 'PUTTENAHALLI'];

    setInterval(() => {
      this.search_placeholder = `Search like ${places[Math.floor(Math.random() * 6)]}`;
    }, 1200);
  }
};
</script>

<style>
.fadeInUp {
    -webkit-animation-duration: .75s;
    animation-duration: .75s;
    -webkit-animation-fill-mode: both;
    animation-fill-mode: both;
    -webkit-animation-name: fadeInUp;
    animation-name: fadeInUp;  
}

.fadeInDown {
    -webkit-animation-duration: 1.5s;
    animation-duration: 1.5s;
    -webkit-animation-fill-mode: both;
    animation-fill-mode: both;
    -webkit-animation-name: fadeInDown;
    animation-name: fadeInDown;
}

@keyframes fadeInUp {
  0% {
      opacity: 0;
      -webkit-transform: translate3d(0,20px,0);
      transform: translate3d(0,20px,0);
  }
  100% {
      opacity: 1;
      -webkit-transform: translateZ(0);
      transform: translateZ(0);
  }
}

@keyframes fadeInDown {
  0% {
      opacity: 0;
      -webkit-transform: translate3d(0,-20px,0);
      transform: translate3d(0,-20px,0);
  }
  100% {
      opacity: 1;
      -webkit-transform: translateZ(0);
      transform: translateZ(0);
  }  
}
</style>