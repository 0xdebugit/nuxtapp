<template>
  <div>
    <div
      align="center"
      style="
        animation-name: fadeInUp;
        animation-fill-mode: both;
        animation-duration: 1s;
      "
    >
      <span class="text-h6" style="color: #007bff">BESCOM</span>
    </div>

    <div
      style="margin-top: -8px; margin-bottom: 10px; color: #6c757d"
      align="center"
    >
      <span class="text-caption" style="font-weight: 200"
        >List of Areas Scheduled for Power Outage in Bengaluru</span
      >
    </div>

    <div
      style="margin-top: -8px; margin-bottom: 10px; color: #6c757d"
      align="center"
    >
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
        <v-icon right dark> mdi-send </v-icon>
      </v-btn>
    </div>

    <div
      class="flex"
      style="
        animation-name: fadeInDown;
        animation-fill-mode: both;
        animation-duration: 2s;
      "
    >
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
        style="margin-top: -15px"
      >
      </v-text-field>
    </div>

    <v-card class="mx-auto text-center" color="#8f8f8f" v-show="false">
      <v-card-text>
        <v-sheet color="rgba(0, 0, 0, .12)">
          <v-sparkline
            :value="dailyCount"
            :gradient="gradient"
            :smooth="radius || false"
            :padding="padding"
            :line-width="width"
            :stroke-linecap="lineCap"
            :gradient-direction="gradientDirection"
            :fill="fill"
            :type="type"
            :auto-line-width="autoLineWidth"
            auto-draw
          >
          </v-sparkline>
        </v-sheet>
      </v-card-text>

      <v-card-text>
        <div class="text-body font-weight-thin text-white">Scheduled Power Outage till 28th Feb</div>
      </v-card-text>

      <v-card-text>
        <div class="text-body font-weight-thin text-white">Scheduled Power Outage till 28th Feb</div>
      </v-card-text>
    </v-card>

    <div
      class="mb-6"
      style="
        animation-name: fadeInUp;
        animation-fill-mode: both;
        animation-duration: 1.4s;
      "
    >
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
                  {{ item["Station "] }}
                </p>
              </td>
              <td
                class="text-caption"
                style="color: #6c757d"
                :style="`background-color: ${item['color_code']}`"
              >
                {{ item["From "] }} - {{ item["To"] }}
              </td>
              <td class="text-caption">
                <p class="text-wrap">
                  {{ item["Areas Affected"] }}
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
import axios from "axios";
import schema from "../static/data/schema.json";

export default {
  name: "InspirePage",
  data() {
    return {
      expand: false,
      search_placeholder: "",
      search: "",
      date_selected: "",
      items: [],
      overlay: false,
      tdata: [],
      color_range: [
        "#A9FA91",
        "#BBFDA7",
        "#CAFFBA",
        "#D9FFCD",
        "#FF898B",
        "#FFA2A3",
        "#FEBFC0",
        "#FFD2D3",
      ],
      dailyCount: [],
      gradients: [
        ["#222"],
        ["#42b3f4"],
        ["red", "orange", "yellow"],
        ["purple", "violet"],
        ["#00c6ff", "#F0F", "#FF0"],
        ["#f72047", "#ffd200", "#1feaea"],
      ],

      width: 2,
      radius: 10,
      padding: 8,
      lineCap: "round",
      gradient: ["#f72047", "#ffd200", "#1feaaa"],
      gradientDirection: "top",
      // gradients,
      fill: false,
      type: "trend",
      autoLineWidth: false,
    };
  },
  computed: {
    filterData() {
      if (this.search != null) {
        return this.tdata.filter((item) => {
          if (
            Object.keys(item).includes("Station ") &&
            Object.keys(item).includes("Areas Affected")
          ) {
            if (
              item["Station "].length > 0 &&
              item["Areas Affected"].length > 0
            ) {
              return (
                item["Station "]
                  .toLowerCase()
                  .includes(this.search.toLowerCase()) ||
                item["Areas Affected"]
                  .toLowerCase()
                  .includes(this.search.toLowerCase())
              );
            }
          }
        });
      } else {
        return this.tdata;
      }
    },
  },
  methods: {
    getJsonData(dataset) {
      return import(`../static/data/${dataset}.json`).then(
        (m) => m.default || m
      );
    },
    // splineConfig(date){
    //   date.forEach(async element => {
    //     let todayCount = await this.getJsonData(element);
    //     console.log(todayCount);
    //     this.dailyCount.push(todayCount.length);
    //     console.log(this.dailyCount);
    //   });
    //   console.log(this.dailyCount);
    // },
    async updateData(dataset) {
      this.overlay = true;
      // this.search = '';
      const response = await this.getJsonData(dataset);
      this.tdata = [];
      let color_code = 0;
      response.map((item) => {
        if (
          Object.keys(item).includes("Station ") &&
          Object.keys(item).includes("Areas Affected")
        ) {
          if (
            Object.keys(item).includes("From ") &&
            Object.keys(item).includes("To")
          ) {
            color_code = parseInt(item["Duration"].split(":")[0]);
            color_code =
              color_code <= 7
                ? this.color_range[color_code]
                : this.color_range[7];
          }
          item["color_code"] = color_code;
          this.tdata.push(item);
        }
      });

      setTimeout(() => {
        this.overlay = false;
      }, 1000);
    },
  },

  created() {
    this.items = schema.date;
    let d = new Date();
    let current_date =
      d.getDate() +
      "-" +
      ("0" + (d.getMonth() + 1)) +
      "-" +
      d.getFullYear().toString();
    this.date_selected = this.items.includes(current_date)
      ? current_date
      : this.items[0];
    this.updateData(this.date_selected);

    let places = [
      "Marathahalli",
      "ADUGODI",
      "Subramanyapura",
      "Padmanabhanagar",
      "BTM",
      "PUTTENAHALLI",
    ];

    // this.splineConfig(this.items);

    setInterval(() => {
      this.search_placeholder = `Search like ${
        places[Math.floor(Math.random() * 6)]
      }`;
    }, 1200);

    // setTimeout(() => {
    //   this.dailyCount = [
    //     173, 203, 208, 134, 205, 192, 254, 204, 209, 103, 192, 95, 204, 133,
    //     176, 156, 147, 63, 131, 136, 122, 122, 119, 124, 43, 43, 103,
    //   ];
    // }, 1000);
  },
};
</script>

<style>
.fadeInUp {
  -webkit-animation-duration: 0.75s;
  animation-duration: 0.75s;
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
    -webkit-transform: translate3d(0, 20px, 0);
    transform: translate3d(0, 20px, 0);
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
    -webkit-transform: translate3d(0, -20px, 0);
    transform: translate3d(0, -20px, 0);
  }
  100% {
    opacity: 1;
    -webkit-transform: translateZ(0);
    transform: translateZ(0);
  }
}
</style>