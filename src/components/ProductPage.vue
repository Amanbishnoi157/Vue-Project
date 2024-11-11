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
            <option v-for="item in shipType" :key="item.id" >
                {{ item }}
            </option>
        </select>
    </div>

    <div>
        <button @click="couponMethod">Apply Coupon</button>

        <div v-if="showCouponInput">
            <input type="text" v-model="couponCode" placeholder="Enter coupon code" />
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
import {
    ref,
    computed,
    watch
} from 'vue';

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

        const shippingCosts = {
            standard: 0,
            express: 1000,
            sameday: 2000,
        };

        const shipType = ref([
            "Satandard",
            "express",
            "sameday"
        ])

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

        const totalPrice = computed(() => {
            let total = 0;

            for (let product in counter.value) {
                total += counter.value[product] * prices[product];
            }

            total += updatedShipCost.value[shippingMethod.value];

            if (couponCorrect.value) {
                total *= 0.8;
            }

            return total;
        });

        const updatedShipCost = computed(() => {
            const newShipCost = {
                ...shippingCosts
            };

            if (totalPrice.value >= 15000) {
                newShipCost.express = 2000;
                newShipCost.sameday = 3000;
            } else {
                newShipCost.express = 1000;
                newShipCost.sameday = 2000;
            }
            return newShipCost;
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
            updatedShipCost,
            shipType
        };
    },
};
</script>

<style scoped>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: cursive;
}

body {
    font-family: 'Arial', sans-serif;
    background-color: #f4f4f9;
    color: #333;
    line-height: 1.6;
    padding: 20px;
}

h3 {
    margin: 10px 0;
    font-size: 1.5rem;
    color: #555;
}

/* Card Styling */
.card {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background-color: #fff;
    border-radius: 8px;
    padding: 16px;
    margin-bottom: 20px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card:hover {
    transform: translateY(-5px);
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
}

.card img {
    width: 150px;
    height: auto;
    border-radius: 8px;
    object-fit: cover;
}

.card .container {
    flex-grow: 1;
    margin-left: 20px;
}

.card .container h3 {
    font-size: 1.2rem;
    margin-bottom: 8px;
}

.card .container div {
    display: flex;
    align-items: center;
}

.card .container button {
    background-color: #007bff;
    color: white;
    border: none;
    padding: 8px 16px;
    font-size: 1.2rem;
    cursor: pointer;
    border-radius: 4px;
    margin: 0 10px;
    transition: background-color 0.3s;
}

.card .container button:hover {
    background-color: #0056b3;
}

.card .container h4 {
    font-size: 1.4rem;
    margin: 0;
}

/* Shipping and Coupon Section */
div>label {
    font-size: 1rem;
    color: #333;
    margin-bottom: 10px;
    display: block;
}

select {
    font-size: 1rem;
    padding: 8px;
    margin-top: 10px;
    width: 100%;
    border: 1px solid #ddd;
    border-radius: 4px;
}

button {
    background-color: #28a745;
    color: white;
    padding: 10px 20px;
    border: none;
    font-size: 1rem;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s;
    margin-top: 10px;
}

button:hover {
    background-color: #218838;
}

button:focus {
    outline: none;
}

/* Coupon Input */
input[type="text"] {
    padding: 8px;
    width: 100%;
    font-size: 1rem;
    margin-top: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
}

input[type="text"]:focus {
    border-color: #007bff;
}

/* Total Price Section */
div h3 {
    font-size: 1.5rem;
    font-weight: bold;
    color: #333;
    margin-top: 20px;
}

div h1 {
    margin-bottom: 20px;
}

input[readonly] {
    padding: 12px;
    font-size: 1.2rem;
    width: 100%;
    border: 1px solid #ddd;
    border-radius: 4px;
    margin-top: 10px;
    background-color: #f7f7f7;
    text-align: center;
}

/* Responsive Layout */
@media (max-width: 768px) {
    .card {
        flex-direction: column;
        align-items: center;
        text-align: center;
    }

    .card img {
        width: 100px;
    }

    .card .container {
        text-align: center;
        margin-left: 0;
    }

    .card .container div {
        flex-direction: column;
    }

    select {
        width: auto;
    }

}
</style>

