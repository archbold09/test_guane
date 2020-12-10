<template>
  <div class="container">
    <div class="row align-self-center">
      <div class="col-6 my-2 text-center">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">
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
            <h5 class="card-title">
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
            <h5 class="card-title">Accessorials</h5>
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
            <table class="table table-hover">
              <thead>
                <tr>
                  <th scope="col"><input type="checkbox" /></th>
                  <th scope="col">Hu count</th>
                  <th scope="col">Dimensions</th>
                  <th scope="col">Weight</th>
                  <th scope="col">Commodity</th>
                  <th scope="col">Stackable</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <th><input type="checkbox" /></th>
                  <td>Mark</td>
                  <td>Otto</td>
                  <td>@mdo</td>
                  <td>Otto</td>
                  <td><input type="checkbox" /></td>
                </tr>
              </tbody>
              <tfoot>
                <tr>
                  <th>Totals:</th>
                  <td>1</td>
                  <td>Units:</td>
                  <td>65 cbf</td>
                  <td>1lb</td>
                </tr>
              </tfoot>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const axios = require("axios");
const URL = `https://tt.guane.com.co/api/loads/1`;
export default {
  data() {
    return {
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
    };
  },
  created: function() {
    this.getData();
  },
  methods: {
    async getData() {
      axios
        .get(URL)
        .then((res) => res.data.hauls[0])
        .then((res) => {
          // console.log(res)
          this.cityFrom = res.city_from;
          this.cityTo = res.city_to;
          this.zipFrom = res.zip_from;
          this.zipTo = res.zip_to;
          this.accessorials.filter((item) => {
            let resultFilter = item.name
              .toLowerCase()
              .includes(res.accessorials[0].toLowerCase().replace("non-",""));
            if (resultFilter) {
              item.propertyValue = true;
            }
          });
        })
        .catch((error) => console.log(`Error:${error}`));
    },
  },
};
</script>
