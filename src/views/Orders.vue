<template>
  <Loading :active="isLoading"></Loading>
  <table class="table mt-4">
  <thead>
    <tr>
      <th width="120">購買時間</th>
      <th>Email</th>
      <th width="120">購買款項</th>
      <th width="120">應付金額</th>
      <th width="100">是否付款</th>
      <th width="200">編輯</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="item in orders" :key="item.id">
      <td>{{item.create_at}}</td>
      <td>{{item.user.email}}</td>
      <td class="text-right">
        {{item.products}}
      </td>
      <td class="text-right">
        {{ item.total }}
      </td>
      <td>
        <div class="form-check form-switch">
          <input class="form-check-input" type="checkbox" id="flexSwitchCheckDefault" :checked="item.is_paid ? '' : disabled" :disabled="!item.is_paid">
          <label class="form-check-label" for="flexSwitchCheckDefault">{{item.is_paid ? '已付款': '未付款'}}</label>
        </div>
      </td>
      <td>
        <div class="btn-group">
          <button class="btn btn-outline-primary btn-sm" @click="openOrderModal(item)">檢視</button>
          <button class="btn btn-outline-danger btn-sm" @click="openDelModal(item)">刪除</button>
        </div>
      </td>
    </tr>
  </tbody>
</table>
<Pagination :pages="pagination" @emit-updatePage="getOrders"/>
<order-modal ref="orderModal" :order="tempOrder" @update-order="updateOrder"></order-modal>
<del-modal ref="delModal" :item="tempOrder" @del-item="deleteOrder"></del-modal>
</template>

<script>
import Pagination from '../components/Pagination.vue'
import OrderModal from '../components/OrderModal.vue'
import DelModal from '../components/DelModal.vue'
const ordersJson = {
  success: true,
  orders: [
    {
      create_at: 1523539834,
      id: '-L9u2EUkQSoEmW7QzGLF',
      is_paid: true,
      message: '這是留言',
      paid_date: 1523539924,
      payment_method: 'credit_card',
      products: {
        L8nBrq8Ym4ARI1Kog4t: {
          id: 'L8nBrq8Ym4ARI1Kog4t',
          product_id: '-L8moRfPlDZZ2e-1ritQ',
          qty: '3'
        }
      },
      total: 100,
      user: {
        address: 'kaohsiung',
        email: 'test@gmail.com',
        name: 'test',
        tel: '0912346768'
      },
      num: 1
    },
    {
      create_at: 1523539519,
      id: '-L9u11NAE0m0SpSBUDIq',
      is_paid: false,
      message: '這是留言',
      payment_method: 'credit_card',
      products: {
        L8nBrq8Ym4ARI1Kog4t: {
          id: 'L8nBrq8Ym4ARI1Kog4t',
          product_id: '-L8moRfPlDZZ2e-1ritQ',
          qty: '3'
        }
      },
      user: {
        address: 'kaohsiung',
        email: 'test@gmail.com',
        name: 'test',
        tel: '0912346768'
      },
      num: 2
    }
  ],
  pagination: {
    total_pages: 1,
    current_page: 1,
    has_pre: false,
    has_next: false,
    category: null
  },
  messages: []
}
export default {
  components: {
    Pagination,
    OrderModal,
    DelModal
  },
  data () {
    return {
      orders: [],
      pagination: {},
      tempOrder: {},
      isLoading: false,
      currentPage: 1
    }
  },
  methods: {
    getOrders (currentPage = 1) {
      // const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/orders?page=${page}`
      // this.isLoading = true
      // this.$http.get(api).then(res => {
      //   console.log(res)
      // })
      this.orders = ordersJson.orders
      this.pagination = { ...ordersJson.pagination }
      console.log(this.orders)
    },
    openOrderModal (item) {
      this.tempOrder = { ...item }
      const orderModal = this.$refs.orderModal
      orderModal.showModal()
    },
    openDelModal (item) {
      this.tempOrder = { ...item }
      const delModal = this.$refs.delModal
      delModal.showModal()
    },
    updateOrder (item) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order?${item.id}`
      this.isLoading = true
      const paid = {
        is_paid: item.is_paid
      }
      this.$http.put(api, { data: paid }).then(res => {
        this.isLoading = false
        this.getOrders(this.currentPage)
        this.$httpMessageState(res, '更新付款狀態')
      })
    },
    deleteOrder () {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order/${this.tempOrder.id}`
      this.isLoading = true
      this.$http.delete(api).then(res => {
        this.isLoading = false
        const delComponent = this.$refs.delModal
        delComponent.hideModal()
        this.getOrders(this.currentPage)
      })
    }
  },
  created () {
    this.getOrders()
  }
}
</script>
