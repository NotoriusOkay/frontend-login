<template>
    <v-card class="cardLogin">
        <v-card-title class="title">Me parecio ver un lindo gatito </v-card-title>
        <v-card-text>
            <v-row justify="center" align="center">
                <v-col cols="4" align-self="center">
                    <img src="../../assets/img/piolin.jpg" alt="" class="imgLogin">
                </v-col>
                <v-col cols="8" align-self="center">
            
            <v-form ref="formlogin">
                <v-text-field label="Correo Electrónico" placeholder="Correo electronico" v-model="correoElectronico" :rules="validarCorreo"/>
                <v-text-field label="Password" placeholder="Password" v-model="password" :rules="validarPassword"/>
            </v-form>
                 </v-col>
            </v-row>
        </v-card-text>
        
        <v-card-actions>
            <v-btn class="btnLogin" rounded block @click='loginBackend'>
                Login
            </v-btn>
        </v-card-actions>
    </v-card>
</template>

<script>
import { async } from 'q'

    export default{
        data(){
            return{
                correoElectronico: '',
                validarCorreo:[
                    v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
                ],
                password: '',
                validarPassword:[
                    value => value.length >= 6 || 'Minimo 6 caracteres'
                    ]
                }
                
        },

        methods:{
            async loginBackend (){
                const valid = this.$refs.formlogin.validate()
                if(valid){
                    const sendData = {
                        email: this.correoElectronico,
                        password: this.password
                    }
                    await this.$auth.loginWith('local',{
                        data: sendData
                    }).then(async (res) =>{
                        console.log('Respuesta del back:', res)
                        if(res.data.error == null){
                            this.$router.push('/dashboard')
                        }
                    }).catch((error) =>{
                        console.log('error: ', error)
                    })
                }else{
                    alert('No cumpliste las reglas, come plomo lindo gatito')
                }
                
            }
        }
    }
</script>

<style scoped>
    .cardLogin{
        background-color:#F9A825;
        border-radius: 10px;
        width: 500px;
        height: 300px;
    }

    .imgLogin{
        width: 100%;
        border-radius: 10px;

    }

    .btnLogin{
        background-color: rgb(104, 211, 104) !important;
        color: black;
        
    }

    .title{
        font-size: 30px;
        justify-content: center;
        color: maroon;
        font-weight: 700;
    }
</style>