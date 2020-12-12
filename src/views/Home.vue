<template>
  <div class="container">
    <div class="row align-self-center">
      <div class="col-6 my-2 text-center">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title font-weight-bold">
              <i class="mdi mdi-map-marker"></i> Pickup
            </h5>
            <div class="row">
              <div class="col-6">
                <input
                  type="email"
                  class="form-control form-control-sm"
                  v-model="cityFrom"
                  placeholder="Zip from"
                />
              </div>
              <div class="col-6">
                <input
                  type="email"
                  class="form-control form-control-sm"
                  v-model="cityTo"
                  placeholder="City from"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-6 my-2 text-center">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title font-weight-bold">
              <i class="mdi mdi-map-marker"></i> Delivery
            </h5>
            <div class="row">
              <div class="col-6">
                <input
                  type="email"
                  class="form-control form-control-sm"
                  v-model="zipFrom"
                  placeholder="Zip from"
                />
              </div>
              <div class="col-6">
                <input
                  type="email"
                  class="form-control form-control-sm"
                  v-model="zipTo"
                  placeholder="City from"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-12 text-center my-2">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title font-weight-bold">Accessorials</h5>
            <div class="row">
              <div
                v-for="(item, index) in accessorials"
                :key="index"
                class="col-3 my-2"
                style="font-size:10px"
              >
                <input
                  :value="item.propertyValue"
                  v-model="item.propertyValue"
                  type="checkbox"
                />
                {{ item.name }}
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="col-12 text-center">
        <div class="card">
          <div class="card-body">
            <div class="row ml-2 mb-4">
              <button
                class="btn btn-outline-danger btn-sm"
                :disabled="
                  checkboxProductSeleted.length == 0
                    ? (disabledButton = true)
                    : (disabledButton = false)
                "
                @click="deleteSelectedProduct"
              >
                Delete
              </button>
              <Modal :table-information="tableInformation" />
            </div>
            <table class="table table-hover">
              <thead>
                <tr>
                  <th scope="col">
                    <input type="checkbox" v-model="selectAll" />
                  </th>
                  <th scope="col">Hu count</th>
                  <th scope="col">Dimensions</th>
                  <th scope="col">Weight</th>
                  <th scope="col">Stackable</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, index) in tableInformation" :key="index">
                  <th>
                    <input
                      type="checkbox"
                      v-model="checkboxProductSeleted"
                      :value="item.hu_count"
                    />
                  </th>
                  <td>{{ item.hu_count }}</td>
                  <td>{{ item.dimensions }}</td>
                  <td>{{ item.weight }}</td>
                  <td>
                    <input
                      :value="item.oversize"
                      v-model="item.oversize"
                      type="checkbox"
                      disabled
                    />
                  </td>
                </tr>
              </tbody>
            </table>
            <span>Selected: {{ checkboxProductSeleted }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const axios = require("axios");
const URL = `https://tt.guane.com.co/api/loads/1`;

import Modal from "../components/Modal";
export default {
  components: {
    Modal,
  },
  data() {
    return {
      disabledButton: true,
      checkboxProductSeleted: [],
      accessorials: [
        { propertyValue: false, name: "Hazmat" },
        { propertyValue: false, name: "Bonded" },
        { propertyValue: false, name: "Tarps" },
        { propertyValue: false, name: "Airport Delivery Non TSA" },
        { propertyValue: false, name: "Airport Delivery TSA" },
        { propertyValue: false, name: "Oversize/Overweight Permits" },
        { propertyValue: false, name: "Escorts" },
        { propertyValue: false, name: "Team Drivers" },
        { propertyValue: false, name: "Non Stackable" },
      ],
      cityFrom: "",
      cityTo: "",
      zipFrom: "",
      zipTo: "",
      tableInformation: [
        {
          hu_count: "2 Mackbook",
          dimensions: "40.0 x 100.0",
          weight: "3kl",
          oversize: true,
        },
        {
          hu_count: "1 Tv Smart",
          dimensions: "1900.0 x 768.0",
          weight: "10kl",
          oversize: false,
        },
        {
          hu_count: "10 Iphones 11 pro",
          dimensions: "1378.0 x 768.0",
          weight: "6kl",
          oversize: true,
        },
      ],
    };
  },
  created: function() {
    this.getAllData();
  },
  computed: {
    selectAll: {
      get: function() {
        return this.tableInformation
          ? this.checkboxProductSeleted.length == this.tableInformation.length
          : false;
      },
      set: function(value) {
        let selected = [];

        if (value) {
          this.tableInformation.forEach((item) => {
            selected.push(item.hu_count);
          });
        }
        this.checkboxProductSeleted = selected;
      },
    },
  },
  methods: {
    async getAllData() {
      await axios
        .get(URL)
        .then((res) => res.data.hauls[0])
        .then((res) => {
          this.cityFrom = res.city_from;
          this.cityTo = res.city_to;
          this.zipFrom = res.zip_from;
          this.zipTo = res.zip_to;
          this.accessorials.filter((item) => {
            let resultFilter = item.name
              .toLowerCase()
              .includes(res.accessorials[0].toLowerCase().replace("non-", ""));
            if (resultFilter) {
              item.propertyValue = true;
            }
          });
          res.commodity.map((item) => this.tableInformation.push(item));
        })
        .catch((error) => console.log(`Error:${error}`));
    },
    deleteSelectedProduct() {
      var array = [];
      for (var i = 0; i < this.tableInformation.length; i++) {
        var igual = false;
        for (
          var j = 0;
          (j < this.checkboxProductSeleted.length) & !igual;
          j++
        ) {
          if (
            this.tableInformation[i]["hu_count"] ==
            this.checkboxProductSeleted[j]
          )
            igual = true;
        }
        if (!igual) array.push(this.tableInformation[i]);
      }
      this.tableInformation = array;
    },
  },
};
</script>
