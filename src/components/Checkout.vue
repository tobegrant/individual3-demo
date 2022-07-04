<template>
    <div class="container-fluid p-5">
        <!-- calculation-->
        <!-- <div class="title-bar">
            <button class="btn btn-dark" v-on:click="showCheckout"><span class="fas fa-arrow-left"></span> Go Back </button>
            <h4>Total Amount: {{total}}</h4>
        </div> -->
        <div class="py-4 d-flex justify-content-between">
            <h4>Total Amount: {{total}}</h4>
        </div>
        <!-- cart-->
        <h4 class="card-title text-dark">Your cart</h4>

        <div class="col-md-3 py-5" v-for="(item, index) in cart" :key="`cart--${index}`">
            <div class="card bg-light text-dark rounded">
                <img v-bind:src="item.image" class="card-img-top" width="100dp" height="150dp">
                <div class="card-body">
                    <h2 class="card-title my-1">{{item.subject}}</h2>
                    <h4 class="card-text">{{item.location}}</h4>
                    <div class="card-text mt-3">Price: ${{item.price}}</div>
                    <div class="my-2">
                        <button class="btn btn-danger" v-on:click="removeFromCart(item)">Remove</button>
                    </div>
                </div>
            </div>
        </div>
        
        <div v-if="!cart.length > 0" class="cart__item"
            style="background-color: transparent; padding: 20px;">
            <div style="text-align: center; color: grey; font-size: 20px; font-weight: bold;">
            List Empty</div>
        </div>
    

        <div class="container my-3">
            <div class="row col-12 d-flex justify-content-end">
                <div class="col text-dark">
                    <h3>Student Details</h3>
                </div>
                <div class="col">
                    <input v-model="user.name" type="text" class="form-control" placeholder="Enter Name" required>
                </div>
                <div class="col">
                    <input v-model="user.number" type="number" class="form-control" placeholder="Enter Phone Number" required>
                </div>
                <div class="col-md-2">
                    <input type="submit" class="btn btn-primary" @click="submit" v-on:click="submit(user.name,user.phone)" value="Checkout">
                </div>
            </div>
        </div>

    </div>
    
</template>

<script>
    export default {
        data(){
            return{
                user:{
                    name:'',
                    number:''
                }
            }
        },
        props:['cart'],
        methods:{
            removeFromCart(lesson){
                this.$emit('removeFromCart',lesson)
            },
            submit(name,phone){
                this.$emit('submit',name,phone)
            },
            showCheckout(){
                this.$emit('showCheckout');
            }
        }
    }
</script>