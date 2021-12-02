<template>
  <div class="container">
    <p v-if="$auth.loggedIn">Name：{{ $auth.user.name }}</p>
    <button @click="logout()">ログアウト</button>
    <input type="text" v-model="productName">
    <input type="number" v-model="productPrice">
    <button @click="addToProductList($auth.user.id, productName, productPrice)">追加</button>
    <div v-for="(product, index) in productList" :key="index">
      <p>{{product.name}}</p>
      <p>{{product.price}}</p>
      <p>{{product.updated_at}}</p>
      <input type="text" v-model="editName">
      <input type="number" v-model="editPrice">
      <button @click="updateProductList(product, editName, editPrice)">変更</button>
      <button @click="deleteProduct(product)">削除</button>
    </div>
    <select v-model="selectValue" name="" id="">
      <option v-for="(user, index) in userList" :key="index">{{user.name}}</option>
    </select>
    <p>{{productTotalPrice}}</p>
    <button @click="pageToCompare()">比べる</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      productName: null,
      productPrice: 0,
      editName: null,
      editPrice: 0,
      userList: [],
      selectValue: ''
    }
  },
  computed: {
    productList() {
      return this.$store.state.productList;
    },
    productTotalPrice() {
      return this.$store.getters.productTotalPrice;
    }
  },
  methods: {
    async addToProductList(id, productName, productPrice) {
      const sendData = {
        user_id: id,
        name: productName,
        price: productPrice,
      }
      await this.$axios.post('http://127.0.0.1:8000/api/product/store', sendData);
      this.editName = productName;
      this.editPrice = productPrice;
      this.productName = null;
      this.productPrice = 0;
      this.getUserData();

    },
    async updateProductList(product, editName, editPrice) {
      if(editName == null | editPrice == null) {
        return alert('入力してください');
      }
      const sendData ={
        id: product.id,
        name: editName,
        price: editPrice,
        user_id: product.user_id
      }
      await this.$axios.post('http://127.0.0.1:8000/api/product/update', sendData);
      this.getUserData();
    },
    async deleteProduct(product) {
      const sendData = {
        id: product.id,
        user_id: product.user_id
      }
      await this.$axios.post('http://127.0.0.1:8000/api/product/destroy', sendData);
      this.getUserData();
    },
    getUserData() {
     this.$store.dispatch('getProductList', this.$auth.user.id);
    },
    async logout() {
      try {
        await this.$auth.logout();
        this.$router.push("/login");
      } catch (error) {
        console.log(error);
      }
    },
    async getUserList() {
      const resData = await this.$axios.get('http://127.0.0.1:8000/api/list/users');
      this.userList = resData.data.data;
    },
    pageToCompare() {
      this.$router.push('/compare')
    }
  },
  created() {
    this.getUserData();
    this.getUserList();
  }
}
</script>
