<template>
  
    <main>
        <header class="bg-dark p-5">
            
            <div class="row d-flex justify-content-between">
                <div class="col">
                    <h1 v-text="sitename"></h1>
                </div>
                
                <div class="col d-flex justify-content-end">
                    <button class="btn btn-dark btn-outline-secondary" v-if="cart.length > 0" v-show="!showProduct" v-on:click='showCheckout'>
                        <span class="fas fa-cart-plus"></span> Cart <span class="badge bg-danger">{{cart.length}}</span>
                    </button>
                    <button class="btn btn-warning" v-on:click="showCheckout" v-show="showProduct"> 
                        Inventory
                    </button>
                </div>
            </div>
                
        </header>
    

        <div class="root">    
            <main>
                <div v-if='!showProduct'>  
                    <div class="title-bar bg-dark px-5">
                        <!-- Bar containing all sort inputs -->
                        <div class="row py-4 d-flex justify-content-between">
                            <div class="row col">
                                <div class="col-md-4">
                                    <select class="btn-secondary btn col-md-12 form-select" name="sortBy" id="select" v-model="sortBy"> Sort
                                        <option disabled value="sort">--Sort By--</option>
                                        <option value="subject">Subject</option>
                                        <option value="location">Location</option>
                                        <option value="price">Price</option>
                                    </select>
                                </div>
                                
                                <div class="col-md-4">
                                    <button v-on:click="ascending = !ascending" class="sort-button btn-secondary btn">
                                        <i v-if="ascending" class="fa fa-sort-up">Ascending</i>
                                        <i v-else class="fa fa-sort-down">Descending</i>
                                    </button>
                                </div>
        
                                <div class="col-md-4 col-sm-12 row d-flex justify-content-end">
                                    <div class="col-md-10 col-sm-11">
                                        <input  class="form-control me-2" type="text" v-model="searchItem" placeholder="Search">
                                    </div>
                                    <div class="col-md-2 col-sm-1">
                                        <button class="btn btn-secondary"><i class="fa fa-search"></i></button>
                                    </div> 

                                </div>

                            </div>
                        </div>
                    </div>  
                    <div class="row">
                        <!-- lessons-->
                        <Lesson  :sortedLessons="sortedLessons" @addToCart="addToCart" />
                    </div>
                    
                </div>
                <!--Checkout-->
                <div v-else class="row">
                   <Checkout :cart="cart" @removeFromCart="removeFromCart" @submit="submit" @showCheckout="showCheckout"/>
                </div>
            </main>
        </div>
  </main>
</template>

<script>
import Lesson from './components/Lesson.vue'
import Checkout from './components/Checkout.vue'

export default {
    components: {
        Lesson,Checkout
    },
    data(){
    return {

                search: '',
                sitename: 'App Lessons',
                lessons: [],
                cart: [],
                showProduct: false,
                order: {
                    firstName: '',
                    lastName: '',
                },
                sort: 'ascending',
                type: '',
                user: {
                    name: '',
                    number: ''
                }
    }
            },
              async created() {
                let course = await fetch("https://cw2-individuals.herokuapp.com/collection/lessons")
                let result = await course.json()
                this.lessons = result
                console.log(result)
            },
            methods: {
                filteredList(){
                    fetch(`https://cw2-individuals.herokuapp.com/collection/lessons/search?key_word=${this.search}`)
                    .then(response => {
                        return response.json()
                    })
                    .then(data => {
                        this.lessons = data
                    })
                },
                addToCart(lesson) {
                    this.lessons.find(item => item.id == lesson.id).Numberofspaces -= 1;
                    this.cart.push({ cartId: (this.cart.length + 1), ...lesson });
                    // console.log('adding to cart', lesson.id)
                },
                removeFromCart(lesson) {
                    if (confirm('You are about to delete this!')) {
                        this.cart = [...this.cart].filter(item => item.cartId != lesson.cartId)
                    }
                },
                showCheckout() {
                    this.showProduct = !this.showProduct;
                },
                submit(name,number) {
                    console.log("I am working")
                    let orders = {
                        checkoutName: name,
                        checkoutPhone: number,
                        cartProduct: this.cart,
                    }
                    let order_details = (JSON.stringify(orders))
                    fetch('https://cw2-individuals.herokuapp.com/collection/lessons', {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json; charset=UTF-8',
                        },
                        mode: "cors",
                        cache: "no-store",
                        body: order_details,
                    })
                    .then(response => response.json())
                    .then(json_response => {
                        console.log(json_response)
                        this.submitForm()
                    })
                    .catch((error) => {
                        console.log(error);
                    });
                },
                phonenumber(number) {
                    if ((number.match(phoneno))) {
                        return true;
                    } 
                },
                submitForm() {
                        alert("Happy Shopping...")
                    },
            },
            computed: {
                total() {
                    return this.cart.length > 0 ? this.cart.map(item => item.price).reduce((acc, cur) => acc + cur) : 0;
                },
                //Everything here is for sorting lessons
                sortedLessons() {
                    switch (this.type) {
                        case 'subject':
                            return this.lessons.sort((a, b) => {
                                switch (this.sort) {
                                    case 'ascending':
                                        return a.subject.toUpperCase() > b.subject.toUpperCase() ? 1 : -1;
                                    default:
                                        return a.subject.toUpperCase() < b.subject.toUpperCase() ? 1 : -1;
                                }
                            })
                        case 'location':
                            return this.lessons.sort((a, b) => {
                                switch (this.sort) {
                                    case 'ascending':
                                        return a.location.toUpperCase() > b.location.toUpperCase() ? 1 : -1;
                                    default:
                                        return a.location.toUpperCase() < b.location.toUpperCase() ? 1 : -1;
                                }
                            })
                        case 'price':
                            return this.lessons.sort((a, b) => {
                                return this.sort == 'ascending' ? a.price - b.price : b.price - a.price;
                            })
                        case 'space':
                            return this.lessons.sort((a, b) => {
                                return this.sort == 'ascending' ? a.availableInventory - b.availableInventory : b.availableInventory - a.availableInventory;
                            })
                        default:
                            return this.lessons.sort((a, b) => {
                                return this.sort == 'ascending' ? a.id - b.id : b.id - a.id;
                            })
                    }
                }
            },
  
}
</script>

<!-- <script>

    import products from "./components/products.vue";
    import checkout from "./components/checkout.vue";
    
    export default {
        name: "App",
        components: {products, checkout},
        data() {
            return {
                sitename: 'After School Lessons',
                products: [],
                cart: [],
                showProduct: true,
                
                searchItem: '',
                ascending: true,
                sortBy: 'subject',
            }
                
        //         search: '',
        //         type: '',
        //         user: {
        //             name: '',
        //             number: ''
        //         }
        },

        methods: {
            addToCart(product) {
                // this.lessons.find(item => item.id == lesson.id).Numberofspaces -= 1;
                // this.cart.push({ id: (this.cart.length + 1), ...lesson });
                // // console.log('adding to cart', lesson.id)

                this.cart.push({

                    _id: product._id,
                    subject: product.subject,
                    location: product.location,
                    price: product.price,
                    Numberofspaces: product.Numberofspaces,
                    image: product.image

                })

                for (let i = 0; i < this.products.length; i++) {

                    if (product.subject === this.products[i].subject) {

                        let newSpace = this.products[i].Numberofspaces - 1
                        this.products[i].Numberofspaces = newSpace
                    }
                }
            },
            removeFromCart(product) {
                if (confirm('You are about to delete this!')) {
                    // this.cart = [...this.cart].filter(item => item.id != lesson.id)
                    for (let i = 0; i < this.cart.length; i++) {

                        if (this.cart[i].subject === product.subject) {

                            this.cart.splice(i, 1);

                            product.Numberofspaces++;

                            if (this.cart.length === 0) {

                                this.showProduct = true;
                            }
                            break;
                        }
                    }

                    for (let i = 0; i < this.products.length; i++) {

                        if (product.subject === this.products[i].subject) {

                            let newSpace = this.products[i].Numberofspaces + 1
                            this.products[i].Numberofspaces = newSpace
                        }
                    }
                }
                
            },
            showCheckout() {
                this.showProduct = this.showProduct ? false : true;
            },
        },
        computed: {
            total() {
                return this.cart.length > 0 ? this.cart.map(product => product.price).reduce((acc, cur) => acc + cur) : 0;
            },

            sortedLessons() {
                let sortedLessons = this.products;
                
                // if (this.search != '' && this.search) {
                //     sortedLessons = sortedLessons.filter((item) => {
                //         return item.subject
                //         .toUpperCase()
                //         .includes(this.search.toUpperCase())
                //     })
                // }

                // Sort by alphabetical order
                sortedLessons = sortedLessons.sort((a, b) => {
                    if (this.sortBy == 'subject') {
                        let fa = a.subject.toLowerCase(), fb = b.subject.toLowerCase()
                    
                        if (fa < fb) {
                        return -1
                        }
                        if (fa > fb) {
                        return 1 
                        }
                        return 0
                        
                    // Sort by price
                    } else if (this.sortBy == 'price') {
                        return a.price - b.price

                    // Sort by location
                    } else if (this.sortBy == 'location') {
                        let fa = a.location.toLowerCase(), fb = b.location.toLowerCase()
                    
                        if (fa < fb) {
                        return -1
                        }
                        if (fa > fb) {
                        return 1 
                        }
                        return 0
                        
                    } 
                })
                
                // Show sorted array in descending or ascending order
                if (!this.ascending) {
                    sortedLessons.reverse()
                }

                return sortedLessons;
            }
        },

        watch: {
            searchItem: async function () {
                if (this.searchItem.length > 0) {
                    let t = this;
                    await fetch('https://cw2-individuals.herokuapp.com/search/lessons/' + this.searchItem).then(
                        function (response) {
                            response.json().then(
                                function (json) {
                                    t.products = json;
                                }
                            )
                        }
                    )

                } else {
                    let t = this;
                    fetch('https://cw2-individuals.herokuapp.com/collection/lessons').then(
                        function (response) {
                            response.json().then(
                                function (json) {
                                    t.products = json
                                }
                            )
                        }
                    )
                }
            }
        },

        // mounted() {
        //     let t = this;
        //     fetch('https://cw2-individuals.herokuapp.com/collection/lessons').then(
        //         function (response) {
        //             response.json().then(
        //                 function (json) {
        //                     t.products = json;
        //                 }
        //             )
        //         }
        //     )
        // },

        async created() {
            let course = await fetch("https://cw2-individuals.herokuapp.com/collection/lessons")
            let result = await course.json()
            this.products = result
        },
    }
</script> -->