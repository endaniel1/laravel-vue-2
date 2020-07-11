<template>
    <div>       
       <form @submit.prevent="editarNota(nota)" v-if="editarActivo">
       	<h3>Editar Tareas</h3>
       	<input type="text" name="" v-model="nota.nombre" placeholder="Nombre" class="form-control mb-2">
       	<input type="text" name="" v-model="nota.descripcion" placeholder="Descripcion" class="form-control mb-2">
       	<button type="submit" class="btn btn-success">Guardar</button>
       	<button type="reset" class="btn btn-danger" @click="cancelarEditar">Cancelar</button>
       </form>

       <form @submit.prevent="agregar" v-else>
       	<h3>Agregar Tareas</h3>
       	<input type="text" name="" v-model="nota.nombre" placeholder="Nombre" class="form-control mb-2">
       	<input type="text" name="" v-model="nota.descripcion" placeholder="Descripcion" class="form-control mb-2">
       	<button type="submit" class="btn btn-primary">Agregar</button>
       </form>

       <h3 class="mt-3">Listado de Notas</h3>
       <ul class="list-group my-2">
       	<li class="list-group-item" v-for="(nota,key) in notas" :key="key">
       		<span class="badge badge-primary float-right">
       			{{nota.updated_at}}
       		</span>
       		<p>{{nota.nombre}}</p>
       		<p>{{nota.descripcion}}</p>
       		<button class="btn btn-danger btn-sm" @click="eliminarNota(nota,key)">Eliminar</button>
       		<button class="btn btn-warning btn-sm" @click="editarForm(nota)">Editar</button>
       	</li>
       </ul>
    </div>
</template>

<script>
    export default {
       data(){
       	return {
	       	notas:[],	
	       	nota:{nombre:"",descripcion:""},
	       	editarActivo:false
       	}
       },
       created(){       	
       		this.cargarNotas();
       },
       methods:{
       	agregar(){
       		if (this.nota.nombre.trim() === "" || this.nota.descripcion.trim() === "") {
       			alert("Debes Completar tODOS lOS cAMPOS");
       			return;
       		}

       		const params = {
       			nombre:this.nota.nombre,
       			descripcion:this.nota.descripcion
       		}
       		this.nota.nombre="";
       		this.nota.descripcion="";

       		axios.post("/notas", params)
       		.then(res => {
       			alert(`Nota ${res.data.nombre} Guarda!`);
       			this.cargarNotas();       			
       		})
       	},
       	eliminarNota(nota,index){
       		axios.delete("/notas/"+nota.id)
       		.then(res=>{
       			alert(`La Nota ${res.data.nombre} Fue Eliminada!`);
       			this.cargarNotas();   
       		})
       	},
       	editarForm(notaBuscarEditar){
       		this.editarActivo=true;
       		this.nota.nombre=notaBuscarEditar.nombre;
       		this.nota.descripcion=notaBuscarEditar.descripcion;
       		this.nota.id=notaBuscarEditar.id;
       	},
       	editarNota(notaEditar){
       		const params ={
       			nombre:notaEditar.nombre,
       			descripcion:notaEditar.descripcion
       		}
       		axios.put(`/notas/${notaEditar.id}`,params)
       		.then(res=>{
       			alert(`La Nota ${res.data.nombre} Fue Editada!`);
       			this.cancelarEditar();
       		})
       	},
       	cancelarEditar(){
       		this.editarActivo=false;

       		this.nota.nombre="";
       		this.nota.descripcion="";
       		this.cargarNotas();
       	},
       	cargarNotas(){
       		axios.get("/notas")
       		.then(res =>{
       			this.notas=res.data;
       		})
       	}
       }
    }
</script>