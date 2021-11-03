<template>
  <div class="cars">
    <div class="car" v-for="item in list" :key="item._id">
      <router-link to="">
        <img :src="item.product_pic_url" alt="" />
      </router-link>
      <div class="more">
        <h4>
          <router-link class="author" to="">
            {{ item.title }}
          </router-link>
        </h4>
        <span>{{ item.name }}</span>
        <strong>2021/10/20</strong>
      </div>
      <div class="num">
        <button @click="item.mount--">-</button>
        <span>{{ item.mount }}</span>
        <button @click="item.mount++">+</button>
      </div>
      <div class="del" @click="remove(index)">×</div>
    </div>
  </div>
</template>
<script>
import { mapState } from "vuex";

export default {
  data() {
    return {
      ischexkbox: false, //全选
    };
  },
  created() {
    this.$store.dispatch("LI_ST");
  },
  computed: {
    ...mapState(["list"]),
    qx() {
      return this.list.every((item) => item.ischeck);
    },
  },

  methods: {
    remove(index) {
      this.list.splice(index, 1);
    },

    move(index) {
      if (this.ischexkbox == true) {
        this.list.splice(index);
      }
    },

    chang() {
      let flase = !this.qx;
      this.list.forEach((item) => (item.ischeck = flase));
    },
  },
};
</script>
<style lang="stylus">
.cars {
  width: 90%;
  margin: auto;

  .car {
    width: auto;
    height: 200px;

    img {
      width: 14%;
      margin-right: 6%;
      float: left;
    }
  }

  .more {
    width: 30%;
    float: left;

    h4 {
      font-size: 10px;
    }

    span {
      font-size: 7px;
    }

    strong {
      font-size: 7px;
    }
  }

  .num {
    width: 30%;
    float: left;

    button {
      height: 20px;
      width: 20px;
      line-height: 20px;
      margin: auto;
    }
  }

  .del {
    width: 20%;
    float: left;
  }
}
</style>