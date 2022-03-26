<template>
<v-app style="background-color:cyan">
     <v-container fluid>
      <v-row dense>
        <v-col
          v-for="product in products"
          :key="product.id"
          :cols="3"
        >
          <v-card>
            <v-img
            :src="product.image"
              class="white--text align-end"
              gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
              height="200px"
            >
              <v-card-title v-text="product.name"></v-card-title>
            </v-img>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
</v-app>
</template>

<script>

    export default {
        data(){
            return {
                products:[],
                product: null,
                reveal: false,
            }
            
        },
        mounted() {
            this.initialize()
            console.log('initialized')
        },
        methods:{
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
<style>
.v-card--reveal {
  bottom: 0;
  opacity: 1 !important;
  position: absolute;
  width: 100%;
}
</style>