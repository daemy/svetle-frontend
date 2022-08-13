<script>
	export let name;
	export let number;

	$: upperCaseName = name.toUpperCase();

	$: console.log(name);

	function incrementNumber() {
		number += 1
	}

	function decrementNumber() {
		if (number>1) {
			number -= 1
		}
	}

	function changeName() {
		name = "b";
	}
</script>

<style>
	h1 {
		color: purple;
	}
</style>

<h1>{upperCaseName}, counter is {number}!</h1>
<button on:click="{incrementNumber}">Increment</button>
<button on:click="{decrementNumber}">Decrement</button>
<button on:click="{changeName}">Change name</button>
