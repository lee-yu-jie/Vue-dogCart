<script setup>
import { ref, computed } from 'vue';

const list = ref([]);
const cart = ref([])
const discount = ref(0);
fetch('/list.json')
  .then(d => d.json())
  .then(res => {
    list.value = res;
  });

const addToCart = (item) => {
  let index = cart.value.findIndex( data => data.name === item.name)

  if( index !== -1 ) {
    cart.value[index].amount++
  }else{
    cart.value.push({
      ...item,
    amount: 1
    });
  }
}  

const sortedCart = computed(() => {
  let newCart = [...cart.value]
  let result = []

  newCart.sort((a, b) => b.price - a.price)
  newCart.forEach((item) => {
    for(let i = 0; i < item.amount ; i++){
      result.push(item.price)
    }
  })

  return result
})

const getSum = computed(() => {
  let result = 0
  let total = 0

  if(sortedCart.value.length >= 3){
    sortedCart.value.forEach((d, index) => {
      result += (index < 3) ? d * 0.8 : d ;
    })
  }else{
    cart.value.forEach((el) => {
    result += el.amount * el.price
    })
  }

  cart.value.forEach((el) => {
    total += el.amount * el.price
  })
  discount.value = Math.round(total - result)

  return Math.round(result * 100) / 100
})

const delet = (item) => {
  const index = cart.value.findIndex( data => data.name === item.name)
  if(index >= 0){
    cart.value.splice(index, 1);
  }
}
</script>

<template>
  <main>
    <header>
      <nav class="bg-light ">
        <div class="pt-3">
          <h1 class="navbar-brand fs-3 h1 text-danger text-center" href="/"><i class="fas fa-gem"></i>狗狗讓我發大財商店</h1>
          <p class="discount-text text-center">全店滿三件打八折，超過三件以價高前三折扣</p>
        </div>
      </nav>
    </header>
    <section class="container mt-4">
      <div class="row items">
        <section class=" col-6 col-md-4 col-lg-2 mb-3" v-for="item in list" :key="item">
          <div class="card">
            <img :src="item.cover" class="cart-img card-img-top" alt="">
            <div class="card-body">
              <h2 class="card-title fw-light fs-4">{{ item.name }}</h2>
              <p class="price">${{ item.price}}</p>
              <button class="btn btn-sm btn-outline-warning " @click="addToCart(item)"><i class="fa-solid fa-paw"></i></button>
            </div>
          </div>
        </section>
      </div>
      <hr>
      <section class="cart">
        <h2>購物車</h2>
        <table class="table cart-item-table">
          <thead>
            <tr>
              <th scope="col">項目</th>
              <th scope="col">數量</th>
              <th scope="col">單價</th>
              <th scope="col">小計</th>
              <th scope="col">刪除</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in cart" :key="item">
              <td>{{item.name}}</td>
              <td><input type="number" class="quantity w-100" v-model="item.amount"/></td>
              <td>${{item.price}}</td>
              <td>${{item.price * item.amount}}</td>
              <td>
                <button class="remove-item-btn btn btn-outline-danger btn-sm " @click="delet(item)">
                <i class="fas fa-trash-alt"></i>
                </button>
              </td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="2"></td>
              <td>折扣</td>
              <td><span class="discount-price">${{discount}}</span></td>
              <td></td>
            </tr>
            <tr>
              <td colspan="2"></td>
              <td>總價</td>
              <td><span class="total-price">${{getSum}}</span></td>
              <td></td>
            </tr>
          </tfoot>
        </table>
        <button class="btn btn-lg btn-success empty-cart" @click="cart = []"><i class="fas fa-baby-carriage"></i> 清空購物車</button>
      </section>
    </section>
  </main>
</template>
