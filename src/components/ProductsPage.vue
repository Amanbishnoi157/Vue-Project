<template>
  <div>
    <h1>Shipping Center</h1>
    
    <div class="card">
      <img src="@/assets/images/jacket.jpg" alt="Jacket" style="width: 20%" />
      <div class="container">
        <h3>Jacket ₹5000</h3>
        <div>
          <button @click="increment('jacket')">+</button>
          <h4>{{ counter.jacket }}</h4>
          <button @click="decrement('jacket')">-</button>
        </div>
      </div>
    </div>

    <div class="card">
      <img src="@/assets/images/jeans.jpg" alt="Jeans" style="width: 20%" />
      <div class="container">
        <h3>Jeans ₹4000</h3>
        <div>
          <button @click="increment('jeans')">+</button>
          <h4>{{ counter.jeans }}</h4>
          <button @click="decrement('jeans')">-</button>
        </div>
      </div>
    </div>

    <div class="card">
      <img src="@/assets/images/tshirt.jpg" alt="T-Shirt" style="width: 20%" />
      <div class="container">
        <h3>T-Shirt ₹2500</h3>
        <div>
          <button @click="increment('tshirt')">+</button>
          <h4>{{ counter.tshirt }}</h4>
          <button @click="decrement('tshirt')">-</button>
        </div>
      </div>
    </div>

    <div class="card">
      <img src="@/assets/images/shoes.jpg" alt="Shoes" style="width: 20%" />
      <div class="container">
        <h3>Shoes ₹6000</h3>
        <div>
          <button @click="increment('shoes')">+</button>
          <h4>{{ counter.shoes }}</h4>
          <button @click="decrement('shoes')">-</button>
        </div>
      </div>
    </div>

    <div class="card">
      <img src="@/assets/images/goggles.jpg" alt="Goggles" style="width: 20%" />
      <div class="container">
        <h3>Goggles ₹3000</h3>
        <div>
          <button @click="increment('goggles')">+</button>
          <h4>{{ counter.goggles }}</h4>
          <button @click="decrement('goggles')">-</button>
        </div>
      </div>
    </div>

    <div>
      <label for="shipping-method">Select Shipping Method:</label>
      <select id="shipping-method" v-model="shippingMethod">
        <option value="standard" >Standard Shipping </option>
        <option value="express" >Express Shipping </option>
        <option value="sameday">Same Day Delivery </option>
      </select>
    </div>

    <div>
      <button @click="couponMethod">Apply Coupon</button>

      <div v-if="showCouponInput">
        <input
          type="text"
          v-model="couponCode"
          placeholder="Enter coupon code"
        />
        <button @click="checkCoupon">Use Coupon</button>
        <p v-if="couponCorrect">Coupon applied! 20% discount.</p>
        <p v-if="couponError" style="color: red;">Invalid coupon code</p>
      </div>
    </div>

    <div>
      <h3>Total Price: ₹{{ totalPrice }}</h3>
      <input type="text" :value="totalPrice" readonly placeholder="Total Price" />
    </div>
  </div>
  
</template>
<script>
import { ref, computed, watch } from 'vue';

export default {
  name: 'ProductPage',
  setup() {
   
    const prices = {
      jacket: 5000,
      jeans: 4000,
      tshirt: 2500,
      shoes: 6000,
      goggles: 3000,
    };

    const counter = ref({
      jacket: 0,
      jeans: 0,
      tshirt: 0,
      shoes: 0,
      goggles: 0,
    });

    const shippingMethod = ref('standard'); 

    // Base shipping costs (standard and same-day do not change)
    const baseShippingCosts = {
      standard: 0,
      express: 2000,  // Default value before totalPrice calculation
      sameday: 2000,
    };

    const shipType = [
        "Standard",
        "Express",
        "Same-day"
    ];

    const givenCode = 'ABCDE'; 
    const couponCode = ref('');
    const showCouponInput = ref(false);
    const couponCorrect = ref(false);
    const couponError = ref(false);

    function increment(product) {
      counter.value[product]++;
    }

    function decrement(product) {
      if (counter.value[product] > 0) {
        counter.value[product]--;
      }
    }

    function couponMethod() {
      showCouponInput.value = !showCouponInput.value;
      couponCorrect.value = false; 
      couponError.value = false;
    }

    function checkCoupon() {
      if (couponCode.value === givenCode) {
        couponCorrect.value = true;
        couponError.value = false;
      } else {
        couponCorrect.value = false;
        couponError.value = true;
      }
    }

    // Computed property for total price calculation
    const totalPrice = computed(() => {
      let total = 0;

      for (let product in counter.value) {
        total += counter.value[product] * prices[product];
      }

      total += shippingCosts.value[shippingMethod.value];

      if (couponCorrect.value) {
        total *= 0.8;  // Apply 20% discount for correct coupon
      }

      return total;
    });

    // Shipping costs that depend on the total price
    const shippingCosts = computed(() => {
      const updatedShippingCosts = { ...baseShippingCosts };

      // If total price is more than ₹15,000, change express shipping cost
      if (totalPrice.value > 15000) {
        updatedShippingCosts.express = 1000;
      } else {
        updatedShippingCosts.express = 2000;
      }

      return updatedShippingCosts;
    });

    watch(totalPrice, (newTotal) => {
      localStorage.setItem('totalPrice', newTotal);
    });

    const savedTotal = localStorage.getItem('totalPrice');
    if (savedTotal) {
      localStorage.setItem('totalPrice', savedTotal);
    }

    return {
      counter,
      shippingMethod,
      totalPrice,
      increment,
      decrement,
      couponMethod,
      checkCoupon,
      couponCode,
      showCouponInput,
      couponCorrect,
      couponError,
      shipType,
      shippingCosts
    };
  },
};
</script>
