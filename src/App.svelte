<script>  
	import { onMount } from "svelte";
  	import axios from "axios";
	let name = 'Svelte';
	let apiData = $state([])
	let logData = $state([])
	let plotTemp = $state([])
	let buttonText = $state("")

	onMount(()=>{
		axios.get("https://tam-arduino-api.vercel.app/data").then((response)=>{
			apiData = response.data

			plotTemp = apiData.map((data)=> {
				return {x:data.create_date,y:data.temp}
			})

		})
		axios.get("https://tam-arduino-api.vercel.app/led").then((response)=>{
			setButtonText(response.data.on_state)
		})
		axios.get("https://tam-arduino-api.vercel.app/logs").then((response)=>{
			logData = response.data

			plotTemp = logData.map((data)=> {
				return {x:data.create_date,y:data.temp}
			})

		})
		
	})	
	function setButtonText(ledState){
		console.log(ledState)
		if(ledState)
			buttonText = "Desligar"
		else
			buttonText = "Ligar"
	}
	
	function getDate(timestamp){
		const date = new Date(0)
		date.setUTCSeconds(timestamp)

		return date.getDate() + "/" + (date.getMonth() + 1) + "/" + date.getFullYear()
	}

	function getHours(timestamp){
		if (timestamp === null)
			return "-"

		const date = new Date(0)
		date.setUTCSeconds(timestamp)

		return getFormattedTime(date.getHours()) + ":" + getFormattedTime(date.getMinutes()) + ":" + getFormattedTime(date.getSeconds())	
	}

	function changeLed(){
		axios.put("https://tam-arduino-api.vercel.app/led")	

		if(buttonText == "Desligar")
			buttonText = "Ligar"
		else
			buttonText = "Desligar"

	}

	function getFormattedTime(time){
  		return (time <= 9) ? ("0" + time) : time
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
<h2>LED</h2>
<button on:click={changeLed}>{buttonText}</button>

<div>
		<h2>Logs</h2>
		<table>
			<thead>
                <tr>
					<th>Dia</th>
                    <th>Pedido</th>
					<th>Realizado</th>
					<th>Ação</th>
                </tr>
					{#each logData as data}
						<tr>
						<td>{getDate(data.time_started)}</td>
						<td>{getHours(data.time_started)}</td>
						<td>{getHours(data.time_completed)}</td>
						<td>{data.action}</td>
						</tr>
					{/each}

            </thead>

		</table>

	</div>


<style>
	#main{
		display: flex;
		align-items: top;
	}
	
	h1 {
		color: rgb(0, 0, 0);
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
	button{
		border-radius: 500px;
		border: 3px solid #00679A;
		background-color: #0099e6;
		color: white;
		font-size: medium;
		width: 58%;
		height: 50px;
	}
	button:hover {
		opacity: 0.85
	}
</style>







