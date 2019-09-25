<template>
    <div>
        <nav class="nav navbat-light fixed-top">
            <div class="navbar-text ml-auto d-flex align-items-center">
                <button class="btn btn-sm btn-outline-success bg-white mr-2 manipulateProgressBar"
                @click="sliderStatus = !sliderStatus">$</button>
                <div class="mr-2" v-if="cart.length>0">
                    <button class="btn btn-success btn-sm dropdown-toggle"
                            type="button" data-toggle="dropdown"
                            aria-haspopup="true" aria-expanded="false"
                            id="cartDropdown"
                            @click="dropdownStatus = !dropdownStatus">
                        <span class="badge badge-pill badge-light mr-2">{{ cartQty }}</span>
                        {{ cartTotal | currency }}
                    </button>

                    <div class="dropdown-menu dropdown-menu-right mr-2"
                         v-if="dropdownStatus && cart.length>0"
                         :class="manipulateDropdown"
                         aria-labelledby="cartDropdown">
                        <div v-for="(item, index) in cart" :key="index">
                            <div class="dropdown-item-text text-nowrap text-right">
                                <span class="badge badge-pill badge-warning align-text-top mr-1">{{ item.qty }}</span>
                                {{ item.product.name }}
                                <b>{{ (item.qty * item.product.price) | currency }}</b>
                                <a class="badge badge-danger text-white ml-2" href="#" @click="deleteItem(index)">-</a>
                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </nav>

        <h1 class="mb-5">My Shop</h1>
        <transition name="fade">
            <div v-if="sliderStatus" class="mb-5">
                <div class="align-items-center" :class="sliderState">
                    <label class="font-weight-bold" for="forMax">max</label>
                    <input id="forMax" class="form-control mx-2" :style="{'width': inputWidth + 'px', textAlign: 'center'}" v-model="max" name="maximumValue">
                    <input type="range" class="custom-range" name="valueRange" min="0" max="200" v-model="max">
                </div>
            </div>
        </transition>

        <div class="row d-flex align-items-center mb-4"
             v-for="(item, index) in productsList"
             v-if="item.price<=Number(max)"
             :key="index">
            <div class="col-1 m-auto p-0">
                <button class="btn btn-primary"
                v-on:click="addItem(item)">+</button>
            </div>
            <div class="col-sm-4 img-box d-flex align-items-center ml-4">
                <img class="img-fluid" :src="item.image" :alt="item.name">
            </div>
            <div class="col ml-4">
                <p class="product-title">{{ item.name }}</p>
                <p class="product-desc">{{ item.description }}</p>
                <p class="product-price float-right">
                    <Price :value="Number(item.price)"
                           :prefix="'€'"
                           :precision="2"
                           :conversion=".87">;
                    </Price>
                </p>
            </div>
        </div>

<!--        <div>-->
<!--            <p>-->
<!--                <strong>Bound: </strong>-->
<!--                <span>{{ text }}</span>-->
<!--            </p>-->
<!--            <p>-->
<!--                <strong>Parsed: </strong>-->
<!--                <span v-html="text + '-&#45;&#45;'"></span>-->
<!--            </p>-->
<!--            <p>-->
<!--                <strong>Unchangeable: </strong>-->
<!--                <span v-once>{{ text}}</span>-->
<!--            </p>-->
<!--            <h5>Text</h5>-->
<!--            <textarea class="form-control mb-4" name="" id="" cols="" rows="3" v-model="text"></textarea>-->

<!--            <h3>Slugetize</h3>-->
<!--            <input name="" class="form-control" type="text" v-model="slugText">-->
<!--            <div class="font-weight-bold text-danger mb-4">{{ slugetize }}</div>-->
<!--        </div>-->

    </div>
</template>

<script>
    import Price from './Price';

    export default {
        name: "Products",
        data() {
            return {
                products: null,
                max: 99,
                cart: [],
                text: 'Start typing...',
                slugText: 'This is a text',
                date: {},
                inputWidth: 60,
                sliderStatus: false,
                dropdownStatus: false
            }
        },
        components: {
          Price
        },
        filters: {
          currency: function(value) {
              return '$' + Number.parseFloat(value).toFixed(2);
          }
        },
        computed: {
            productsList: function () {
                return this.products
            },
            sliderState: function() {
                return (
                    this.sliderStatus? 'd-flex': 'd-none'
                )
            },
            manipulateDropdown: function () {
                return (
                    this.dropdownStatus? 'd-block': 'd-none'
                )
            },
            slugetize: function() {
                return (
                    this.slugText
                        .toLowerCase()
                        .replace(/[^\w ]+ /g, + '')
                        .replace(/ +/g, '-') + '-' + this.now()
                )
            },
            cartTotal: function() {
                let sum = 0;
                this.cart.forEach((productItem) => {
                    sum = sum + productItem.product.price * productItem.qty;
                });
                return sum;
            },
            cartQty: function() {
                let qty = 0;
                for(let key in this.cart) {
                    qty = qty + this.cart[key].qty;
                }
                return qty;
            }
        },
        methods: {
            addItem: function(product) {
                var whichProduct;
                var existing = this.cart.filter(function(item, index) {
                    if(item.product.id == Number(product.id)) {
                        whichProduct = index;
                        return true;
                    }else {
                        return false;
                    }
                });

                if(existing.length) {
                    this.cart[whichProduct].qty++;
                }else {
                    this.cart.push({product: product, qty: 1});
                }
            },
            deleteItem: function(id) {
                if(this.cart[id].qty>1) {
                    this.cart[id].qty--;
                }else {
                    this.cart.splice(id, 1);
                }
            },
            now: function(date) {
                date = new Date();
                return (
                    String(date.getHours()) +
                    String(date.getMinutes()) +
                    String(date.getSeconds())
                )
            }
        },
        mounted: function() {
            fetch('https://hplussport.com/api/products/order/price')
                .then(response => response.json())
                .then( data => {
                    this.products = data;
                })
            }
    }
</script>

<style lang="scss" scoped>
    $title-color: green;
    $desc-color: darkGreen;
    %align-left {
        text-align: left;
    }
    .img-box {
        overflow: hidden;
        max-height: 200px;
        box-shadow: 0 0 5px 1px grey;
        img {
            width: 100%;
        }
    };
    .product-title {
        font-size: 30px;
        color:  $title-color;
        font-weight: 600;
        @extend %align-left;
    };
    .product-price {
        color: $title-color;
        font-weight: 900;
        font-size: 20px;
    }
    .product-desc {
        font-size: 16px;
        color: $desc-color;
        @extend %align-left;
    }

    .fade-enter,
    .fade-leave-to {
        opacity: 0;
    }

    .fade-enter-active,
    .fade-leave-active {
        transition: all 1s ease-in-out;
    }

    .manipulateProgressBar:hover,
    .manipulateProgressBar:active {
        color: #28a745!important;
    }
</style>