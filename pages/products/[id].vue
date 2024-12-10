<script setup lang="ts">
import { type Product } from '~/types/Products';
    const nuxtApp = useNuxtApp()
    const {params:{id}} = useRoute()

    const {data , pending} = useAsyncData<Product>(`product-${id}`,()=>{
        console.log(id);
        
        return $fetch(`https://dummyjson.com/products/${id}`)
    },{
        transform:(data)=>{
            return {
                ...data,
                fetchedAt:new Date()
            }
        },
        getCachedData:(key)=>{
            const existing = nuxtApp.static.data[key] || nuxtApp.payload.data[key]
            if(!existing){
                return
            }
            
            const expireTime = new Date(existing.fetchedAt)
            expireTime.setTime(expireTime.getTime() + 15 * 1000);

            const isExpired = expireTime.getTime() < Date.now();

            if(isExpired){
                return;
            }

            return existing
        },
        lazy:true
    }) 

</script>

<template>

<NuxtLink :to="{name:'index'}">Home</NuxtLink>

<h3 v-if="pending">Loading...</h3>

<div v-else class="product-container">
 <div class="img-container">
    <img :src="data?.thumbnail" alt="">
 </div>
 <div>
    <h2>{{ data?.title }}</h2>
    <div class="additional-info">
        <p>{{ data?.description }}</p>
        <p class="price">${{ data?.price }}</p>
    </div>
 </div>
</div>

</template>


<style scoped>
a{
    color:inherit;
    font-size: 1.5rem;
    text-decoration: none;
    display: flex;
    align-items: center;
}

a::before{
    content:"\2190";
    font-size: 1rem;
}

.product-container{
    display: grid;
    grid-template-rows:1fr 1fr ;

    width: min(98%,37.5rem);
    margin-inline: auto;
}


.product-container > .img-container{
    text-align: center;
}

.product-container > .img-container>img{
    max-width: 300px;
    width: 100%;
    height: 100%;
}

.price{
    text-align: end;
}
</style>