<template>
  <link href="https://fonts.googleapis.com/css?family=Raleway:300,500,700" rel="stylesheet">
  <thead>
    <th scope="col"></th>
    <th scope="col">Nr</th>
    <th scope="col">Name</th>
    <th scope="col right">Price</th>
    <th scope="col text-end">Quantity</th>
    <th scope="col text-right">Total</th>
  </thead>
  <tr v-for="(invoice_product, k) in invoice_products" :key="k">
    <slot class="trashIconContainer">
      <input class="btn btn-secondary" type="button" @click="deleteRow(k, invoice_product)" value="Remove">
    </slot>
    <td>
      <input class="form-control" type="text" v-model="invoice_product.product_no" />
    </td>
    <td>
      <select id="products" class="form-control" aria-label=".form-select-lg example" v-model="invoice_product.product_name">
        <option selected>Select product</option>
        <option value="phone">Phone</option>
        <option value="laptop">Laptop</option>
        <option value="tablet">Tablet</option>
      </select>
    </td>
    <td>
      <input class="form-control text-right" type="number" min="0" step=".01" v-model="invoice_product.product_price" @change="calculateLineTotal(invoice_product)"/>
    </td>
    <td>
      <input class="form-control text-right" type="number" min="0" step=".01" v-model="invoice_product.product_quantity" @change="calculateLineTotal(invoice_product)"/>
    </td>
    <td>
      <input readonly class="form-control text-right" type="number" min="0" step=".01" v-model="invoice_product.line_total"/>
    </td>
  </tr>
  <tfoot>
    <tr>
      <td colspan="5" class="text-right">Total</td>
      <td class="text-center">{{invoice_total}}</td>
    </tr>
    <input type='button' class="text-left btn left btn-primary" @click="addNewRow" value="Add">
  </tfoot>

</template>

<script>
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
export default {
  name: 'HelloWorld',
  data() {
    return {
      invoice_subtotal: 0,
      invoice_total: 0,
      invoice_products: [{
        product_no: '',
        product_name: '',
        product_price: '',
        product_quantity: '',
        line_total: 0
      }]
    }
  },
  methods: {
    saveInvoice() {
      console.log(JSON.stringify(this.invoice_products));
    },
    calculateTotal() {
      let subtotal, total;
      subtotal = this.invoice_products.reduce(function (sum, product) {
        let lineTotal = parseFloat(product.line_total);
        if (!isNaN(lineTotal)) {
          return sum + lineTotal;
        }
      }, 0);

      this.invoice_subtotal = subtotal.toFixed(2);

      total = subtotal;
      total = parseFloat(total);
      if (!isNaN(total)) {
        this.invoice_total = total.toFixed(2);
      } else {
        this.invoice_total = '0.00'
      }
    },
    calculateLineTotal(invoice_product) {
      let total = parseFloat(invoice_product.product_price) * parseFloat(invoice_product.product_quantity);
      if (!isNaN(total)) {
        invoice_product.line_total = total.toFixed(2);
      }
      this.calculateTotal();
    },
    deleteRow(index, invoice_product) {
      let idx = this.invoice_products.indexOf(invoice_product);
      console.log(idx, index);
      if (idx > -1) {
        this.invoice_products.splice(idx, 1);
      }
      this.calculateTotal();
      const products = JSON.parse(localStorage.getItem('Products'));
      const filtered = products.filter(product => {
        product.id !== products.length
      })
      localStorage.setItem('Products', JSON.stringify(filtered));
      // localStorage.removeItem('Products', )
    },
    addNewRow() {
      this.invoice_products.push({
        product_no: '',
        product_name: '',
        product_price: '',
        product_quantity: '',
        line_total: 0
      });
      localStorage.setItem('Products', JSON.stringify(this.invoice_products))
    }
}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
