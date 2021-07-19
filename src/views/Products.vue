<template>
  <Loading :active="isLoading"></Loading>
  <div class="text-end">
    <button class="btn btn-primary" type="button" @click="openModal(true)">建立新產品</button>
  </div>
  <table class="table mt-4">
  <thead>
    <tr>
      <th width="120">分類</th>
      <th>產品名稱</th>
      <th width="120">原價</th>
      <th width="120">售價</th>
      <th width="100">是否啟用</th>
      <th width="200">編輯</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="product in products" :key="product.id">
      <td>{{product.category}}</td>
      <td>{{product.title}}</td>
      <td class="text-right">
        {{$filters.currency(product.origin_price)}}
      </td>
      <td class="text-right">
        {{$filters.currency(product.price)}}
      </td>
      <td>
        <span class="text-success" v-if="product.is_enabled">啟用</span>
        <span class="text-muted" v-else>不啟用</span>
      </td>
      <td>
        <div class="btn-group">
          <button class="btn btn-outline-primary btn-sm" @click="openModal(false, product)">編輯</button>
          <button class="btn btn-outline-danger btn-sm" @click="openDelModal(product)">刪除</button>
        </div>
      </td>
    </tr>
  </tbody>
</table>
<Pagination :pages="pagination" @emit-updatePage="getProducts"/>
<product-modal ref="productModal" :product="tempProduct" @update-product="updateProduct"></product-modal>
<del-modal ref="delModal" :item="tempProduct" @del-item="deleteProduct"></del-modal>
</template>

<script>
import ProductModal from '../components/ProductModal.vue'
import DelModal from '../components/DelModal.vue'
import Pagination from '../components/Pagination.vue'

export default {
  components: {
    ProductModal,
    DelModal,
    Pagination
  },
  inject: ['emitter'],
  data () {
    return {
      products: [],
      pagination: {},
      tempProduct: {}, // 是要打到後端的
      isNew: false,
      isLoading: false
    }
  },
  methods: {
    openModal (isNew, item) {
      if (isNew) {
        this.tempProduct = {}
      } else {
        this.tempProduct = { ...item } // 淺拷貝
      }
      this.isNew = isNew
      const modal = this.$refs.productModal
      modal.showModal()
    },
    openDelModal (item) {
      this.tempProduct = { ...item }
      const delModal = this.$refs.delModal
      delModal.showModal()
    },
    getProducts (page = 1) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/products/?page=${page}`
      this.isLoading = true
      this.$http.get(api).then(res => {
        this.isLoading = false
        if (res.data.success) {
          this.products = res.data.products
          this.pagination = res.data.pagination
        }
      })
    },
    updateProduct (item) {
      this.tempProduct = item
      let httpMethod = 'post'
      let api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product`

      // 新增商品
      if (!this.isNew) {
        httpMethod = 'put'
        api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${item.id}`
      }
      const productComponent = this.$refs.productModal
      this.$http[httpMethod](api, { data: this.tempProduct }).then(response => {
        productComponent.hideModal()
        this.$httpMessageState(response, '更新商品狀態')
      })
    },
    deleteProduct (item) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${this.tempProduct.id}`
      this.$http.delete(api).then(res => {
        console.log(res.data)
        const delComponent = this.$refs.delModal
        delComponent.hideModal()
        this.getProducts()
      })
    }
  },
  created () {
    this.getProducts()
  }
}
</script>
