<template>
<v-app>
   <v-data-table
    :headers="headers"
    :items="products"
    sort-by="calories"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>รายการดอกไม้</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="green"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              เพิ่มดอกไม้
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                  >
                    <v-text-field
                      v-model="editedItem.name"
                      label="ชื่อดอกไม้"
                    ></v-text-field>
                  </v-col>
                  <v-col
                  >
                    <v-text-field
                      v-model="editedItem.price"
                      label="ราคา"
                    ></v-text-field>
                  </v-col>
                   <v-col
                  >
                    <v-text-field
                      v-model="editedItem.description"
                      label="รายละเอียด"
                    ></v-text-field>
                  </v-col>
                  <v-col
                  >
                    <v-text-field
                      v-model="editedItem.image"
                      label="รูปภาพ"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">ต้องการที่จะลบข้อมูล จริง หรือ ไม่?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="primary"
        @click="initialize"
      >
        Reset
      </v-btn>
    </template>
  </v-data-table>
</v-app>
</template>

<script>
    export default {
        data(){
            return {
      dialog: false,
      dialogDelete: false,
      headers: [
        {
          text: 'ชื่อดอกไม้',
          align: 'start',
          sortable: false,
          value: 'name',
        },
        { text: 'ราคา', value: 'price' },
        { text: 'รายละเอียด', value: 'description' },
        { text: 'จัดการ', value: 'actions',sortable: false },
      ],
      products: [],
      editedIndex: -1,
      editedItem: {
        name: '',
        price: 0,
        description: '',
        image: '',

      },
      defaultItem: {
        name: '',
        price: 0,
        description: '',
        image: '',

      },
            }
        },

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Flower' : 'Edit Flower'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
      dialogDelete (val) {
        val || this.closeDelete()
      },
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
                });
            },
       editItem (item) {
        this.editedIndex = this.products.indexOf(item)
        this.editedItem = Object.assign({}, item)
        //API Update
        this.dialog = true
      },

      deleteItem (item) {
        this.editedIndex = this.products.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },

      deleteItemConfirm () {
        this.products.splice(this.editedIndex, 1)
        //API Delete
                axios.delete('api/product/destroy/'+this.editedItem.id, {
                 })
                .then(response=>{
                    this.products.splice(this.editedIndex, 1)
                })
                .catch(error =>{
                    console.log("error")
                });
        this.closeDelete()
      },

      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      save () {
        if (this.editedIndex > -1) {
          //Update
          Object.assign(this.products[this.editedIndex], this.editedItem)
        } else {
          // Add new
                 axios.post('api/product/store',{
                   name: this.editedItem.name,
                   description: this.editedItem.description,
                   price: this.editedItem.price,
                   image: this.editedItem.image,
                 })
                .then(response=>{
                    this.products.push(this.editedItem)
                })
                .catch(error =>{
                    console.log("error")
                });
        }
        this.close()
      },
        }
    }
</script>
