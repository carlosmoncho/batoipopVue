<template>
<div>
    
<div class="container-fluid px-xl-5">
    <div id="contenedor_carga" v-if="charge" >
    <div id="carga"></div>
  </div>

    <div v-else>
         <div class="row px-xl-5" >
            <div class="col-lg-5 mb-30" v-scrollanimation>
                <div id="product-carousel" class="carousel slide" data-ride="carousel">
                    <div class="carousel-inner bg-light">
                        <div class="carousel-item active">
                            <img class="w-100 h-100" src="../assets/img/product-1.jpg" alt="Image">
                        </div>
                        <div class="carousel-item">
                            <img class="w-100 h-100" src="../assets/img/product-2.jpg" alt="Image">
                        </div>
                        <div class="carousel-item">
                            <img class="w-100 h-100" src="../assets/img/product-3.jpg" alt="Image">
                        </div>
                        <div class="carousel-item">
                            <img class="w-100 h-100" src="../assets/img/product-4.jpg" alt="Image">
                        </div>
                    </div>
                    <a class="carousel-control-prev" href="#product-carousel" data-slide="prev">
                        <i class="fa fa-2x fa-angle-left text-dark"></i>
                    </a>
                    <a class="carousel-control-next" href="#product-carousel" data-slide="next">
                        <i class="fa fa-2x fa-angle-right text-dark"></i>
                    </a>
                </div>
            </div>

            <div class="col-lg-7 h-auto mb-30">
                <div class="h-100 bg-light p-30">
                    <h3>{{producto.nombre}}</h3>
                    <small>Valoracion del Usuario</small>
                    <div class="d-flex mb-3" style="justify-content:center;">
                        <div class="text-primary mr-2">
                            <small class="fas fa-star" v-for="valoration in valoracionUser.average" :key="valoration.id"></small>
                            <small class="bi bi-star-half" v-if="!entero"></small>
                        </div>
                        <small class="pt-1">({{valoracionUser.count}} Reviews)</small>
                    </div>
                    <h3 class="font-weight-semi-bold mb-4">{{producto.precio+' €'}}</h3>
                    <p class="mb-4">
                        {{producto.descripcion}}
                    </p>
                    <div class="d-flex align-items-center mb-4 pt-2" style="justify-content:center;" v-if="userlog">
                        <button class="btn btn-primary px-3" @click="goBuy"><i class="fa fa-shopping-cart mr-1"></i> Comprar</button>
                    </div>
                    <p v-else>Inicia sesión para poder comprar</p>
                    <div v-if="!denunciado && userlog" class="d-flex align-items-center mb-4 pt-2" style="justify-content:center;" >
                        <button class="btn btn-primary px-3" @click="denunciar"><i class="bi bi-exclamation-triangle"></i> Denunciar</button>
                    </div>
                    <div class="d-flex pt-2"  style="justify-content:center;">
                        <strong class="text-dark mr-2">Vendedor:</strong>
                        <div class="d-inline-flex">
                            <a @click="goDetails"  class=" px-2">
                                {{user.name}}
                            </a>
                            
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="row px-xl-5">
            <div class="col">
                <div class="bg-light p-30">
                    <div class="nav nav-tabs mb-4">
                        <a class="nav-item nav-link text-dark" v-bind:class="{active: lista}" @click="changeLista" id="tab-pane-1" data-toggle="tab">Descripción</a>
                        <a class="nav-item nav-link text-dark" v-bind:class="{active: !lista}" @click="changeLista" id="tab-pane-2" data-toggle="tab">Comentarios</a>
                    </div>
                    <div class="tab-content">
                        <div v-if="lista">
                            <h4 class="mb-3">Descripción del Producto </h4>
                            <p>{{producto.descripcion}}</p>
                        </div>
                        <div v-else class="active" >
                            <div class="row">
                                <div class="col-md-6">
                                    <h4 class="mb-4">Mensaje/s para {{producto.nombre}}</h4>
                                    <comentario v-for="comentario in mensajes" :key="comentario.id" :message="comentario"></comentario>
                                </div>
                                <div class="col-md-6" v-if="userlog">
                                    <h4 class="mb-4">Deja un Comentario</h4>
                                    <form @submit.prevent="dejarComentario">
                                        <div class="form-group">
                                            <label for="message">Tu comentario *</label>
                                            <textarea id="message" v-model="message" cols="30" rows="5" class="form-control"></textarea>
                                        </div>
                                        <div class="form-group mb-0">
                                            <input type="submit" value="Deja tu comentario" class="btn btn-primary px-3">
                                        </div>
                                    </form>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
       
    </div>
</div>
    
            
</template>

<script>
import Api from '../Api'
import Comentario from './Comentario.vue'
export default {
    data(){
        return{
 
            user: {},
            valoracionUser : {},
            producto: {},
            entero: true,
            charge: true,
            lista: true,
            message:'',
            mensajes:[],
            denunciado:false,
            userlog: false,
        }
    },
    components:{      Comentario     },
    name:'ProductDetails',
    props:['producto_id'],
    methods:{
        async denunciar(){
            let denuncia = {
                    user_id : localStorage.getItem('idUser'),
                    product_id: this.producto.id
                }
            try {
                if(!this.denunciado){
                    let response = await Api.denunciasA.create(denuncia)
                    if(response.status == 201){
                        this.denunciado = true
                    }
                }
            } catch (error) {
                console.error(error)
            }
        },
        async getDenuncia(){
            let denuncia = {
                    userId : localStorage.getItem('idUser') ,
                    productId: this.producto.id
                }
                try {
                     let response = await Api.denunciasA.existe(denuncia)
                this.denunciado = response.data
                } catch (error) {
                    console.error(error)
                }
               

        },
        changeLista(){
            if(this.lista){
                this.lista = false
                document.getElementById("tab-pane-2").className += "active";
            }else{
                this.lista = true
            }
        },
        goBuy(){
            this.$router.push('/buy/'+this.producto_id)
        },
        goDetails(){
            this.$router.push('/vendorDetails/'+this.user.id)
        },
        async getMessages(){
            try {
                let response = await Api.mensajes.getAllByProduct(this.producto_id)
                this.mensajes = response.data
            } catch (error) {
                console.error(error)
            }
        },
        async dejarComentario(){
            let newMessage = {
                    emisor_id: localStorage.getItem('idUser'),
                    receptor_id: this.user.id,
                    product_id: this.producto.id,
                    contenido: this.message
                }
            try {
                let response = await Api.mensajes.create(newMessage)
                this.mensajes.push(response.data)
                this.message = ''
            } catch (error) {
                console.error(error)
            }
        }
    },
    computed:{
        isUserLoged(){
            return !!this.$store.state.token
        }
    },
    watch:{
        isUserLoged(newValue){
            this.userlog = newValue
        }
    },
    async mounted(){
        try {
           let response = await Api.products.detalles(this.$route.params.id)
            this.producto = response.data.products[0]
            this.user = response.data.user[0]
            this.valoracionUser.average = response.data.valoracion
            this.valoracionUser.count = response.data.conteo
            if( response.data.valoracion % 1 != 0 ){
                    this.entero = false
                    let num = Math.floor( response.data.valoracion)
                    this.valoracionUser.average = num
                }
                
            this.getDenuncia()
        } catch (error) {
            console.error(error)
        }
        await this.getMessages()
        this.charge = false
        if(this.isUserLoged){
            this.userlog = true;
        }
    },
    
}
</script>


<style scoped >


.before-enter{
  opacity: 0;
  transform: translateX(-150px);
  transition: all 1s cubic-bezier(.21,.69,.53,.42);

}

.enter{
    opacity: 1;
    transform:  translateX(0px);

}


</style>