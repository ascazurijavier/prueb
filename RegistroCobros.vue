<template>
	
<b-container fluid>
	<b-modal id="modal-registrar"
		ref="my-modal"
		header-bg-variant="success"
		size="lg" 
		:title="textoCobro+razonSocial"><!-- : -->
		<div class="modal-body">
			<b-container>
				<b-row class="my-0 bg-primary content shadow p-2 mb-2 mx-0 bg-white rounded overflow-auto text-right" align-v="end">
					<b-col sm="2" class="p-0">
						<label>Recibo N°:</label>
					</b-col>
					<b-col sm="3" class="p-0">
						<b-form-input 
							v-model="numeroRecibo"	
							placeholder=""
							maxlength="10"
						></b-form-input>
					</b-col>

					<b-col sm="1" class="p-0">
						<label>Precio:</label>
					</b-col>
					<b-col sm="3" class="text-right">
						<b-form-input 
							class="text-right"
							type="number"
							v-model="valor" 
							value="0.00"
						></b-form-input>
					</b-col>

				</b-row>						
			</b-container>	
		</div>
		<template v-slot:modal-footer="{ ok }" >
		
				<b-col class="text-right">
					<b-button class="mx-2" id="BotonOkAlta" variant="secondary" @click="ok()">Cancelar</b-button>

					<b-button id='Boton_Alta' variant="success" @click="registrarPago">Registrar	
					</b-button >
				</b-col>
		
		</template>
	</b-modal>


	<b-row class="text-center">

		<b-col md="9" class="py-0">
			<div class="content shadow p-3 mb-2 bg-white rounded overflow-auto" align="center" style="margin-top:0px; height:500px;  z-index: 0;">
				<b-table hover 
					:items="arrayResumenCuentaVentas"
					:fields="EncabezadoCuentaVentas"
					:tbody-tr-class="rowClass">			
				</b-table>
			</div> 
		</b-col>

		<b-col md="3" class="my-1">

			<label>Fecha desde:</label>
			<b-form-datepicker
				class="my-1"
				id="example-datepicker" 
				v-model="fechai"
				@input="cambiarFecha1()">
			</b-form-datepicker>

			<label class="my-1">hasta:</label>
			<b-form-datepicker  
				class="my-1"
				id="example-datepicker1" 
				v-model="fechaf"
				@input="cambiarFecha2()">
			</b-form-datepicker>

			<label class="mt-4">RazonSocial:</label>
			<b-form-input 
				v-model="criterioFiltroClientes"	
				placeholder="Filtro"
				@change="filtroClientes()"
			></b-form-input>
			<b-select 
				value-field="id"
				text-field="razon_social"
				v-model="cliente"
				:options="arrayClientesFiltrados"
				@change="resumenCuentaCliente()"
			></b-select> 			
			<b-button v-b-modal.modal-registrar 
				class="mt-4 px-5"
				variant="success"
				@click=""
				:disabled="eligioCliente"
				>Registrar Pago
			</b-button>	
	


		</b-col>

	</b-row>
</b-container>
</template>

<style>


</style>

<script>
	import {mapState,mapActions,mapMutations } from 'vuex';	
	export default {
		data() {
			return {
				EncabezadoCuentaVentas: [
					{
						key: 'fecha',
						label: 'Fecha',
						sortable: true,
						formatter: (value, key, item) => {
						return value.substring(8,10)+'/'+value.substring(5,7)+'/'+value.substring(0, 4);
						},
					}, 
					{
						key: 'tipo',
						label: 'Tipo',
					},
					{
						key: 'numero',
						label: 'Numero',
						tdClass:'text-right',
						thStyle:'text-align: right;',
					},
					{
						key: 'debe',
						label: 'Debe',
						tdClass:'text-right',
						thStyle:'text-align: right;',
					},
					{
						key: 'haber',
						label: 'Haber',
						tdClass:'text-right',
						thStyle:'text-align: right;',
					},
					{
						key: 'saldo',
						label: 'Saldo',
						tdClass:'text-right',
						thStyle:'text-align: right;',
					},					
				],
				arrayClientesFiltrados:[], 
				criterioFiltroClientes:'',
				fechai:new Date(),
				fechaf:new Date(),
				idClienteElegido:0,
				cliente:0,
				numeroRecibo:0,
				valor:0,
				textoCobro:'Cobro a: ',
				razonSocial:''

			}
		},
		computed:{
			...mapState(['arrayResumenCuentaVentas','arrayClientes','host','fecha1','fecha2','id_cliente']),
			eligioCliente(){
				return this.id_cliente==0 ? true : false
			}
			
			
		},
		methods:{
			...mapActions(['obtenerResumenCuentaVentas','obtenerClientes']), 
			...mapMutations(['valorFechai','valorFechaf','valorCliente']),
			cambiarFecha1(){
				this.valorFechai(this.fechai);
				this.obtenerResumenCuentaVentas();
			},
			cambiarFecha2(){
				this.valorFechaf(this.fechaf);
				this.obtenerResumenCuentaVentas();
			},
			cambiarCliente(){
				this.valorCliente(this.cliente);
			},	
			filtroClientes(){
				this.arrayClientesFiltrados = this.arrayClientes.filter(cliente => cliente.razon_social.toUpperCase().includes(this.criterioFiltroClientes.toUpperCase()));
			},
			rowClass(item, type) {
				if (!item || type !== 'row') return
					if (item.debe ==0) return 'table-warning';
			},
			clienteSeleccionado(){
				this.arrayCuentaVentasFiltrados = this.arrayCuentaVentas.filter(cuenta => cuenta.id_cliente==this.idClienteElegido);
			},
			resumenCuentaCliente(){
				this.cambiarCliente();
				this.obtenerResumenCuentaVentas();
				console.log(this.id_cliente);
				const found = this.arrayClientesFiltrados.find(element => element.id == this.id_cliente);
				this.razonSocial=found.razon_social;			
			},

			registrarPago: async function () {
				//this.$refs['my-modal'].hide()

				try{
					this.$refs['my-modal'].hide();
					let newPost={
						opcion:"registrarPago",
						id_cliente:this.id_cliente,
						numeroRecibo:this.numeroRecibo,
						valor:this.valor
					};
					const entradasBD = await fetch(this.host+'php/cuenta_ventas.php',{
						method:'POST',
						body: JSON.stringify(newPost),
						headers:{
								"Content-Type": "application/json"
						}
					});
					this.obtenerResumenCuentaVentas();
					// const datos = await entradasBD.json();
					// this.respuestaFactura(datos);
					// this.facturarPDF(datos);
				}catch (error){
					console.log(error);
				} 				
			},
		},
	    created(){
	        this.obtenerClientes();
			this.fechai=this.fecha1;
			this.fechaf=this.fecha2;
			this.fechai.setMonth(this.fechai.getMonth()-1);	        
	    },  

	}
</script>