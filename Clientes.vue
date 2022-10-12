<template>
	<div>
		<div id="referenciaDesaparecen">
			<div id="referenciaDesaparecen1">
				<b-button v-b-modal.modal-alta class="btn btn-success" style="margin-top:10px; width:150px;" @click="alta1()">Alta 
					
				</b-button><!-- <b-icon icon="file-earmark-plus" aria-hidden="true">  -->
				<b-button v-b-modal.modal-alta class="btn btn-success" style="margin-top:15px; width:150px;" @click="modificar1()" :hidden="mostrarModificarEliminar">Modificar 
					<!-- <b-icon icon="file-text" aria-hidden="true">  -->
				</b-button><!--   -->
				<b-button v-b-modal.modal-alta class="btn btn-success" style="margin-top:15px; width:150px;" @click="eliminar1()" :hidden="mostrarModificarEliminar">Eliminar 
					<!-- <b-icon icon="trash-fill" aria-hidden="true">  -->
				</b-button><!--  -->
				<b-button v-b-modal.modal-buscar class="btn btn-success" style="margin-top:15px; width:150px;">Buscar
				</b-button>
			</div>  
		</div>

		<div class="content shadow p-3 mb-2 bg-white rounded overflow-auto" align="center" style=" height:500px;  z-index: 0;">
			<b-table striped hover 
				ref="selectableTable"
				:items="arrayClientes" 
				:fields="EncabezadoClientes"
				:per-page="perPage"
				:current-page="currentPage"
				selectable
				select-mode="single"
				@row-selected="onRowSelected" 
				:filter="criterio"			 
				:filter-function="filtro"
				:tbody-tr-class="rowClass"
			></b-table>
		</div>

		<b-pagination
			v-model="currentPage"
			:total-rows="renglones"
			:per-page="perPage"
			align="center"
		></b-pagination>

		<b-modal id="modal-buscar"
			ref="my-modal-buscar"
			header-bg-variant="success"
			size="sx" 
			title="Buscar Clientes">
			<div class="modal-body">
				<b-container class="bv-example-row">
						<b-row class="my-1" align-v="end" >
							<b-col sm="4" class="text-right">
								<label for="input-default">Razon Social:</label>
							</b-col>
							<b-col sm="7" class="">
								<b-form-input 
									v-model="criterio[1]"	
									placeholder="Nombre"
								></b-form-input>
							</b-col>
						</b-row>
				</b-container>	
			</div>
			<template v-slot:modal-footer="{ ok }" >			
				<b-button id="BotonOkAlta" variant="secondary" @click="ok()">Ok</b-button>
			</template>
		</b-modal>

		<b-modal id="modal-alta"
			ref="my-modal"
			header-bg-variant="success"
			size="xl" 
			:title="titulo"><!-- : -->
			<div class="modal-body">
				<b-container>
					<b-row class="mx-0" align-v="end">
						<b-col sm="5">
							<label>RazónSocial:</label>
							<b-form-input 
								v-model="razon_social"	
								placeholder="Nombre"
							></b-form-input>
						</b-col>
						<b-col sm="3">
							<label for="input-default">C.U.I.T:</label>
							<b-form-input 
								v-model="cuit"
								placeholder="Nombre"
							></b-form-input>
						</b-col>    

						<b-col sm="4" >
							<label >Responsabilidad:</label>
							<b-select
								v-model="id_responsabilidad"
								:options="arrayResponsabilidad"
								
								value-field="id"
								text-field="detalle"
								disabled-field="notEnabled"
							></b-select>
						</b-col>

						<b-col sm="4">
							<label>Dirección:</label>
							<b-form-input 
								v-model="direccion"	
								placeholder="Dirección"
							></b-form-input>
						</b-col>
						<b-col sm="3">
							<label>Teléfono:</label>
							<b-form-input 
								v-model="telefono"	
								placeholder="Teléfono"
							></b-form-input>
						</b-col>
					</b-row>					
				</b-container>	
			</div>
			<template v-slot:modal-footer="{ ok }" >			
				<b-button id="BotonOkAlta" variant="secondary" @click="ok()">Cancelar</b-button>

				<b-button id='Boton_Alta' variant="success" :hidden="mostrarBotonAlta" @click="alta()"><!-- 
					 
					<b-icon icon="cloud-upload" aria-hidden="true"> 
				-->Guardar Cliente				
					
				</b-button >

				<b-button id='Boton_Modificar' variant="success" :hidden="mostrarBotonModificar" @click="modificar()"><!-- hidden="mostrarBotonModificar" 
	<b-icon icon="cloud-upload" aria-hidden="true">
				-->Guardar Cliente
					 
				</b-button >
				<b-button id='Boton_Eliminar' variant="danger" :hidden="mostrarBotonEliminar" @click="eliminar()">Eliminar Cliente
					<!-- <b-icon icon="trash-fill" aria-hidden="true"> --> 
				</b-button >
				
			</template>
		</b-modal>
	</div>
</template>
<style>

#referenciaDesaparecen{
	top :60px;
	position:relative;
	z-index: 1;
	margin-right: 100px;
}
#referenciaDesaparecen1{
	top :0px;
	right: 20px;
	width: 120px;
	height: 400px;
	padding: 10px;

	
	padding-top: 5px;
	background-color: rgba(255, 255, 255,0);
	position:absolute;
	opacity: 0.3;
	z-index: 2;
	-webkit-transition:2s;
	-moz-transition:2s;
	-o-transition:2s;	
}

#referenciaDesaparecen1:hover {
	cursor: default;
	opacity: 1;
}


</style>
<script>
	import {mapState,mapActions,mapMutations } from 'vuex';	
	export default {
		data() {
			return {
				
				EncabezadoClientes: [
					'id',
					{
						key: 'razon_social',
						label: 'Razón Social',
						sortable: true
					},
					{
						key: 'detalle',
						label: 'Responsable',
						sortable: true
					},					
					{
						key: 'cuit',
						label: 'C.U.I.T.',
						//sortable: true
					},					
				],
				criterio:['F',''],
				id:0,
				razon_social:'',
				cuit:'',
				direccion:'',
				telefono:'',

				titulo:'',
				id_responsabilidad:0,
				perPage: 100,
				currentPage: 1,
				renglones:0,				
			}
		},
		computed:{
			...mapState(['arrayClientes','host','arrayResponsabilidad']),
			mostrarModificarEliminar(){
				return this.id == 0 ? true : false
			},
			mostrarBotonAlta(){
				return this.titulo == 'Alta de Cliente' ? false: true
			},
			mostrarBotonModificar(){
				return this.titulo == 'Modificar Cliente' ? false : true
			},
			mostrarBotonEliminar(){
				return this.titulo == 'Eliminar Cliente' ? false : true
			},
			verificar(){
				return this.titulo == 'Alta de Cliente' ? false: true
			}


		},
		methods:{
			...mapActions(['obtenerResponsabilidad','obtenerClientes']), 
			//...mapMutations(['valorUsuario']),
			onRowSelected(items) {
				if (items.length==1){
					this.id=items[0]['id'];
					this.razon_social=items[0]['razon_social'];
					this.cuit=items[0]['cuit'];
					this.id_responsabilidad=items[0]['responsable'];
					this.direccion=items[0]['direccion'];
					this.telefono=items[0]['telefono'];

				}
			},
			rowClass(item, type) {
				if (!item || type !== 'row') return

				if (item.cuitBien =='No') return 'table-warning';
			},
			filtro(row, filter) {
				if (filter[0]==row.eliminado){
					if (row.razon_social.toUpperCase().includes(filter[1].toUpperCase())){
						return true;
					}else{
						return false; 
					}
				}else{
					return false;         
				}
			},
			alta1(){
				this.titulo='Alta de Cliente';
				this.id=0;
				this.razon_social='';
				this.cuit='';
			},
			modificar1(){
				this.titulo='Modificar Cliente';
			},
			eliminar1(){
				this.titulo='Eliminar Cliente';
			},


			alta: async function () {
				try{
					this.$refs['my-modal'].hide()
				let newPost={
					opcion:"Alta",
					razon_social:this.razon_social,
					cuit:this.cuit,
					direccion:this.direccion,
					telefono:this.telefono,
					id_responsabilidad:this.id_responsabilidad
				};
				const entradasBD = await fetch(this.host+'php/clientes.php',{
					method:'POST',
					body: JSON.stringify(newPost),
					headers:{
							"Content-Type": "application/json"
					}
				});
				//const datos = await entradasBD.json();
				const datos = await entradasBD;
				console.log(datos);
				this.obtenerClientes();
				}catch (error){
				console.log(error);
				} 				
			},
			modificar: async function () {
				try{
					this.$refs['my-modal'].hide()
				let newPost={
					opcion:"Modificar",
					id:this.id,
					razon_social:this.razon_social,
					cuit:this.cuit,
					direccion:this.direccion,
					telefono:this.telefono,					
					id_responsabilidad:this.id_responsabilidad
				};
				const entradasBD = await fetch(this.host+'php/clientes.php',{
					method:'POST',
					body: JSON.stringify(newPost),
					headers:{
							"Content-Type": "application/json"
					}
				});
				//const datos = await entradasBD.json();
				const datos = await entradasBD;
				console.log(datos);
				this.obtenerClientes();
				}catch (error){
				console.log(error);
				} 				
			},
			eliminar: async function () {
				try{
					this.$refs['my-modal'].hide()
				let newPost={
					opcion:"Eliminar",
					id:this.id
				};
				const entradasBD = await fetch(this.host+'php/clientes.php',{
					method:'POST',
					body: JSON.stringify(newPost),
					headers:{
							"Content-Type": "application/json"
					}
				});
				//const datos = await entradasBD.json();
				const datos = await entradasBD;
				console.log(datos);
				this.obtenerClientes();
				}catch (error){
				console.log(error);
				} 				
			},			
		},
	    created(){
	        this.obtenerClientes();
	        this.obtenerResponsabilidad();
	    },  
	    updated(){
	      this.renglones=this.$refs.selectableTable.filteredItems.length;
	    },
	}
</script>