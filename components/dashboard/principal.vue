<template>
    <v-col cols= "12">
      <v-row class="renglon">
        <v-data-table 
          :headers = "headers"
          :items = "usuarios"
          :items-per-page = "5"
          class = "elevation-1"
          style="width: 100%;">

          <template #[`item.actions`]=" { item }">
            <v-row class="renglon">
              <v-col cols="6" >
                <v-btn icon color="orange" @click="dialogUpdate(item)">
                  <v-icon>mdi-human-edit</v-icon>
                </v-btn>
              </v-col>
              <v-col cols="6">
                <v-btn icon color="red" @click="dialogUser(item)">
                  <v-icon>mdi-eraser</v-icon>
                </v-btn>
              </v-col>
            </v-row>  
          </template>
        </v-data-table>
      </v-row>

      <v-row class="renglon">   
          <v-btn block color="blue" @click="open">
                    Nuevo Usuario
          </v-btn>
            
        </v-row>
        <v-dialog v-model="openDialog" width="800" height="500" persistent>
            <v-card>
                <v-card-title>
                    Datos del usuario
                </v-card-title>
                <v-card-text>
                    <!--Formulario para registrar-->
                    <v-form ref="formRegistro">
                        <v-text-field v-model="name" type="text" placeholder="Name:" label="Name"></v-text-field>
                        <v-text-field v-model="lastname" type="text" placeholder="Lastname:" label="Lastname"></v-text-field>
                        <v-text-field v-model="email" type="email" placeholder="Email:" label="Email"></v-text-field>
                        <v-text-field v-model="password" type="password" placeholder="Password:" label="Password"></v-text-field>
                    </v-form>
                </v-card-text>
                <v-card-actions style="width: 100%; display: flex; flex-direction: column;">
                    
                    <v-row style="width: 100%; margin-top:5px;margin-bottom: 10px;">
                        <v-btn block color="green" @click="registraUsuario">
                            Registrar
                        </v-btn>
                    </v-row>
                    <v-row style="width: 100%; margin-top:5px;margin-bottom: 10px;">
                        <v-btn block color="red" @click="openDialog= false">
                            Cancelar
                        </v-btn>
                    </v-row>

                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog v-model="openDialogErase" width="500" height="500" persistent>
            <v-card>
                <v-card-title>Borrar Usuario</v-card-title>
                <v-car-text>Realmente lo quieres borrar?</v-car-text>
                <v-card-actions>
                    <v-btn color="green" @click="openDialogErase = false">
                        Cancelar
                    </v-btn>
                    <v-spacer></v-spacer>
                    <v-btn color="red" @click="eraseUser">
                        Borrar
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog v-model="openDialogUpdate" width="800" height="500" persistent>
            <v-card>
                <v-card-title>
                    Datos del usuario
                </v-card-title>
                <v-card-text>
                    <!--Formulario para registrar-->
                    <v-form ref="formUpdate">
                        <v-text-field v-model="nameUpdate" type="text" placeholder="Name:" label="Name"></v-text-field>
                        <v-text-field v-model="lastnameUpdate" type="text" placeholder="Lastname:" label="Lastname"></v-text-field>
                        <v-text-field v-model="passwordUpdate" type="password" placeholder="Password:" label="Password"></v-text-field>
                    </v-form>
                </v-card-text>
                <v-card-actions style="width: 100%; display: flex; flex-direction: column;">
                    
                    <v-row style="width: 100%; margin-top:5px;margin-bottom: 10px;">
                        <v-btn block color="green" @click="actualizaUsuario">
                            Registrar
                        </v-btn>
                    </v-row>
                    <v-row style="width: 100%; margin-top:5px;margin-bottom: 10px;">
                        <v-btn block color="red" @click="openDialogUpdate= false">
                            Cancelar
                        </v-btn>
                    </v-row>

                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-col>
</template>

<script>
    export default {
        data() {
            return {
                usuarios: [],
                headers: [
                    {
                        text: 'Nombre',
                        align: 'center',
                        sortable: true,
                        value: 'name'
                    },
                    {
                        text: 'Apellidos',
                        align: 'center',
                        sortable: true,
                        value: 'lastname'
                    },{
                        text: 'Correo Electronico',
                        align: 'center',
                        sortable: true,
                        value: 'email'
                    },
                    {
                        text: 'Fecha de Creación',
                        align: 'center',
                        sortable: true,
                        value: 'date'
                    },
                    {
                        text: 'Acciones',
                        align: 'center',
                        sortable: true,
                        value: 'actions'
                    }
                ],
                openDialog: false,
                name:'',
                lastname: '',
                email: '',
                password: '',
                idEraserUser: '',
                openDialogErase: false,
                admin: 'AdminErick',
                openDialogUpdate: false,
                nameUpdate: '',
                lastnameUpdate: '',
                passwordUpdate: '',
                datos: {}
            }
        },
        mounted() {
            this.loadUsers()
        },
        methods: {
            async loadUsers() {
                const config = {
                    headers: {
                        'Content-Type': 'application/json;charset=UTF-8',
                        'Access-Control-Allow-Origin': '*'
                    }
                }
                await this.$axios.get('user/getallusers', config)
                    .then((res) => {
                        console.log('res',res)
                        if(res.data.message === 'Usuarios'){
                            this.usuarios = res.data.data
                        }
                    }) 
                    .catch((error) => {
                        console.log('error', error)
                    })
            },
            open (){
                this.openDialog = true
            },
            async registraUsuario(){
                const config = {
                    headers: {
                        'Content-Type': 'application/json;charset=UTF-8',
                        'Access-Control-Allow-Origin': '*'
                    }
                }
                const usuarioNuevo = {
                    name: this.name,
                    lastname: this.lastname,
                    email: this.email,
                    password: this.password
                }
                await this.$axios.post('user/register', usuarioNuevo, config)
                    .then((res) =>{
                        console.log('res',res)
                        if(res.data.error === null){
                            this.openDialog = false
                            this.loadUsers()
                        }
                    })
                    .catch((error) => {
                        console.log('error', error)
                    })
            },
            async eraseUser(){
              if(this.admin !== 'AdminErick'){
                const config = {
                    headers: {
                        'Content-Type': 'application/json;charset=UTF-8',
                        'Access-Control-Allow-Origin': '*'
                    }
                }
                const usuario = {
                    id: this.idEraserUser
                }
                await this.$axios.post('/user/eraseusers', usuario, config)
                    .then((res) => {
                        console.log(res)
                        if(res.data.message === 'Usuario borrado'){
                            this.loadUsers()
                            this.openDialogErase = false
                        }
                    })
                    .catch((error) => {
                        console.log(error)
                    })
              }
              
            },
            dialogUser( item ) {
                this.idEraserUser = item._id
                this.admin = item.name
                this.openDialogErase = true
            },
            dialogUpdate( item ) {
                this.datos = item
                this.nameUpdate = this.datos.name
                this.lastname = this.datos.lastname
                this.passwordUpdate = this.datos.password
                this.openDialogUpdate = true
            },
            async actualizaUsuario () {
                const config = {
                    headers: {
                        'Content-Type': 'application/json;charset=UTF-8',
                        'Access-Control-Allow-Origin': '*'
                    }
                }
                const usuarioNuevo = {
                    id: this.datos._id,
                    name: this.nameUpdate,
                    lastname: this.lastnameUpdate,
                    email: this.datos.email,
                    password: this.passwordUpdate
                }
                await this.$axios.post('/user/updateuser', usuarioNuevo, config)
                    .then((res) => {
                        console.log(res)
                        if(res.data.message === 'Usuario actualizado'){
                            this.loadUsers()
                            this.openDialogUpdate = false
                        }
                    })
                    .catch((error) => {
                        console.log(error)
                    })
            }
        }
    }
</script>

<style scoped>

.renglon{
    width:100%;
    margin-top: 20px;
    margin-bottom: 20px;
    height: auto;
}
</style>