<template>
  <form>
    {{message}}
    <br>
    加减只差{{balance}}

    <slot>我是替补内容</slot>
    <table class="detial-wrap">
      <tr>
        <th>商品信息</th>
        <th>商品金额</th>
        <th>商品数量</th>
        <th>总金额</th>
        <th>编辑</th>
      </tr>
      <tr v-for="item in cartList">
        <td class="goods-detial-wrap">
          <div class="checkbox-wrap left"><span class="checkbox checked"></span></div>
          <div class="goods-detial right">
            <div class="good-img left"><img v-bind:src="item.imgPic"/></div>
            <div class="good-text left">
              <div class="name">{{item.name}}</div>
              <dl class="gifts">
                <dt>赠送：</dt>
                <dd>{{item.gifts}}</dd>
              </dl>
            </div>
          </div>
        </td>
        <td class="unitprice">{{item.price | round}}</td>
        <td class="buy-num">
          <div class="choosenum-handler"><i class="icon-minus" @click="minus(item)"></i><span
            class="countbox">{{item.count}}</span><i
            class="icon-plus" v-on:click="plus(item)"></i></div>
          <div class="stock onsell"></div>
        </td>
        <td class="amount">{{item.price * item.count | round}}</td>
        <td class="icon icon-delete"></td>
      </tr>
    </table>
    <footer class="checkout-wrap">
      <div class="total-check-wrap left">
        <div class="checkbox-wrap left"><span class="checkbox checked"></span></div>
        <div class="check-text"><span class="checked-all">全选</span><span class="unchecked-all">取消全选</span>
        </div>
      </div>
      <div class="checkout right">
        <div class="total-money"><span class="name">总金额 :</span><span
          class="amount">{{ getTotalMount() | round}}元</span></div>
        <input type="submit" value="结账" class="danger"/>
      </div>
    </footer>
  </form>
</template>

<script>
  export default {
    name: 'hello',
//    接受子组件中的message
    props: ['message'],
    data(){
      return {cartList: []}
    },
    mounted: function () {
//    dom更新，$nextTick可以确保dom的更新
      this.$nextTick(function () {
        this.getDataList();
      })
    },
    filters: {
      round: function (value) {
        return value.toFixed(2)
      }
    },
    computed: {
//    返回store中的值
      balance () {
        return this.$store.state.counts
      }
    },
    methods: {
      getDataList: function () {
        this.$http.get('static/resource/cart.json').then(function (response) {
          this.cartList = response.body.result;
        })
      },
      plus: function (item) {
        //触发vuex的action
        this.$store.dispatch('increment')
        item.count++;
        //触发change事件，通知父组件
        this.$emit('change')
      },
      minus: function (item) {
        //触发vuex的action
        this.$store.dispatch('decrement')
        if (item.count > 1) {
          item.count--;
          //触发change事件，通知父组件
          this.$emit('change')
        } else {
          item.count = 1;
        }
      },
      getTotalMount: function () {
        var sum = 0;
        this.cartList.forEach(function (value, index) {
          sum += value.count * value.price;
        });
        return sum;
      }
    }
  }
</script>
