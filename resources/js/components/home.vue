<style> body {     background-color:cyan ; } </style>
<template>
<v-app>
     <v-row
    align="top"
    justify="space-around"
  >
    <v-btn @click="redirect('product')">
      จัดการดอกไม้
    </v-btn>
  </v-row>
</v-app>
</template>

<script>
    export default {
        data(){
            return {
                products:[],
                product: null,
            }
        },
        mounted() {
            this.initialize()
            console.log('initialized')
        },
        methods:{
            redirect(url){
                window.location.href = url
            },

            initialize(){
                axios.get("api/product")
                .then(response=>{
                    if(response.data.success == true){
                    this.products = response.data.products
                    }
                })
                .catch(error =>{
                    console.log("error")
                })
            },
        }
    }
</script>
