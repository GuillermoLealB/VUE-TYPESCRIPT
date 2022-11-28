<template>

  <div class="headerContainer">
    <p class="title"> <b> Make your own Counts </b></p>
    <p>PROVE CRUD</p>
  </div>


  <form action="">
    <div class="two-input">
      <div class="input-group mb-3">
        <span class="input-group-text" id="basic-addon1">Product</span>
        <input type="text" class="form-control" placeholder="Product Name" aria-label="Username"
          aria-describedby="basic-addon1" v-model="inpProduct">
      </div>

      <div class="input-group mb-3">
        <span class="input-group-text" id="basic-addon1">$</span>
        <input type="number" class="form-control" placeholder="00.00" aria-label="Username"
          aria-describedby="basic-addon1" v-model="inpCost">
      </div>
      <select class="selected" v-model="selected">
          <option disabled  >Select one</option>
          <option selected  value=1>Accesory</option>
          <option value=2>Electronic</option>
          <option value=3>Consumables</option>
          </select>
      <button type="submit" class="send-bttn add-bttn" @click.prevent="addProduct" :disabled="disableAdd">add </button>
    </div>
  </form>

  <table class="table table-hover">
    <thead>
      <tr>
        <th scope="col">Type</th>
        <th scope="col">Product</th>
        <th scope="col">Price</th>
        <th scope="col" class="option-Tittle">Options</th>
      </tr>
    </thead>
    <tbody class="content-table">
      <tr v-for="(element, index) in dataApi" :key="index">
        <th scope="row">
          <div v-if="index != idModify">{{element.idCategory}}</div>
          <select  v-if="index == idModify" class="selected" v-model="newSelected">
          <option disabled >Select one</option>
          <option selected  value=1>Accesory</option>
          <option value=2>Electronic</option>
          <option value=3>Consumables</option></select>
        </th>
        <td>
          <div v-if="index != idModify">
            {{ element.name }}
          </div>
          <div>
            <input v-if="index == idModify" v-model="newProduct" type="string">
          </div>
        </td>
        <td>

          <div v-if="index != idModify">
            ${{ element.price }}
          </div>
          <div>
            <input v-if="index == idModify" v-model="newCost" t type="number">
          </div>

        </td>
        <td>
          <div v-if="index != idModify" class="order-icons">
            <span @click="modifyProduct(index)"><i class="bi bi-pencil"></i></span>
            <span @:click="deleteProduct(element.id)"><i class="bi bi-trash"></i></span>

          </div>

          <div v-if="index == idModify" class="button-order">
            <button class="btn btn-primary" @click="okClick(element.id, newProduct, newCost,newSelected)">Ok</button>
            <button class="btn btn-secondary" @click="nokClick">Cancel</button>
          </div>

        </td>
      </tr>

    </tbody>
  </table>
  <div class="Total">TOTAL: ${{TotalCount}}</div>
</template>
  
<script lang="ts">

import { computed, defineComponent, watch } from 'vue'
import { ref } from 'vue'

interface Response {
    id?:         number;
    name:       string | null;
    price:      number | null |"";
    idCategory: number | undefined;
    oCategory?:   null;
}
export default defineComponent({
  name: 'RegisterProduct',
  
  data() {
    return {
      newCost: ref<number>(),
      newProduct: ref<string>(),
      newSelected: ref<number>(),
      check1: ref<boolean>(true),
      check2: ref<boolean>(true),
      check3: ref<boolean>(true),
      inpProduct: "" as string | null ,
      inpCost: {} as number |null |"",
      inpCategory: {} as number | null,
      dataApi: [] as any,
      idModify: ref<number | null>(null),
      contentAPi: ref<Response>(),
      selected: ref<number>(),
      total: ref<number>()
    }
  },
  methods: {

    addProduct(): void {

      let sendInfJSONP: Response = {
        name: this.inpProduct,
        price: this.inpCost,
        idCategory: this.selected
      }
      fetch('http://localhost:5048/api/Product/Add', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(sendInfJSONP),
      }).then(() => this.listApi("http://localhost:5048/api/Product/List"))
      this.inpCost = null
      this.inpProduct = null

    },

    deleteProduct(id: number): void {
      fetch('http://localhost:5048/api/Product/Delete/' + id, { method: 'DELETE' })
        .then(() => this.listApi('http://localhost:5048/api/Product/List'));
    },

    modifyProduct(id: number): void {
      this.newCost = this.dataApi[id].price
      this.newProduct = this.dataApi[id].name
      this.newSelected = this.dataApi[id].idCategory
      this.idModify = id

    },

    okClick(index: number, product?: string, dollars?: number,category ?: number) {
      fetch('http://localhost:5048/api/Product/Modify', {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ id: index, name: product, price: dollars, idCategory:category }),
      }).then(() => this.listApi('http://localhost:5048/api/Product/List'))
      this.idModify = null
    },

    nokClick() {
      this.idModify = null
    },

    async listApi(api: string) {
      let reque = await fetch(api)
      let resultado = await reque.json()
      this.dataApi = resultado.response;
      this.contentAPi = resultado.response;
      
    },

     proveAPI() {
      // let reque = await fetch("http://localhost:5048/api/Product/List")
      // let resultado = await reque.json()
      // this.contentAPi = resultado.response;
      // console.log(this.contentAPi)
      console.log(this.selected)
      console.log(this.check3)
    }
  },

  watch: {
    inpProduct: function (): void {
      this.check1 = this.inpProduct == null || this.inpProduct == "" ? true : false;
      console.log(this.disableAdd)
    },

    inpCost: function (): void {
      this.check2 = this.inpCost == null || this.inpCost == "" ? true : false;
      console.log(this.disableAdd)
    },

    selected:function():void {

      this.check3=this.selected == undefined ?true :false
    }

  },
  created: function () {
    this.listApi("http://localhost:5048/api/Product/List")
  },

  computed: {
    disableAdd():boolean{
      
      if ((this.check2 || this.check1)==false) 
       { if (this.check3 ==false)
        return false
      else return true}
      else return true
    },

    TotalCount(): any{
      var totally =0
      for (let index = 0; index < this.dataApi.length; index++) {
        
        totally = this.dataApi[index].price + totally
      }

      return totally
      
    }
  }
})

</script>
  
<style scoped>

.title{
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  font-size: 4rem;
}

.add-bttn{
  width: 40rem;
  height: 200rem;
}
.two-input {
  display: flex;

}

.send-bttn {
  height: 38PX;
}

.selected{
  height: 37px;
  
}

.headerContainer {
  display: flex;
  justify-content: space-between;
  background-color: var(--color-title);
  color: #fcfcfc;
  padding: 3%;
}

.button-order {
  display: flex;
  justify-content: space-around;

}
.option-Tittle{
  display: flex;
  justify-content: center;
}

.order-icons {
  display: flex;
  justify-content: space-around;
}

.bi-trash:hover {
  color: #ff0000;
  transition: color 250ms, ;
}

.bi-pencil:hover {
  color: #00ff00;
  transition: color 250ms, ;
}

.Total{
  font-size: 5rem;
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  display: flex;
  justify-content: center;
}

</style>
  