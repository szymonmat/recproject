<template>
  <nav class="navbar navbar-expand-sm justify-content-end navbar-dark bg-dark">
    <div class="container-fluid">
      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#navbarSupportedContent"
        aria-controls="navbarSupportedContent"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <a class="navbar-brand me-auto mb-2 mb-lg-0" href="#"
          >Shopware Listing</a
        >
        <form>
          <select
            id="productOrder"
            @change="updateSearchOrder($event.target.value)"
            class="form-select bg-dark"
            aria-label="Select order"
          >
            <option value="price-asc" selected>Najtańsze</option>
            <option value="price-desc">Najdroższe</option>
          </select>
        </form>
      </div>
    </div>
  </nav>
  <div class="container-fluid bg-white">
    <div class="row justify-content-sm-center">
      <div class="col-lg-6 col-sm-9">
        <input
          type="search"
          @keyup="updateSearchString($event.target.value)"
          class="form-control"
          id="search"
          aria-describedby="Search"
          placeholder="Szukaj..."
        />
      </div>
    </div>
  </div>
  <div class="container-lg">
    <div v-if="items && items.length > 0" id="cardList" class="bg-light">
      <ul class="row gx-3">
        <li v-for="item in items" :key="item.id" class="col-lg-4 col-sm-6">
          <div class="card">
            <div class="card-body">
              <h6 class="card-title">{{ item.translated.name }}</h6>
              <p class="card-text">{{ item.translated.description }}.</p>
              <p class="card-text price">
                <strong>Cena</strong> {{ item.calculatedPrice.unitPrice }}zł
              </p>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>

export default {
  name: "App",
  data() {
    return {
      items: [],
      searchOrder: "price-asc",
      searchString: "",
    };
  },
  components: {
  },
  methods: {
    updateSearchOrder(value) {
      this.searchOrder = value;
      this.getProductData();
    },
    updateSearchString(value) {
      this.searchString = value;
      this.getProductData();
    },
    getProductData() {
      let src, settings;
      if (this.searchString.length === 0) {
        src =
          "https://demo.crehler.dev/store-api/product-listing/e435c9763b0d44fcab67ea1c0fdb3fa0";
        settings = JSON.stringify({ order: this.searchOrder });
      } else {
        src = "https://demo.crehler.dev/store-api/search";
        settings = JSON.stringify({
          search: this.searchString,
          order: this.searchOrder,
        });
      }

      fetch(src, {
        method: "POST",
        headers: {
          "sw-access-key": "SWSCMDV3N2DIOUNZTKNNCTBBCW",
          "Content-Type": "Application/json",
        },
        body: settings,
      })
        .then((response) => {
          response.json().then((data) => {
            console.log(data);
            this.items = data.elements;
          });
        })
        .catch((err) => {
          console.error(err);
        });
    },
  },
  mounted() {
    this.getProductData();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  background-color: #f8f9fa;
  min-height: 100vh;
}
#productOrder{
  border-color:#6c757d;
  color:#6c757d;
}
#app h6 {
  font-weight: bold;
}

ul {
  padding-left: 0;
  list-style-type: none;
}
.card {
  margin-top: 1rem;
}
#search {
  margin-top: 200px;
  margin-bottom: 200px;
}
#cardList {
  padding-top: 40px;
}
</style>
