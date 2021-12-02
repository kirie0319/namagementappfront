<template>
  <div class="container">
    <p v-if="$auth.loggedIn">Name：{{ $auth.user.name }}</p>
    <button @click="logout()">ログアウト</button>
    <select v-model="selectValue" name="" id="">
      <option :value="user.id" v-for="(user, index) in userList" :key="index">{{user.name}}</option>
    </select>
    <p>{{selectValue}}</p>
    <button @click="select(selectValue)">選ぶ</button>
    <button @click="compare(  productTotalPrice, otherTotalPrice)">比べる</button>
    <p>{{productTotalPrice}}</p>
    <p>{{otherTotalPrice}}</p>
    <p>{{compareTotalPrice}}</p>
    <button @click="pageToIndex()">戻る</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
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
    },
    otherTotalPrice() {
      return this.$store.getters.otherTotalPrice;
    },
    compareTotalPrice() {
      return this.$store.state.compareTotalPrice;
    }
  },
  methods: {
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
    pageToIndex() {
      this.$router.push('/')
    },
    select(value) {
      this.$store.dispatch('getOtherUserList', value);
    },
    compare(myTotal, otherTotal) {
      this.$store.commit('selectOtherTotalPrice', {myTotal, otherTotal});
    }
  },
  created() {
    this.getUserData();
    this.getUserList();
  }
}
</script>
