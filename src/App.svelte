<script>  
	import { onMount } from "svelte";
  	import axios from "axios";
	let name = 'Svelte';
	let apiData = $state([])
	let plotTemp = $state([])

	onMount(()=>{
		axios.get("https://tam-arduino-api.vercel.app/data").then((response)=>{
			apiData = response.data

			plotTemp = apiData.map((data)=> {
				return {x:data.create_date,y:data.temp}
			})

		})
	})	
	
	function getDate(timestamp){
		const date = new Date(0)
		date.setUTCSeconds(timestamp)

		return date.getDate() + "/" + (date.getMonth() + 1) + "/" + date.getFullYear()
	}

	function getHours(timestamp){
		const date = new Date(0)
		date.setUTCSeconds(timestamp)

		return date.getHours() + ":" + date.getMinutes() 
	}



</script>

<h1>Terrarium Monitor</h1>

<div id="main">

		{#if apiData.length > 0}

		<div>
		<h2>Latest Data</h2>
		<table>
			<thead>
				<tr>
					<th>Temperatura</th>
					<td>{apiData[0].temp}</td>
				</tr>
				<tr>
					<th>Humidade</th>
					<td>{apiData[0].humid}</td>
				</tr>	
				<tr>
					<th>Estado da Lampada</th>
					<td>{apiData[0].has_light}</td>
				</tr>
				<tr>
					<th>Qualidade do Ar</th>
					<td>{apiData[0].air_ok}</td>
				</tr>			
			</thead>
		</table>
	</div>
	{/if}

	<div>
		<h2>Data:</h2>
		<table>
			<thead>
                <tr>
                    <th>Dia</th>
					<th>Hora</th>
					<th>Temperatura</th>
					<th>Humidade</th>
					<th>Estado da Lampada</th>
					<th>Qualidade do Ar</th>
                </tr>

					{#each apiData as data}
						<tr>
						<td>{getDate(data.create_date)}</td>
						<td>{getHours(data.create_date)}</td>
						<td>{data.temp}</td>
						<td>{data.humid}</td>
						<td>{data.has_light}</td>
						<td>{data.air_ok}</td>
						</tr>
					{/each}

            </thead>

		</table>

	</div>


	

</div>

<style>
	#main{
		display: flex;
		align-items: top;
	}
	
	h1 {
		color: aquamarine;
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
	table, th, td {
		border: 1px solid;
 		border-collapse: collapse;
		margin-right: 32px;
		
	}
	th, td{
		padding: 8px;
	}
</style>







