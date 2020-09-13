<p id="demo"></p>

<script>
	import{ fade } from 'svelte/transition';
	import{ fly } from 'svelte/transition';
	import { onMount } from "svelte";

	let nuevo = '';
	let c = 4;
	var d = new Date();
	let url = 'https://filebin.net/6bgi34xmfac01e7a/tareas.json?t=dfoako1l';
	let data = [];

	onMount(async function() {
        const response = await fetch(url);
        data = await response.json();
		console.log(data);
    });
	

	let todos = [
		{
			id: 1,
			completed: false,
			title: 'Para añadir una tarea, escribirla y luego apretar enter',
			editing: false,
		},
		{
			id: 2,
			completed: false,
			title: 'Para editar una tarea, hacerle doble click',
			editing: false,
		},
		{
			id: 3,
			completed: false,
			title: 'Para borrarla, presionar el boton de la derecha o marcarlo y presionar borrar completados',
			editing: false,
		}
	]

	function nuevotodo(event){
		if(event.which === 13) {
			todos.push({
				id: c,
				completed: false,
				title: nuevo,
				editing: false,
			})
			todos = todos;
			nuevo = '';
			c++;
		}
	}

	function tarearandom(){
		fetch('./tareas.json')
			.then(res => res.json())
			.then(data => {
				console.log(data)
				const html = data.map(quotes => {
					return quotes.quote
				})
				todos.push({
					id: c,
					completed: false,
					title: html[Math.floor((Math.random() * 5))],
					editing: false,
				})
			});
		todos = todos;
		c++;
	}

	function borrartodo(id){
		todos = todos.filter(todo => todo.id !== id);
	}

	function seleccionartodos(){
		todos.forEach(todo => todo.completed = event.target.checked);
		todos = todos;
	}

	function borrarcompletados(){
		todos = todos.filter(todo => !todo.completed)
	}

	function editartodo(todo){
		todo.editing = true;
		todos = todos;
	}

	function terminado(todo) {
		todo.editing = false;
		todos = todos;
	}

	function terminado2(todo, event) {
		if(event.which === 13){
			todo.editing = false;
			todos = todos;
		}
	}
</script>

<main>

</main>

<style lang="scss">
	.container {
		max-width : 600px;
		margin: 0 auto;
		overflow: hidden;
	}

	.logo {
		display : block;
		margin: 20 px auto;
		height: 75px;
		text-align: center;
	}

	.todo-input {
	  width: 100%;
	  padding: 10px 18px;
	  font-size: 18px;
	  margin-bottom: 16px;
	}
	.todo-item {
	  margin-bottom: 12px;
	  display: flex;
	  align-items: center;
	  justify-content: space-between;
	  animation-duration: 0.3s;
	}
	.remove-item {
	  cursor: pointer;
	  margin-left: 14px;
	  &:hover {
		color: black;
	  }
	}
	.todo-item-left { // later
	  display: flex;
	  align-items: center;
	}
	
	.todo-item-label {
	  padding: 10px;
	  border: 1px solid white;
	  margin-left: 12px;
	}
	.todo-item-edit {
	  font-size: 24px;
	  color: #2c3e50;
	  margin-left: 12px;
	  width: 100%;
	  padding: 10px;
	  border: 1px solid #ccc; //override defaults
	  font-family: 'Avenir', Helvetica, Arial, sans-serif;
	  &:focus {
		outline: none;
	  }
	}
	.completed {
	  text-decoration: line-through;
	  color: grey;
	}
	.extra-container {
	  display: flex;
	  align-items: center;
	  justify-content: space-between;
	  font-size: 16px;
	  border-top: 1px solid lightgrey;
	  padding-top: 14px;
	  margin-bottom: 14px;
	}
	button {
	  font-size: 14px;
	  background-color: white;
	  appearance: none;
	  &:hover {
		background: lightgreen;
	  }
	  &:focus {
		outline: none;
	  }
	}
	.clearbtn	{
	  	font-size: 14px;
	  	background-color: white;
	  	appearance: none;
	  	&:hover {
			background: red;
	  	}
	 	 &:focus {
			outline: none;
	 	}
	}
	.active {
	  background: lightgreen;
	}
  </style>
  
  	<div class="container">
		<img src="https://miro.medium.com/max/1400/1*D4Q_5erHUdm8zXNaxPsEGQ.png" alt="svelte logo" class="logo" >
		<input type="text" class="todo-input" placeholder="Añadir tarea" bind:value={nuevo} on:keydown={nuevotodo}>

		{#each todos as todo}
			<div class="todo-item">
				<div class="todo-item-left" transition:fly="{{ y: 35, duration: 357 }}">
					<input type="checkbox" bind:checked={todo.completed}>
					{#if !todo.editing}
						<div class="todo-item-label" class:completed={todo.completed} on:dblclick={editartodo(todo)}>{todo.title}</div>
					{:else}
						<input class="todo-item-edit" bind:value={todo.title} type="text" on:blur={terminado(todo)} on:keydown={terminado2(todo,event)} autofocus>
					{/if}
				</div>
				<div class="remove-item" on:click={borrartodo(todo.id)}>
					&times;
				</div>
			</div>
		{/each}

		<div class="extra-container">
			<div><label><input type="checkbox" on:change={seleccionartodos}>Marcar todo</label></div>
		</div>
  
		<div class="extra-container">
			<div>
				<button class="clearbtn" on:click={borrarcompletados}>Borrar completados</button>
			</div>
			<div>
				<button on:click={tarearandom}>Tarea random</button>
			</div>
		</div>
  </div>