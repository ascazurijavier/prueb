<template>
	<div>
		<div id="referenciaDesaparecen">
			<div id="referenciaDesaparecen1">
				<b-button v-b-modal.modal-facturar class="btn btn-success" style="margin-top:10px; width:150px;" @click="selecFactura">Facturar
				</b-button>
				<b-button v-b-modal.modal-facturar class="btn btn-success" style="margin-top:10px; width:150px;" @click="selecNota" :disabled="siFactura">Nota de Crédito
				</b-button>
				<b-button v-b-modal.modal-buscar class="btn btn-success" style="margin-top:30px; width:150px;">Buscar
				</b-button>
				<b-button v-b-modal.modal-generarPDF class="btn btn-warning" style="margin-top:30px; width:150px;" :disabled="siEligioRenglon">Generar PDF
				</b-button>
				<b-button v-b-modal.modal-variables class="btn btn-danger" style="margin-top:30px; width:150px;" >Variables
				</b-button>				
			</div>
		</div>

		<b-modal id="modal-variables"
			ref="my-modal"
			header-bg-variant="danger"
			size="xs" 
			title="Variables"><!-- : -->
			<div class="modal-body">
				<b-container>
					<b-row class="my-0 bg-primary content shadow p-2 mb-2 mx-0 bg-white rounded overflow-auto text-right" align-v="end">
						
						<b-col sm="4" class="p-0">
							<label>Alicuota del IVA:</label>
						</b-col>
						<b-col sm="3" class="p-0">
							<b-select 
								v-model="AlicuotasIVA"
								:options="arrayAlicuotasIVA"
								
							></b-select> 	
						</b-col>
					</b-row>
					<b-row class="my-0 bg-primary content shadow p-2 mb-2 mx-0 bg-white rounded overflow-auto text-right" align-v="end">						
						<b-col sm="4" class="p-0">
							<label>Punto de Venta:</label>
						</b-col>
						<b-col sm="2" class="p-0">
							<b-form-input 
								v-model="puntoVenta"
								disabled	
							></b-form-input>
						</b-col>
					</b-row>
										
				</b-container>	
			</div>
			<template v-slot:modal-footer="{ ok }" >
			
					<b-col class="text-right">
						<b-button class="mx-2" id="BotonActualizar" variant="secondary" @click="ok()">Cancelar</b-button>

						<b-button id='Boton_Buscar' variant="danger" @click="actualizarVariables()">Actualizar	
						</b-button >
					</b-col>
			
			</template>
		</b-modal>

		<b-modal id="modal-generarPDF"
			ref="my-modal"
			header-bg-variant="warning"
			size="xs" 
			title="Generar PDF"><!-- : -->
			<div class="modal-body">
				<h5 class="text-center">Esta seguro de Regenerar el Documento?</h5>
			</div>
			<template v-slot:modal-footer="{ ok }" >
			
					<b-col class="text-right">
						<b-button class="mx-2" id="BotonOkGenerarPDF" variant="secondary" @click="ok()">Cancelar</b-button>

						<b-button id='Boton_Buscar' variant="warning" @click="volverGenerarPDF()">Generar PDF	
						</b-button >
					</b-col>
			
			</template>
		</b-modal>

		<b-modal id="modal-buscar"
			ref="my-modal"
			header-bg-variant="success"
			size="xs" 
			title="Buscar"><!-- : -->
			<div class="modal-body">
				<b-container>
					<b-row class="my-0 bg-primary content shadow p-2 mb-2 mx-0 bg-white rounded overflow-auto text-right" align-v="end">
						<b-col sm="1" class="p-0">
							<label>Mes:</label>
						</b-col>
						<b-col sm="3" class="p-0">
							<b-select 
								value-field="id"
								text-field="texto"
								v-model="mesElegido"
								:options="arrayMeses"
								
							></b-select> 	
						</b-col>

						<b-col sm="1" class="p-0">
							<label>Año:</label>
						</b-col>
						<b-col sm="4" class="text-right">
							<b-select 

								v-model="añoElegido"
								:options="arrayAños"
							
							></b-select> 
						</b-col>

					</b-row>						
				</b-container>	
			</div>
			<template v-slot:modal-footer="{ ok }" >
			
					<b-col class="text-right">
						<b-button class="mx-2" id="BotonOkBuscar" variant="secondary" @click="ok()">Cancelar</b-button>

						<b-button id='Boton_Buscar' variant="success" @click="buscar()">Buscar	
						</b-button >
					</b-col>
			
			</template>
		</b-modal>

		<b-modal id="modal-facturar"
			ref="my-modal"
			header-bg-variant="success"
			size="xl" 
			:title="FacturaNota"><!-- : -->
			<div class="modal-body">
				<b-container>
					<b-row class="my-1 bg-primary content shadow p-2 mb-2 mx-0 bg-white rounded overflow-auto text-right " align-v="end" :hidden='mostrarFactura'>
						<b-col sm="1" class="p-0">
							<label>RazonSocial:</label>
						</b-col>
						<b-col sm="1" class="p-0">
							<b-form-input 
								v-model="criterioFiltroClientes"	
								placeholder="Nombre"
								@change="filtroClientes()"
							></b-form-input>
						</b-col>
						<b-col sm="3" class="p-0">
							<b-select 
								value-field="id"
								text-field="razon_social"
								v-model="idClienteElegido"
								:options="arrayClientesFiltrados"
								@change="clienteSeleccionado()"
							></b-select> 
						</b-col>
						<b-col sm="1" class="text-right p-0">
							<label >CUIT:</label>
						</b-col>							
						<b-col sm="2" class="p-0">
							<b-form-input 
								v-model="cuit"	
							></b-form-input>
						</b-col>
						<b-col sm="1" class="text-right p-0">
							<label >CondVenta:</label>
						</b-col>						
						<b-col sm="2" class="p-0">
							<b-select 
								value-field="id"
								text-field="detalle"
								v-model="idCondVenta"
								:options="arrayCondicionVenta"
								@change="">
									<template #first>
										<b-select-option :value="0">Seleccione...</b-select-option>
									</template>						
							</b-select> 
						</b-col>
					</b-row>
					<b-row class="my-1 bg-primary content shadow p-2 mb-2 mx-0 bg-white rounded overflow-auto text-right " align-v="end" :hidden='mostrarNota'>
						<b-col sm="1" class="p-0">
							<label>RazonSocial:</label>
						</b-col>
						<b-col sm="4" class="p-0" >
							<b-form-input disabled
								v-model="razon_social"	
							></b-form-input>
						</b-col>
						<b-col sm="2" class="p-0 ">
							<label>Comprobante:</label>
						</b-col>
						<b-col sm="2" class="p-0" >
							<b-form-input disabled
								v-model="NroCbteAsoc"	
							></b-form-input>
						</b-col>
					</b-row>
					<div class="content shadow p-3 mb-2 bg-white rounded overflow-auto" align="center" style="margin-top:20px; height:200px;">
						<b-table striped hover 
							:items="arrayRenglones" 
							:fields="EncabezadoRenglones"
							>
							<template #cell(show_details)="row">
								<b-button size="sm" @click="eliminarRenglon(row)" variant="link"><b-icon icon="Trash" variant="danger"></b-icon></b-button>
							</template>
						</b-table>
					</div> 

					<b-row class="my-1 bg-primary content shadow p-2 mb-2 mx-0 bg-white rounded overflow-auto text-right " align-v="end">
						<b-col sm="1" class="p-0">
							<label>Cant:</label>
						</b-col>
						<b-col sm="1" class="p-0">
							<b-form-input 
								class="text-right"
								type="number"
								v-model="cantidad"	
								placeholder="0.00"
								step="1.00"
								mask="0000.00"
								number
							></b-form-input>	
						</b-col>

						<b-col sm="1" class="p-0">
							<label>Detalle:</label>
						</b-col>
						<b-col sm="5" class="p-0">
							<b-form-input 
								v-model="detalle"	
								placeholder=""
								maxlength="80"
							></b-form-input>
						</b-col>

						<b-col sm="1" class="p-0">
							<label>Precio:</label>
						</b-col>
						<b-col sm="2" class="text-right">
							<b-form-input 
								class="text-right"
								type="number"
								v-model="precio"
								step="any"
								value="0.00"
								number
							></b-form-input>
						</b-col>
						<b-col sm="1">
							<b-button id='Boton_Alta' variant="success" @click="cargarRenglon()" :disabled="maximoRenglones"> Cargar	
							</b-button >
						</b-col>
					</b-row>						
				</b-container>	
			</div>
			<template v-slot:modal-footer="{ ok }" >
				<b-row class="bg-primary content shadow py-2 my-1 mx-4 bg-white rounded overflow-auto text-right" align-h="start"  align-v="end">
					<b-col sm="1" class="p-0 text-right">
						<label>Neto:</label>
					</b-col>
					<b-col sm="2" class="text-right p-0">
						<b-form-input 
							class="text-right "
							type="number"
							v-model="aFacturar.neto" 
						disabled
						></b-form-input><!-- -->
					</b-col>

					<b-col sm="1" class="p-0 text-right">
						<label>Iva:</label>
					</b-col>
					<b-col sm="2" class="text-right p-0">
						<b-form-input 
							class="text-right"
							type="number"
							v-model="aFacturar.iva" 
						disabled
						></b-form-input><!-- -->
					</b-col>			

					<b-col sm="1" class="mx-0 p-0 text-right">
						<label>Total:</label>
					</b-col>
					<b-col sm="2" class="mx-0 text-right p-0">
						<b-form-input 
							class="text-right"
							type="number"
							v-model="aFacturar.total" 
							value="0.00"
							disabled	
						></b-form-input><!-- -->
					</b-col>			
				
					<b-col sm="3" class="text-right">
						<b-button class="mx-1" id="BotonOkAlta" variant="secondary" @click="ok()">Cancelar</b-button>

						<b-button id='Boton_Alta' variant="success" @click="facturar()" :disabled="activarBotonfacturar" :hidden='mostrarFactura'> Facturar	
						</b-button >
						<b-button id='Boton_Nota' variant="success" @click="NotaCredito()" :disabled="activarBotonNotaCredito" :hidden='mostrarNota'>N Crédito	
						</b-button >					
					</b-col>
				</b-row>
			</template>
		</b-modal>

		<div class="content shadow p-3 mb-2 bg-white rounded overflow-auto" align="center" style="margin-top:0px; height:550px;  z-index: 0;">
			<b-table striped hover 
				:items="arrayFacturasVentas" 
				:fields="EncabezadoFacturasVentas"
				selectable
				select-mode="single"				
				@row-selected="onRowSelected" 
				:tbody-tr-class="rowClass">
				<template #cell(show_details)="row">
					<b-button size="sm" @click="abrirFacturaPDF(row)" variant="link"><b-icon icon="printer-fill" variant="success"></b-icon></b-button>				
				</template>				
			</b-table>
		</div> 
		<a id="FacturaPDF" href="" target="_blank"></a>
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
				arrayMeses:[{
						id:'01',
						texto:'Enero'
					},
					{
						id:'02',
						texto:'Febrero'
					},
					{
						id:'03',
						texto:'Marzo'
					},
					{
						id:'04',
						texto:'Abril'
					},
					{
						id:'05',
						texto:'Mayo'
					},
					{
						id:'06',
						texto:'Junio'
					},
					{
						id:'07',
						texto:'Julio'
					},
					{
						id:'08',
						texto:'Agosto'
					},
					{
						id:'09',
						texto:'Septimbre'
					},
					{
						id:'10',
						texto:'Octubre'
					},
					{
						id:'11',
						texto:'Noviembre'
					},
					{
						id:'12',
						texto:'Diciembre'
					},
				],
				arrayAños:[2022,2023,2024,2025,2026,2027,2028,2029,2030,2031,2032,2033,2034,2035,2036,2037,2038,2039,2040],
 				filtroFacturas:{desde:'',hasta:''},
 				mesElegido:'',
 				añoElegido:'',
				arrayAlicuotasIVA:[21,10.5],
				AlicuotasIVA:21,
				EncabezadoFacturasVentas: [
					'numero',
					{
						key: 'fecha',
						label: 'Fecha',
						sortable: true,
						formatter: (value, key, item) => {
						return value.substring(8,10)+'/'+value.substring(5,7)+'/'+value.substring(0, 4);
						},
					}, 					
					'cliente',
					{
						key: 'tipo',
						label: 'Tipo',
						sortable: true,
						formatter: (value, key, item) => {
						return value==1 ? 'Factura' : 'Nota Crédito'
						},
					},					
					'tipo_a_b',
					'iva',
					'neto',
					'total',
					{
						key:'show_details',
						label:'',
						thStyle:'width:20px;',
					},
				],
				EncabezadoRenglones: [{
						key:'cantidad',
						label:'Cant',
						thStyle:'text-shadow: 2px 2px 5px green; text-align: right;',
						tdClass:'text-right',
						formatter: (value, key, item) => {
							return value.toFixed(2);;
						},					
					},
					{
						key:'detalle',
						label:'Detalle',
						thStyle:'text-shadow: 2px 2px 5px green;',
					
					},
					{
						key:'precio',
						label:'Precio',
						thStyle:'text-shadow: 2px 2px 5px green;text-align: right;',
						tdClass:'text-right',
						formatter: (value, key, item) => {
							return value.toFixed(2);;
						},						
					},
					{
						key:'neto',
						label:'Neto',
						thStyle:'text-shadow: 2px 2px 5px green;text-align: right;',
						tdClass:'text-right',
						formatter: (value, key, item) => {
							return value.toFixed(2);;
						},						
					},					
					{
						key:'total',
						label:'Total',
						thStyle:'text-shadow: 2px 2px 5px green;text-align: right;',
						tdClass: (value, key, item) => {
							return value==0 ? 'bg-warning text-right' : 'text-right'
	
						},
						formatter: (value, key, item) => {
							return value.toFixed(2);;
						},						

					},
					{
						key:'show_details',
						label:'',
						thStyle:'width:20px;',
					},
				],

				FacturaNota:'',
				id_factura:0,
				NroCbteAsoc:0,
				FchDesdeCbteAsoc:'',
				idCliente:0,
				razon_social:'',
				tipo:0,

				criterioFiltroClientes:'',
				arrayClientesFiltrados:[],
				idClienteElegido:0,
				idCondVenta:0,
				cuit:'',
				inscripcionCliente:'',

				arrayRenglones : [],

				cantidad:0,
				detalle: "",
				precio: 0,
				aFacturar:[{
					total:0,
					neto:0,
					iva:0,
				}]
	
			}
		},
		computed:{
			...mapState(['arrayFacturasVentas','arrayClientes','arrayCondicionVenta','iva','host','puntoVenta']),
			maximoRenglones(){
				return this.arrayRenglones.length>16 ? true : false
			},
			activarBotonfacturar(){
				return this.idCondVenta==0 || this.idClienteElegido==0 || this.arrayRenglones.length==0 || this.aFacturar.total==0 ? true : false
			},
			activarBotonNotaCredito(){
				return this.NroCbteAsoc==0 || this.arrayRenglones.length==0 || this.aFacturar.total==0 ? true : false
			},
			mostrarFactura(){
				return this.FacturaNota=='Factura' ? false : true
			},
			mostrarNota(){
				return this.FacturaNota=='Nota de Crédito' ? false : true
			},
			siFactura(){
				 return this.tipo==1 ? false : true 
			},
			siEligioRenglon(){
				 return this.id_factura!=0 ? false : true 
			},

				
		},
		methods:{
			...mapActions(['obtenerFacturasVentas','obtenerClientes','obtenerCondicionVenta','obtenerPuntoVenta']), 
			...mapMutations(['valorIva']),
			actualizarVariables(){
				this.valorIva(this.AlicuotasIVA);
				this.sumarNetoTotal(this.AlicuotasIVA);
				document.getElementById('BotonActualizar').click();
			},			

			selecFactura(){
				this.FacturaNota='Factura';
			},
			buscar(){
	    		var hoy = new Date(this.añoElegido+'/'+this.mesElegido+'/01');
				var ultimoDia = new Date(hoy.getFullYear(), hoy.getMonth() + 1, 0);
	 			this.mesElegido=("0" + (hoy.getMonth() + 1)).slice(-2);
	 			this.añoElegido=hoy.getFullYear();
		    	this.filtroFacturas.desde=this.añoElegido+'/'+this.mesElegido+'/01';
		    	this.filtroFacturas.hasta=this.añoElegido+'/'+this.mesElegido+'/'+ultimoDia.getDate();
				this.obtenerFacturasVentas(this.filtroFacturas);
				document.getElementById('BotonOkBuscar').click();
			},
			selecNota(){
				this.FacturaNota='Nota de Crédito';
			},
			onRowSelected(items) {
				console.log(items)
				if (items.length==1){
					this.id_factura=items[0]['id'];
					this.NroCbteAsoc=items[0]['numero'];
					this.FchDesdeCbteAsoc=items[0]['fecha'];
					this.idClienteElegido=items[0]['cod_clie'];
					this.razon_social=items[0]['cliente'];
					this.tipo=items[0]['tipo'];
					this.clienteSeleccionado();
				}
			},  

			filtroClientes(){
				this.arrayClientesFiltrados = this.arrayClientes.filter(cliente => cliente.razon_social.toUpperCase().includes(this.criterioFiltroClientes.toUpperCase()));
			},

			rowClass(item, type) {
				if (!item || type !== 'row') return
				if (item.anulada =='V') return 'table-warning';
			},

			eliminarRenglon(item){
				var removed = this.arrayRenglones.splice(item.index, 1);
				this.sumarNetoTotal(this.iva);
			},

			abrirFacturaPDF(item){
				console.log(item.item);
				// console.log(item.item.tipo_a_b);
				// console.log(item.item.cliente);
				if(item.item.tipo=='1'){
					var nombreFactura='Factura '+item.item.tipo_a_b+' '+item.item.numero+' '+item.item.cliente.trim()+'.pdf';
				}else{
					var nombreFactura='NotaCredito '+item.item.tipo_a_b+' '+item.item.numero+' '+item.item.cliente.trim()+'.pdf';					
				}

				document.getElementById('FacturaPDF').href='//localhost:80/pizzorno/facturar/test/pdf/verPDF.php?nombre='+nombreFactura;
				document.getElementById('FacturaPDF').click();
			},

			respuestaFactura(respuesta){
				if(typeof(respuesta.FeCabResp) != "undefined"){ 
					console.log(respuesta.FeCabResp.FchProceso);
					console.log(respuesta.FeDetResp.FECAEDetResponse.CAE);
					// console.log(respuesta.FeDetResp.FECAEDetResponse.CAEFchVto);
					// console.log(respuesta.FeDetResp.FECAEDetResponse.CbteDesde);
					// console.log(respuesta.FeDetResp.FECAEDetResponse.CbteFch);
					// console.log(respuesta.FeDetResp.FECAEDetResponse.DocNro);
					// console.log(respuesta.FeDetResp.FECAEDetResponse.Resultado);
					// console.log(respuesta.FeDetResp.FECAEDetResponse.Observaciones);	
				}
				this.obtenerFacturasVentas(this.filtroFacturas);
			},
			
			facturarPDF: async function (respuesta) {
				// console.log(respuesta.FeDetResp.FECAEDetResponse.CAE);
				//this.$refs['my-modal'].hide()
				try{
					
				let newPost={
					opcion:"FacturaA",
					id_cliente:this.idClienteElegido,
					cuit:this.cuit,
					aFacturar:this.aFacturar,
					arrayRenglones:this.arrayRenglones,
					PuntoVenta:respuesta.FeCabResp.PtoVta,
					idCondVenta:this.idCondVenta,
					CAE:respuesta.FeDetResp.FECAEDetResponse.CAE,
					CAEFchVto:respuesta.FeDetResp.FECAEDetResponse.CAEFchVto,
					numero:respuesta.FeDetResp.FECAEDetResponse.CbteDesde,
					inscripcionCliente:this.inscripcionCliente,
					iva:this.iva,
				};
				const entradasBD = await fetch(this.host+'facturar/test/pdf/factura.php',{
					method:'POST',
					body: JSON.stringify(newPost),
					headers:{
							"Content-Type": "application/json"
					}
				});
				// const datos = await entradasBD.json();
				// console.log(datos)
				}catch (error){
					console.log(error);
				} 				
			},

			volverGenerarPDF: async function () {
				document.getElementById('BotonOkGenerarPDF').click();
				try{
					let newPost={
						id_factura:this.id_factura,
						iva:this.iva,
						PtoVta:this.puntoVenta,
					};
					const entradasBD = await fetch(this.host+'facturar/test/pdf/factura_regenerar.php',{
						method:'POST',
						body: JSON.stringify(newPost),
						headers:{
								"Content-Type": "application/json"
						}
					});
					//const datos = await entradasBD.json();
				}catch (error){
					console.log(error);
				}
							
			},

			facturar: async function () {
				//this.$refs['my-modal'].hide()

				try{
					this.$refs['my-modal'].hide();
					let newPost={
						opcion:"FacturaA",
						id_cliente:this.idClienteElegido,
						cuit:this.cuit,
						iva:this.iva,
						aFacturar:this.aFacturar,
						idCondVenta:this.idCondVenta,
						arrayRenglones:this.arrayRenglones,
						inscripcionCliente:this.inscripcionCliente,
						PtoVta:this.puntoVenta,
					};
					const entradasBD = await fetch(this.host+'facturar/test/facturarAB.php',{
						method:'POST',
						body: JSON.stringify(newPost),
						headers:{
								"Content-Type": "application/json"
						}
					});
					const datos = await entradasBD.json();
					this.respuestaFactura(datos);
					this.facturarPDF(datos);
				}catch (error){
					console.log(error);
				} 				
			},

			NotaCreditoPDF: async function (respuesta) {
				try{
					let newPost={
						opcion:"FacturaA",
						id_cliente:this.idClienteElegido,
						cuit:this.cuit,
						aFacturar:this.aFacturar,
						arrayRenglones:this.arrayRenglones,
						PuntoVenta:respuesta.FeCabResp.PtoVta,
						idCondVenta:this.idCondVenta,
						CAE:respuesta.FeDetResp.FECAEDetResponse.CAE,
						CAEFchVto:respuesta.FeDetResp.FECAEDetResponse.CAEFchVto,
						numero:respuesta.FeDetResp.FECAEDetResponse.CbteDesde,
						inscripcionCliente:this.inscripcionCliente,
						iva:this.iva,
					};

					const entradasBD = await fetch(this.host+'facturar/test/pdf/notaCredito.php',{
						method:'POST',
						body: JSON.stringify(newPost),
						headers:{
								"Content-Type": "application/json"
						}
					});
					const datos = await entradasBD.json();
					console.log(datos)
				}catch (error){
					console.log(error);
				} 				
			},

			NotaCredito: async function () {
				//this.$refs['my-modal'].hide()
				try{
					this.$refs['my-modal'].hide();
					let newPost={
						opcion:"NotaCredito",
						id_cliente:this.idClienteElegido,
						cuit:this.cuit,
						iva:this.iva,
						aFacturar:this.aFacturar,
						idCondVenta:this.idCondVenta,
						arrayRenglones:this.arrayRenglones,
						inscripcionCliente:this.inscripcionCliente,
						NroCbteAsoc:this.NroCbteAsoc,
						FchDesdeCbteAsoc:this.FchDesdeCbteAsoc,
						PtoVta:this.puntoVenta,
					};
					const entradasBD = await fetch(this.host+'facturar/test/notaCreditoAB.php',{
						method:'POST',
						body: JSON.stringify(newPost),
						headers:{
								"Content-Type": "application/json"
						}
					});
					const datos = await entradasBD.json();
					this.respuestaFactura(datos);
					this.NotaCreditoPDF(datos);
				}catch (error){
					console.log(error);
				} 				
			},

			sumarNetoTotal(iva){
				if (this.arrayRenglones.length!=0){
					this.arrayRenglones.forEach(function(renglon){
						return renglon.neto=renglon.precio*renglon.cantidad;
					});
					this.arrayRenglones.forEach(function(renglon){
						return renglon.total=renglon.neto*(1+iva/100);
					});	
					this.aFacturar= this.arrayRenglones.reduce((a, b) => ({ 
						neto: a.neto + b.neto,
						total: a.total + b.total
					}));
					this.aFacturar.iva=this.aFacturar.total-this.aFacturar.neto;
					this.aFacturar.neto=parseFloat(this.aFacturar.neto.toFixed(2));	
					this.aFacturar.total=parseFloat(this.aFacturar.total.toFixed(2));
					this.aFacturar.iva=parseFloat(this.aFacturar.iva.toFixed(2));
				}
			},

			cargarRenglon(){
				console.log(1+this.iva/100)
				this.arrayRenglones.push({
					"idArticulo":0,
					"cantidad":this.cantidad,
					"detalle":this.detalle,
					"precio":this.precio,
					 "neto":0,
					 "total":0					
					// "neto":this.precio*this.cantidad,
					// "total":this.precio*this.cantidad*(1+this.iva/100)
				});
				this.sumarNetoTotal(this.iva);
			},

			clienteSeleccionado(){
				console.log(this.idClienteElegido);
				const found = this.arrayClientes.find(element => element.id == this.idClienteElegido);
				this.cuit=found.cuit;
				this.inscripcionCliente=found.responsable;
				console.log(this.cuit);
				this.sumarNetoTotal(this.iva);
			},

		},
	    created(){
	    	var hoy = new Date();
			var ultimoDia = new Date(hoy.getFullYear(), hoy.getMonth() + 1, 0);
 			this.mesElegido=("0" + (hoy.getMonth() + 1)).slice(-2);
 			this.añoElegido=hoy.getFullYear();
	    	this.filtroFacturas.desde=hoy.getFullYear()+'/'+("0" + (hoy.getMonth() + 1)).slice(-2)+'/01';
	    	this.filtroFacturas.hasta=hoy.getFullYear()+'/'+("0" + (hoy.getMonth() + 1)).slice(-2)+'/'+ultimoDia.getDate();
	    	this.obtenerPuntoVenta();
	        this.obtenerFacturasVentas(this.filtroFacturas);
	        this.obtenerClientes();
	        this.obtenerCondicionVenta();
	    },  

	}
</script>