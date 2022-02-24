<template>
            <div class="col-lg-12">
                <h5 class="section-title position-relative text-uppercase mb-3"><span class="bg-secondary pr-3">Iniciar Sesión</span></h5>
                <div class=" p-30 mb-5">
                    <div class="row rowCenter">
                        <ValidationObserver v-slot="{ handleSubmit }">
                        <form class="bg-light" @submit.prevent="handleSubmit(submit)">
                        <div class="col-md-12 form-group">
                                 <validation-provider rules="required" v-slot="{ errors }" name="email">
                            <label>E-mail</label>
                            <input class="form-control" v-model="user.email" type="text" placeholder="example@email.com">
                            <span class="text-danger">{{ errors[0] }}</span>
                            </validation-provider>
                        </div>
                        <div class="col-md-12 form-group">
                             <validation-provider rules="required" v-slot="{ errors }" name="password">
                            <label>Contraseña</label>
                            <input class="form-control" v-model="user.password" type="password" placeholder="password">
                            <span class="text-danger">{{ errors[0] }}</span>
                            </validation-provider>
                        </div>
                        <div class="col-md-12 form-group">
                            <button type="submit" class="btn btn-default btn-primary">Iniciar Sesión</button>
                        </div>
                        </form>
                        </ValidationObserver>
                        
                    </div>
                </div>
            </div>
</template>

<script>
import store from '../store'
import { ValidationObserver } from 'vee-validate';
import { ValidationProvider } from 'vee-validate';
import { extend } from 'vee-validate';
import { required } from 'vee-validate/dist/rules';



extend('required',{ 
  ...required,
  message: 'El campo es requerido'});

export default {
     components:{ValidationProvider, ValidationObserver},
    data(){
        return{
            user:{}
        }
    },
    methods: {
    async submit() {
        try {
            await store.dispatch('login', this.user)
        } catch (error) {
            console.error('error')
        }
    },

  },

}
</script>