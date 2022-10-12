<template>
	
<b-container fluid>
	<b-row class="text-center">

		<b-col md="10" class="py-0">
			<div class="content shadow p-3 mb-2 bg-white rounded overflow-auto" align="center" style=" height:500px;  z-index: 0;">
				<b-table striped hover 
					:items="arrayCuentaVentas" 
					:fields="EncabezadoCuentaVentas"
					>
					<template #cell(pdf)="row">
						<b-button size="sm" @click="generarPDF(row)" >PDF</b-button><!-- <b-icon icon="Trash" variant="danger"></b-icon> -->
					</template>								
				</b-table>
			</div> 
		</b-col>

		<b-col md="2" class="my-1">
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
			<b-button 
				class="my-1 px-5"
				variant="success"
				@click="obtenerCuentaVentas()">Generar Saldo
			</b-button>		
			<b-button 
				class="my-1 px-5"
				variant="success"
				@click="saldoVentasPDF()">Generar PDF
			</b-button>				
		</b-col>

	</b-row>
	<a id="CuentaPDF" href="" target="_blank"></a>
	<a id="SaldosPDF" href="" target="_blank"></a>
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
						key: 'id',
						label: 'ID',
					},					
					{
						key: 'razon_social',
						label: 'Razon Social',
						sortable: true,
					},
					{
						key: 'anterior',
						label: 'Anterior',
					},					
					{
						key: 'debe',
						label: 'Debe',
					},
					{
						key: 'haber',
						label: 'Haber',
					},
					{
						key: 'saldo',
						label: 'Saldo',
					},
					{
						key: 'pdf',
					}
				],
				fechai:new Date(),
				fechaf:new Date(),
				link:'php/cuenta_ventas_pdf.php?cliente=5',
			}
		},
		computed:{
			...mapState(['arrayCuentaVentas','host','fecha1','fecha2']),
		},
		methods:{
			...mapActions(['obtenerCuentaVentas']), 
			...mapMutations(['valorFechai','valorFechaf']),
			generarPDF(item){
				console.log(item.item.id);
				// this.link=this.host+'php/cuenta_ventas_pdf.php?cliente='+item.item.id;
				// document.getElementById('CuentaPDF').click();


				document.getElementById('CuentaPDF').href='//localhost:80/pizzorno/php/cuenta_ventas_pdf.php?cliente='+item.item.id+'&fecha1='+this.fecha1+'&fecha2='+this.fecha2;
				document.getElementById('CuentaPDF').click();
			},
			cambiarFecha1(){
				this.valorFechai(this.fechai);
			},
			cambiarFecha2(){
				this.valorFechaf(this.fechaf);
			},
			saldoVentasPDF: async function () {
				//this.$refs['my-modal'].hide()
				try{
					let newPost={
						opcion:"SaldoVentas",
						arrayCuentaVentas:this.arrayCuentaVentas
					};
					const entradasBD = await fetch(this.host+'facturar/test/pdf/saldoVentas.php',{
						method:'POST',
						body: JSON.stringify(newPost),
						headers:{
								"Content-Type": "application/json"
						}
					});
					const datos = await entradasBD.json();
					document.getElementById('SaldosPDF').href='//localhost:80/pizzorno/facturar/test/pdf/'+datos[0];
					document.getElementById('SaldosPDF').click();
				}catch (error){
					console.log(error);
				} 				
			},		

		},
	    created(){
			this.fechai=this.fecha1;
			this.fechaf=this.fecha2;
	    },  

	}
</script>