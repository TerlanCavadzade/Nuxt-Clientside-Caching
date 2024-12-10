<script setup lang="ts">
import { type Products } from '~/types/Products';


const {data,pending} = useAsyncData<Products>("products",()=>{

    
    
    return $fetch("https://dummyjson.com/products")
},{lazy:true})
</script>

<template>

    <h2 v-if="pending">Loading...</h2>

    <ul>
        <li v-for="product of data?.products" :key="product.id">
            <NuxtLink :prefetch="false" :to="{ name: 'products-id', params:{id:product.id}}">{{ product.title }}</NuxtLink>
        </li>
    </ul>
</template>