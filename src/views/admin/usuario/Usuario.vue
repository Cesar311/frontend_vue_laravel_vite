<template>
    <div class="card">
        <h1>Usuario</h1>
     <!-- {{  usuarios }} -->
       {{ usuario }}
     <!-- {{ errors }}--> 
        <div>
            <label for="n">NOMBRE</label>
            <input type="text" id="n" v-model="usuario.name">
            <InputText id="name" v-model.trim="usuario.name" required="true" autofocus fluid />
            {{ errors.name }}
            <br>
            <label for="c">CORREO</label>
            <input type="email" id="c" v-model="usuario.email">
            {{ errors.email }}
            <br>
            <label for="p">CONTRASEÑA</label>
            <input type="password" id="p" v-model="usuario.password">
            {{ errors.password }}
            <br>
            <button @click="guardarUsuario">{{ usuario.id?'Modificar':'Guardar'}}</button>
            <button @click="cancelarRegistro">Cancelar</button>
        </div>
        <table border="1">
            <thead>
                <tr>
                    <td>ID</td>
                    <td>EMAIL</td>
                    <td>CREADO EN</td>
                    <td>ACCION</td>
                </tr>
            </thead>
            <tbody>
                <tr v-for="user in usuarios" :key="user.id">
                    <td>{{ user.id }}</td>
                    <td>{{ user.email }}</td>
                    <td>{{ user.created_at }}</td>
                    <td>
                        <button @click="funEditar(user)">EDITAR</button>
                        <button @click="funEliminar(user)">ELIMINAR</button>
                    </td>
                </tr>
            </tbody>
        </table>

        <DataTable :value="usuarios" tableStyle="min-width: 50rem">
            <Column field="id" header="ID"></Column>
            <Column field="email" header="EMAIL"></Column>
            <Column field="created_at" header="CREADO EN"></Column>
        </DataTable>

    </div>
</template>
<script setup>
// Importaciones
//import Swal from 'sweetalert2'  
import { onMounted, ref } from 'vue';
import usuarioService from '../../../services/usuario.service'

// Declarar variables
const usuarios = ref([]);
const usuario = ref({name: "", email: "", password: ""});
const errors = ref({});

// Ciclo de vida
onMounted(()=>{
    getUsuarios()
})

// Funciones o metodos
async function getUsuarios(){
    try {
        const { data } = await usuarioService.index();
        
        console.log(data);
        usuarios.value = data;
    } catch (error) {
        
        if(error.response.status == 404) {
            alert("La página buscada no existe");
            console.log(error);
        }
        
    }

}

async function guardarUsuario(){
    
    try {

        if(usuario.value.id){
            await usuarioService.update(usuario.value.id, usuario.value);

            // Swal.fire({
            //     title: "Usuario Actualizado!",
            //     text: "Para continuar presione OK!",
            //     icon: "success"
            // });
        }else {
            await usuarioService.store(usuario.value);
            
            //Swal.fire({
            //    title: "Usuario registrado!",
            //    text: "Para continuar presione OK!",
            //    icon: "success"
            //});
        }

            
        getUsuarios();
    } catch (error) {
        errors.value = error.response.data.errors;
        
        //alert("Error al registrar usuario");
    }
}

function funEditar(user){
    usuario.value = user;
}

async function funEliminar(user){
    if(confirm("¿Esta seguro de eliminar al usuari?")){
        await usuarioService.destroy(user.id);
        getUsuarios();

    }

}

function cancelarRegistro(){
    usuario.value = {name: "", email: "", password: ""};
}

</script>
