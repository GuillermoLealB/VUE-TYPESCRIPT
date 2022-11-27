<template>

  <div class="headerContainer">
    <p>Make your own Tasks</p>
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

      <button type="submit" class="send-bttn" @click.prevent="addProduct" :disabled="disableAdd">insertar </button>
    </div>
  </form>



  <!--<table class="table table-hover">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Nombre</th>
        <th scope="col">Precio</th>
        <th scope="col">Opciones</th>
      </tr>
    </thead>
    <tbody class="content-table">
      <tr v-for="(element, index) in list" :key="index" ref="list">
        <th scope="row"><input type="checkbox"></th>
        <td>
          <div v-if="!element.check">
            {{ element.product }}
          </div>
          <div v-if="element.check">
            <input type="string" v-model="newProduct">
          </div>
        </td>
        <td>

          <div v-if="!element.check">
            ${{ element.cost }}
          </div>
          <div v-if="element.check">
            <input v-model="newCost"  type="number">
          </div>
          
        </td>
        <td>
          <div class="order-icons" v-if="!element.check">
            <span @click="modifyProduct(index)"><i class="bi bi-pencil"></i></span>
            <span @:click="deleteProduct(index)"><i class="bi bi-trash"></i></span>
            <span><i class="bi bi-caret-down-square"></i></span>
            <span><i class="bi bi-caret-up-square"></i></span>
          </div>

          <div class="button-order" v-if="element.check">
            <button class="btn btn-primary" @click="okClick(index)">Ok</button>
            <button class="btn btn-secondary" @click="nokClick(index)">Cancel</button>
          </div>
        </td>
      </tr>
    </tbody>
  </table>-->



  <table class="table table-hover">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Nombre</th>
        <th scope="col">Precio</th>
        <th scope="col">Opciones</th>
      </tr>
    </thead>
    <tbody class="content-table">
      <tr v-for="(element, index) in dataApi" :key="index">
        <th scope="row"><input type="checkbox"></th>
        <td>
          <div v-if="index != idModify">
            {{ element.descripcion }}
          </div>
          <div>
            <input v-if="index == idModify" v-model="newProduct" type="string">
          </div>
        </td>
        <td>

          <div v-if="index != idModify">
            ${{ element.precio }}
          </div>
          <div>
            <input v-if="index == idModify" v-model="newCost" t type="number" >
          </div>

        </td>
        <td>
          <div v-if="index != idModify" class="order-icons">
            <span @click="modifyProduct(index)"><i class="bi bi-pencil"></i></span>
            <span @:click="deleteProduct(element.idProducto)"><i class="bi bi-trash"></i></span>
            <span><i class="bi bi-caret-down-square"></i></span>
            <span><i class="bi bi-caret-up-square"></i></span>
          </div>

          <div v-if="index == idModify" class="button-order">
            <button class="btn btn-primary" @click="okClick(element.idProducto,newProduct,newCost)">Ok</button>
            <button class="btn btn-secondary" @click="nokClick(index)">Cancel</button>
          </div>

        </td>
      </tr>

    </tbody>
  </table>

  <button @click="listApi('http://localhost:5003/api/Producto/Lista')">Api</button>
</template>
  
<script lang="ts">
import { arrayExpression, objectExpression, throwStatement } from '@babel/types';
import { computed, defineComponent, watch } from 'vue'
import { ref } from 'vue'
interface List {
  product: string | null | undefined | "",
  cost: number | undefined | "" | null,
  check: boolean
}
interface DataApi {
  descripcion?:string
  precio?:number 
  Marca?: number
}

export default defineComponent({
  name: 'RegisterProduct',

  data() {

    return {
      newCost: ref<number>(),
      newProduct: ref<string>(),
      check1: ref<boolean>(true),
      check2: ref<boolean>(true),
      checkIcon: ref<boolean>(false),
      inpProduct: ref<string | null>(null),
      inpCost: (null) as number | null | "",
      inf: ({}) as List,
      list: Array<List>(),
      dataApi: [] as any,
      idModify: ref<number| null> (null),
      checkModify: ref<boolean>(),
      contentAPi: Array<any>([this.dataApi],[this.modify])
    }
  },
  methods: {

    addProduct(): void {
      // this.inf = {
      //   product: this.inpProduct,
      //   cost: this.inpCost,
      //   check: false,
      // };
      let sendInfJSONP = {
        codigoBarra: null,
        descripcion: this.inpProduct,
        marca: null,
        idCategoria: 3,
        precio: this.inpCost,
      }
      fetch('http://localhost:5003/api/Producto/Save', {
        method: 'POST', 
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(sendInfJSONP),
      }).then(()=> this.listApi("http://localhost:5003/api/Producto/Lista"))
      
    },

    deleteProduct(id: number): void {
      //this.list.splice(value, 1);
      fetch('http://localhost:5003/api/Producto/Delete/' + id, { method: 'DELETE' })
    .then(()=> this.listApi('http://localhost:5003/api/Producto/Lista'));
    },

    modifyProduct(id: number): void {

      // fetch('http://localhost:5003/api/Producto/Modify', {
      //   method: 'PUT', 
      //   headers: {
      //     'Content-Type': 'application/json',
      //   },
      //   body: JSON.stringify({idProducto:id,Marca: "Modify"}),
      // }).then()
      this.newCost=this.dataApi[id].precio
      this.newProduct=this.dataApi[id].descripcion
      this.idModify=id
      console.log(this.idModify)
      // this.newCost = this.list[value].cost
      // this.newProduct = this.list[value].product
      // this.list[value].check = true;
    },

    okClick(index: number, product?:string, price?:number) {
      fetch('http://localhost:5003/api/Producto/Modify', {
        method: 'PUT', 
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({idProducto:index,Descripcion: product,Precio: price}),
      }).then(()=> this.listApi('http://localhost:5003/api/Producto/Lista'))
      this.idModify= null
  
      // this.list[index].cost = this.newCost
      // this.list[index].product = this.newProduct
      // this.list[index].check = false

    },

    nokClick(index: number) {
      // this.list[index].check = false
      this.idModify= null
    },

    async listApi(api: string) {
      interface Data {
        msg: string,
        plc: Array<string>
      }
      let reque = await fetch(api)
      let resultado = await reque.json()
      this.dataApi = resultado.reponse;
      this.contentAPi[0]=this.dataApi
      console.log(this.dataApi)
      
      // let jason:object= { 
      //   msg: "music",
      //   plc: ["juan"," meme","lol"]
      // }
      // let data:Data = jason 
      // for (const i of jason.plc) {
      //   console.log(data)
      // }

    }
  },

  watch: {
    inpProduct: function (): void {
      this.check1 = this.inpProduct == null || this.inpProduct == "" ? true : false;
    },

    inpCost: function (): void {
      this.check2 = this.inpCost == null || this.inpCost == "" ? true : false;

    }

  },
  created: function () {
    this.listApi("http://localhost:5003/api/Producto/Lista")
  },

  computed: {
    disableAdd() {
      if (this.check2 || this.check1)
        return true
      else return false
    }
  }
})

</script>
  
<style scoped>
.two-input {
  display: flex;

}

.send-bttn {
  height: 38PX;
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
</style>
  