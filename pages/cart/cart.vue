<template>
	<view class="pages">
		<view style="display: flex;">
			<image src="/static/cart.png" style="width: 100rpx; height: 100rpx;"></image>
			<view style="font-size: 60rpx;font-weight: bold; margin-left: 10rpx;">我的购物车</view>
		</view>
		<view class="card">
			<view v-if="cartList.length > 0">
			  <table class="cart-table">
			    <thead>
			      <tr>
			        <th width="80px">选择</th>
			        <th width="120px">商品图片</th>
			        <th>商品名称</th>
			        <th width="120px">单价</th>
			        <th width="150px">数量</th>
			        <th width="120px">小计</th>
			        <th width="80px">操作</th>
			      </tr>
			    </thead>
			    <tbody>
			      <tr v-for="(item, index) in cartList" :key="item.id">
			        <td>
						<checkbox activeBackgroundColor="#ffa500" iconColor="white" :checked="item.checked" @click="toggleSelect(index)"/>
			        </td>
			        <td>
			          <image :src="item.imgUrl" mode="aspectFill" class="product-img"></image>
			        </td>
			        <td style="font-size: 18px;">{{ item.name }}</td>
			        <td style="font-size: 18px;">¥{{ item.price.toFixed(2) }}</td>
			        <td>
			          <button class="num-btn" @click="decreaseNum(index)" :disabled="item.num <= 1">-</button>
			          <input type="number" class="num-input" v-model.number="item.num" @change="calcTotalPrice" min="0" />
			          <button class="num-btn" @click="increaseNum(index)">+</button>
			        </td>
			        <td style="font-size: 18px;">¥{{ (item.price * item.num).toFixed(2) }}</td>
			        <td class="del-btn" @click="deleteItem(index)">删除</td>
			      </tr>
			    </tbody>
			  </table>
			</view>
			<view class="empty-cart" v-else>
			    🛒 您的购物车还是空的，快去添加商品吧！
			  </view>
			  <view class="cart-footer" v-if="cartList.length > 0">
			    <view class="check-all">
			      <checkbox activeBackgroundColor="#ffa500" iconColor="white" :checked="isCheckAll" @click="handleCheckAll" /> 全选
			    </view>
			    <view class="total-price">
			      合计：¥<span>{{ totalPrice.toFixed(2) }}</span>
			    </view>
			  </view>
		</view>
	</view>
</template>

<script>
	export default {
	  name: 'Cart',
	  data() {
	    return {
	      cartList: [
	        {
	          id: 1,
	          name: '罗技G304鼠标',
	          price: 99.00,
	          num: 1,
	          checked: false,
	          imgUrl: '../../static/shubiao.png'
	        },
	        {
	          id: 2,
	          name: '华为Fit4手表',
	          price: 299.00,
	          num: 1,
	          checked: false,
	          imgUrl: '../../static/shoubiao.png'
	        },
	        {
	          id: 3,
	          name: '256GU盘',
	          price: 89.00,
	          num: 1,
	          checked: false,
	          imgUrl: '../../static/upang.png'
	        }
	      ],
	      isCheckAll: false,
	      totalPrice: 0
	    }
	  },
	  onLoad() { 
	    this.calcTotalPrice();
	    this.checkIsAllChecked();
	  },
	  methods: {
	    increaseNum(index) {
	      this.cartList[index].num++;
	      this.calcTotalPrice();
	    },
	    decreaseNum(index) {
	      if (this.cartList[index].num > 1) {
	        this.cartList[index].num--;
	        this.calcTotalPrice();
	      }
	    },
	    deleteItem(index) {
	      uni.showModal({
	        title: '提示',
	        content: '确定要删除该商品吗？',
	        success: (res) => {
	          if (res.confirm) {
	            this.cartList.splice(index, 1);
	            this.calcTotalPrice();
	            this.checkIsAllChecked();
	          }
	        }
	      });
	    },
		toggleSelect(index) {
			this.cartList[index].checked = !this.cartList[index].checked
			this.calcTotalPrice()
			this.checkIsAllChecked()
		},
	    calcTotalPrice() {
	      let total = 0;
	      this.cartList.forEach(item => {
	        if (item.checked) {
	          total += item.price * item.num;
	        }
	      });
	      this.totalPrice = total;
	      this.checkIsAllChecked();
	    },
	    checkIsAllChecked() {
	      if (this.cartList.length === 0) {
	        this.isCheckAll = false;
	        return;
	      }
	      this.isCheckAll = this.cartList.every(item => item.checked);
	    },
	    handleCheckAll() {
		  this.isCheckAll = !this.isCheckAll;
	      this.cartList.forEach(item => {
	        item.checked = this.isCheckAll;
	      });
	      this.calcTotalPrice();
	    }
	  }
	}
</script>

<style>
	.pages {
		padding: 80rpx 60rpx;
	}
	.card {
		margin-top: 40rpx;
		box-shadow: 3rpx 3rpx 3rpx 3rpx #d9d9d9;
		border-radius: 20rpx;
	}
	.cart-table {
	  width: 100%;
	  border-collapse: collapse;
	  text-align: center;
	}
	.cart-table th {
	  background: #f8f8f8;
	  height: 0px;
	  font-size: 20px;
	  color: #333;
	  padding: 30rpx 20rpx;
	}
	.cart-table td {
	  height: 100px;
	  border-bottom: 1px solid #eee;
	}
	.product-img {
	  width: 80px;
	  height: 80px;
	  border-radius: 4px;
	}
	.num-btn {
	  width: 30px;
	  height: 30px;
	  border: 1px solid #ddd;
	  background: #f8f8f8;
	  cursor: pointer;
	  outline: none;
	  padding: 0;
	  line-height: 30px;
	  display: inline-block;
	  vertical-align: middle;
	  margin: 0 6rpx;
	}
	.num-input {
	  width: 50px;
	  height: 30px;
	  text-align: center;
	  border: 1px solid #ddd;
	  border-left: none;
	  border-right: none;
	  outline: none;
	  padding: 0;
	  display: inline-block;
	  vertical-align: middle;
	}
	.del-btn {
	  color: #ff4400;
	  cursor: pointer;
	  font-size: 18px;
	}
	.del-btn:hover {
	  text-decoration: underline;
	}
	.cart-footer {
	  margin-top: 20px;
	  display: flex;
	  justify-content: space-between;
	  align-items: center;
	  padding: 0 50rpx 30rpx 50rpx;
	}
	.check-all {
	  cursor: pointer;
	  font-size: 18px;
	}
	.total-price {
	  font-size: 24px;
	  font-weight: bold;
	  color: #ff4400;
	}
	.total-price span {
	  font-size: 24px;
	}
	.empty-cart {
	  text-align: center;
	  padding: 100px 0;
	  color: #999;
	  font-size: 24px;
	}
</style>