<template>
  <div>
    <div class="jumbotron jumbotron-fluid jumbotron-bg-cover d-flex banner-img">
      <div class="container" >
        <h1 class="display-4 text-white shadow">即使風格變化，一樣井井有條</h1>
        <p class="lead"></p>
      </div>
    </div>
    <div class="container mb-4">
      <div class="bg-white ml-0">
        <nav aria-label="breadcrumb" style="">
          <ol class="breadcrumb bg-white">
            <li class="breadcrumb-item"><router-link to="/">首頁</router-link></li>
            <li class="breadcrumb-item"><router-link to="/productList">商品列表</router-link></li>
            <li class="breadcrumb-item active" aria-current="page">{{currentCategory}}</li>
          </ol>
        </nav>
      </div>
      <div class="row mb-4">
        <div class="col-md-3 mb-4">
          <ul class="list-group sticky-top" style="top:10px">
            <li class="list-group-item list-group-item-action"
            :class="{'active': item.category === currentCategory}"
             v-for="item in categories" :key="item.category"
             @click.prevent="getCategory(item.category)">
              <i :class="item.icon"></i> {{item.category}}
            </li>
          </ul>
        </div>
        <div class="col-md-9">
          <div class="row">
            <!-- 2 -->
            <div class="col-md-6 col-lg-4 mb-3" v-for="item in filterCategory" :key="item.id">
              <div class="card box-shadow h-100"
              @click.stop="$router.push(`/productList/${item.id}`)">
                <div class="card-bg-cover"
                :style="{backgroundImage: `url(${item.imageUrl})`}">
                </div>
                <div class="card-body">
                  <h5 class="card-title">{{item.title}}
                    <span v-if="item.category === '燈具'" class="text-danger h5 flicker">NEW</span>
                  </h5>
                  <h5 class="badge badge-warning">{{item.category}}</h5>
                  <p class="card-text">{{item.description}}</p>
                </div>
                <div class="card-footer d-flex align-items-center border-top-0 bg-white">
                  <div class="mr-auto">
                    <div class="font-weight-bold h5"
                    v-if="!item.origin_price">{{item.price | currency}}</div>
                    <del class="font-weight-bold"
                    v-if="item.origin_price">{{item.origin_price | currency}}</del>
                    <div class="text-success font-weight-bold h5"
                    v-if="item.origin_price">{{item.price | currency}}
                    </div>
                  </div>
                  <div>
                    <a href="#" class="btn btn-outline-primary rounded-circle"
                    @click.stop.prevent="addToCart(item.id)"
                    :class="{'disabled': singleLoading}">
                      <i class="fa fa-shopping-bag"
                      v-if="!singleLoading  || productId !== item.id"></i>
                      <i class="fas fa-spinner fa-spin"
                      v-if="singleLoading && productId === item.id"></i>
                    </a>
                  </div>
                </div>
              </div>
            </div>
            <!--  -->
          </div>
          <pagination :page="pagination" @switch="getProducts"></pagination>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
import Pagination from '../../Pagination';

export default {
  components: {
    Pagination,
  },
  data() {
    return {
      currentCategory: '全部商品',
      categories: [
        { category: '全部商品', icon: 'fas fa-align-justify' },
        { category: '各式桌椅', icon: 'fas fa-chair' },
        { category: '寢具', icon: 'fas fa-bed' },
        { category: '沙發', icon: 'fas fa-couch' },
        { category: '燈具', icon: 'fas fa-lightbulb' },
      ],
    };
  },
  methods: {
    getProducts(page = 1) { // page = 1
      this.$store.dispatch('productListModules/getProducts', page);
    },
    getCategory(selected) {
      const vm = this;
      vm.currentCategory = selected;
    },
    addToCart(id, qty = 1) {
      this.$store.dispatch('cartsModules/addToCart', { id, qty });
    },
  },
  computed: {
    filterCategory() {
      const vm = this;
      if (vm.currentCategory !== '全部商品') {
        return vm.products.filter(item => item.category === vm.currentCategory);
      }
      return vm.products;
    },
    ...mapGetters('productListModules', ['products']),
    ...mapGetters('cartsModules', ['productId', 'singleLoading']),
    ...mapGetters(['pagination']),
  },
  created() {
    if (this.$route.query.category) {
      this.currentCategory = this.$route.query.category;
      this.$router.push('/productList');
    }
    this.getProducts();
  },
};
</script>

<style lang="scss" scoped>
.banner-img {
  background-image: url('../../../assets/images/banner.jpg')
}
.list-group .active {
  background-color: rgba(128, 128, 128, 0.925);
}
.list-group-item {
  cursor: pointer;
  border: 0;
}
.card {
  cursor: pointer;
}
.card-bg-cover {
  background-size: cover;
  background-position: center center;
  height: 200px;
}
.card-bg-cover:hover {
  background-size: cover;
  background-position: center center;
  height: 200px;
  opacity: 0.8;
}
.jumbotron-bg-cover {
  background-size: cover;
  background-position: center center;
  height: 400px;
}
.box-shadow {
  box-shadow: 0 3px 5px  rgba(0, 0, 0, .16);
}
.box-shadow:hover {
  box-shadow: 0 3px 5px  rgba(0, 0, 0, 0.7);
}
@keyframes flicker {
  0%{opacity: 1;}
  50%{opacity: 0;}
  100%{opacity: 1;}
}
.flicker {
  animation: flicker 2s linear infinite;
}
</style>
