<template>
    <!-- lessons-->
    <div class="col-md-3 py-2" v-for="(lessons, index) in sortedLessons" :key="`lesson--${index}`">
        <div class=" bg-light text-dark rounded">
            <img v-bind:src="lessons.image" class="card-img-top" width="200dp" height="250dp">
            <div class="card-body">
                <h2 class="card-title my-1">{{lessons.subject}}</h2>
                <h4 class="card-text">{{lessons.location}}</h4>
                <div class="card-text my-1">Price: ${{lessons.price}}</div>
                <div class="card-text my-1">Number of Spaces: {{ lessons.Numberofspaces}}</div>
                <div class="my-2">
                    <button class="btn btn-warning" v-on:click="addToCart(lessons)" :disabled="!lessons.Numberofspaces">Add To Cart</button>
                    <span v-if="lessons.Numberofspaces">{{'Left '+ lessons.Numberofspaces}}</span>
                    <span v-else>This lesson is fully booked!</span>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
    export default {
        props:['sortedLessons'],
        data() {
            return {};
        },
        methods:{
            addToCart(lesson){
                this.$emit('addToCart',lesson)
            }
        }
    }
</script>