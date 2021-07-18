<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Navbar</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav">
        <li class="nav-item">
          <a class="nav-link active" aria-current="page" href="#">產品</a>
        </li>
        <li class="nav-item">
          <!-- <a class="nav-link" href="#">訂單</a> -->
          <router-link class="nav-link" to="/dashboard/orders">訂單</router-link>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#">優惠卷</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#" @click.prevent="logout">登出</a>
        </li>
      </ul>
    </div>
  </div>
</nav>
</template>
<script>
export default {
  methods: {
    logout () {
      const api = `${process.env.VUE_APP_API}logout`
      this.$http.post(api).then(response => {
        if (response.data.success) {
          this.$router.push('/login')
        }
      })
    }
  }
}
</script>
