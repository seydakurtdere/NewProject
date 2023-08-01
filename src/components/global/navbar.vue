<template>
  <b-container fluid lg="px-0" class="bg-white">
    <b-row class="navsm py-2 px-xl-5 px-3">
      <b-navbar toggleable="lg">
        <b-col lg="3" sm="6" class="text-start ms-lg-2">
          <b-navbar-brand tag="h2" class="p-0 fw-bold">
            <b-link to="/">
              <img src="../../assets/sass/images/shop.png" alt="shopping" width="60" height="60">
            </b-link>
          </b-navbar-brand>
        </b-col>
        <b-navbar-toggle
          target="nav-collapse"
          class="bg-white shadow-none text-end"
        ></b-navbar-toggle>
        <b-collapse id="nav-collapse" is-nav class="justify-content-between">
          <b-col lg="6">
            <b-input-group class="mt-2 mt-lg-0 ms-lg-5">
              <template>
                <b-form-input
                  placeholder="Search..."
                  class="shadow-none rounded"
                  v-model="searchProduct"
              ></b-form-input>
                <b-button class="bg-transparent border rounded"
                  ><b-icon
                    icon="search"
                    @click="filterItems()"
                    class="text-warning fw-bold"
                  ></b-icon>
                </b-button>
              </template>
            </b-input-group>
          </b-col> 
        </b-collapse>
      </b-navbar></b-row
    >
  </b-container>
</template>
<script>
import { getAuth, signOut, onAuthStateChanged } from "firebase/auth";
import router from "../../router";
import {
  getFirestore,
  query,
  getDocs,
  collectionGroup,
} from "firebase/firestore";
const db = getFirestore();
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "navbar",
  data() {
    return {
      user: null,
      searchProduct: "",
      filteredProducts: [],
      Products: [],
    };
  },
  created() {
    const auth = getAuth();
    onAuthStateChanged(auth, (userAuth) => {
      if (userAuth) {
        this.user = userAuth;
      } else {
        this.user = null;
      }
    });
    this.getProducts();
  },
  methods: {
    logout() {
      const auth = getAuth();
      signOut(auth)
        .then(() => {
          localStorage.removeItem("User");
          router.push({
            name: "login",
          });
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async getProducts() {
      const sousCollectionRef = query(collectionGroup(db, `products`));
      const sousCollectionSnapshot = await getDocs(sousCollectionRef);
      this.Products = [];
      sousCollectionSnapshot.forEach((doc) => {
        this.Products.push({
          id: doc.id,
          name: doc.data().name,
          description: doc.data().description,
          price: doc.data().price,
          image: doc.data().image,
        });
      });
    },
    filterItems() {
      this.filteredProducts = this.Products.filter((item) => {
        return item.name
          .toLowerCase()
          .includes(this.searchProduct.toLowerCase());
      });
      this.$root.$emit("filtered-Products", this.filteredProducts);
    },
  },
};
</script>
<style lang="scss" scoped>
@import "@/assets/main.scss";
.container-fluid {
  z-index: 1001;
  position: sticky;
  .navsm {
    @media (max-width: 991px) {
      background: #FF7F00;
    }
    h2 {
      a {
        font-size: 30px;
        letter-spacing: 3px;
        color: $color-global;
        @media (max-width: 991px) {
          color: #ffc107;
        }
      }
    }
  }
  .top-bar {
    background: #f5f5f5;
    a {
      color: #6c757d;
    }
  }
  .b-dropdown {
    background: white;
  }
  .cart {
    color: white;
    font-size: 1.7rem;
  }
  .dropdown-toggle::after {
    display: none;
  }
  .user {
    .usericon {
      font-size: 2rem;
    }
    &::after {
      display: none;
    }
  }
}
</style>
