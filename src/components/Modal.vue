<template>
  <div>
    <button v-b-modal.modal-add class="btn btn-success btn-sm mx-4">
      Add new row
    </button>
    <b-modal
      id="modal-add"
      ref="modal"
      title="Add new product"
      @show="resetModal"
      @hidden="resetModal"
      @ok="handleOk"
    >
      <div class="container">
        <form
          class="row text-center"
          ref="form"
          @submit.stop.prevent="handleSubmit"
        >
          <div class="col-6">
            <b-form-group
              :state="productState.quantity"
              label="Quantity"
              label-for="quantity-input"
              invalid-feedback="Quantity is required"
            >
              <b-form-input
                id="quantity-input"
                v-model="product.quantity"
                :state="productState.quantity"
                required
              ></b-form-input>
            </b-form-group>
          </div>
          <div class="col-6">
            <b-form-group
              :state="productState.dimension"
              label="Dimension"
              label-for="dimension-input"
              invalid-feedback="Dimension is required"
            >
              <b-form-input
                id="dimension-input"
                v-model="product.dimension"
                :state="productState.dimension"
                required
              ></b-form-input>
            </b-form-group>
          </div>
          <div class="col-6">
            <b-form-group
              :state="productState.weight"
              label="Weight"
              label-for="weight-input"
              invalid-feedback="Weight is required"
            >
              <b-form-input
                id="weight-input"
                v-model="product.weight"
                :state="productState.weight"
                required
              ></b-form-input>
            </b-form-group>
          </div>
          <div class="col-6">
            <label for="stackable">Stackable</label>
            <br />
            <input
              type="checkbox"
              :value="product.stackable"
              v-model="product.stackable"
            />
          </div>
        </form>
      </div>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: "Modal",
  props:['tableInformation'],
  data() {
    return {
      localTable: this.tableInformation,
      product: {
        quantity: "",
        dimension: "",
        weight: "",
        stackable: false,
      },
      productState: {
        quantity: null,
        dimension: null,
        weight: null,
      },
      name: "",
      nameState: null,
    };
  },
  created:function(){
    console.log(this.localTable)
  },
  methods: {
    checkFormValidity() {
      
      const valid = this.$refs.form.checkValidity();
      this.nameState = valid;
      this.productState.quantity = valid;
      this.productState.dimension = valid;
      this.productState.weight = valid;
      return valid;
    },
    resetModal() {
      this.product.quantity = "";
      this.product.dimension = "";
      this.product.weight = "";
      this.product.stackable = false;
      this.productState.quantity = null;
      this.productState.dimension = null;
      this.productState.weight = null;
    },
    handleOk(bvModalEvt) {
      bvModalEvt.preventDefault();
      this.handleSubmit();
    },
    handleSubmit() {
      if (!this.checkFormValidity()) {
        return;
      }
      let product = {
        hu_count: this.product.quantity,
        dimensions: this.product.dimension,
        weight: this.product.weight,
        oversize: this.product.stackable,
      };
       this.localTable.push(product)
      this.$nextTick(() => {
        this.$bvModal.hide("modal-add");
      });
    },
  },
};
</script>
