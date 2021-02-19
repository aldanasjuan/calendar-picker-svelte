<script>
	import {createEventDispatcher} from 'svelte'
	const dispatch = createEventDispatcher()
	export let value = undefined
	export let valueString = ''
	export let iconStyle = '', dateStyle = ""
	export let init = true
	export let placeholder = "mm/dd/yyyy"
	let fade = false
	let val = undefined, main = undefined
	let monthPicker = false
	let mainStyle = undefined
	if(init === true || value !== undefined){
		try {
			val = new Date(value)
		} catch (err) {
			console.log(err)
			val = new Date()
		}
		if ((val.getTime() >= 0 || val.getTime() <= 0) == false) {
			val = new Date()
		}
	}
	let m = val ? val.getMonth() : new Date().getMonth()
	let d = val ? val.getDate() : new Date().getDate()
	let y = val ? val.getFullYear() : new Date().getFullYear()
	$: value ? valueString = getStringDate(value, false) : ''
	$: val ? value = val : ''
	$: value ? dispatch("change", value) : ""
	let id = (Math.random() * Math.random()).toString(36)
	let open = false
	$: {
		m = val ? val.getMonth() : new Date().getMonth()
		d = val ? val.getDate() : new Date().getDate()
		y = val ? val.getFullYear() : new Date().getFullYear()
	}
	export let shortMonths = [
		'Ene.',
		'Feb.',
		'Mar.',
		'Abr.',
		'May.',
		'Jun.',
		'Jul.',
		'Ago.',
		'Sept.',
		'Oct.',
		'Nov.',
		'Dic.',
	]
	export let days = ['L', 'M', 'X', 'J', 'V', 'S', 'D']
	export let months = [
		'Enero',
		'Febrero',
		'Marzo',
		'Abril',
		'Mayo',
		'Junio',
		'Julio',
		'Agosto',
		'Septiembre',
		'Octubre',
		'Noviembre',
		'Diciembre',
	]
	let daysInMonth = (month, year) => {
		return new Date(year, month + 1, 0).getDate()
	}
	let offset = (month, year) => {
		return new Date(year, month, 0).getDay()
	}
	$: prevOffset = daysInMonth(m - 1, y) - offset(m, y)
	$: tail = Math.abs(((daysInMonth(m, y) + offset(m, y)) % 7) - 7)
	function selection(day) {
		val = new Date(y, m, day)
		open = false
	}
	function substract() {
		if (monthPicker == false) {
			fade = true
			setTimeout(() => {
				fade = false
			}, 100)
			if (m == 0) {
				m = 11
				y--
			} else {
				m--
			}
		} else {
			y--
		}
	}
	function add() {
		if (monthPicker == false) {
			fade = true
			setTimeout(() => {
				fade = false
			}, 100)
			if (m == 11) {
				m = 0
				y++
			} else {
				m++
			}
		} else {
			y++
		}
	}
	function getDate(date, time = true) {
		let d = new Date(date)
		if ((d.getTime() >= 0 || d.getTime() <= 0) == false) return '-'
		if (time == true) {
			return `${d.getDate() < 10 ? '0' : ''}${d.getDate()}/${
				d.getMonth() + 1 < 10 ? '0' : ''
			}${d.getMonth() + 1}/${d.getFullYear()} - ${
				d.getHours() > 9 ? d.getHours() : `0${d.getHours()}`
			}:${d.getMinutes() > 9 ? d.getMinutes() : `0${d.getMinutes()}`}`
		} else {
			return `${d.getDate() < 10 ? '0' : ''}${d.getDate()}/${
				d.getMonth() + 1 < 10 ? '0' : ''
			}${d.getMonth() + 1}/${d.getFullYear()}`
		}
	}
	function getStringDate(date) {
		let d = new Date(date)
		if ((d.getTime() >= 0 || d.getTime() <= 0) == false) return '-'

		return `${d.getMonth() + 1 < 10 ? '0' : ''}${d.getMonth() + 1}/${d.getDate() < 10 ? '0' : ''}${d.getDate()}/${d.getFullYear()}`
	}
	function close() {
		open = false
	}
	function scale() {
		return {
			easing: (t) => (t < 0.5 ? 2 * t * t : -1 + (4 - 2 * t) * t),
			duration: 200,
			css: (e) => `
				transform-origin: center;
				transform: scale(${e})
				`,		
		}
	}
	function reset() {
		m = val ? val.getMonth() : new Date().getMonth()
		d = val ? val.getDate() : new Date().getDate()
		y = val ? val.getFullYear() : new Date().getFullYear()
		open = false
	}
	function check(e) {
		let val = parseInt(e.target.innerText)
		if (Number.isNaN(val) || val < 1900 || val > 3000) {
			e.target.innerText = y
		} else {
			y = val
		}
	}
	function handleKey(e){
		if([9, 27].includes(e.keyCode)){
			e.preventDefault()
			e.target.blur()
		}
		if((e.keyCode >= 48 && e.keyCode <= 57 ) || (e.keyCode >= 96 && e.keyCode <= 105 ) || [37,39,8,46].includes(e.keyCode) ){
		return
		}
		e.preventDefault()
	}
	function position(node){
		document.body.append(node)
		let wh = window.innerHeight
		let ww = window.innerWidth
		
		let dropdownRect = node.getBoundingClientRect()
		let mainRect = main.getBoundingClientRect()

		let mainTop = mainRect.top + (mainRect.height / 2)
		let mainLeft = mainRect.left + (mainRect.width / 2)
		let dropdownTop = mainTop - (dropdownRect.height / 2)
		let dropdownLeft = mainLeft - (dropdownRect.width / 2)
		dropdownLeft < 0 ? dropdownLeft = 10 : ""
		dropdownLeft + dropdownRect.width > ww ? dropdownLeft = ww - dropdownRect.width - 10 : ""
		dropdownTop < 0 ? dropdownTop = 10 : ""
		dropdownTop + dropdownRect.height > wh ? dropdownTop = wh - dropdownRect.height - 10 : ""
		node.style.left = `${dropdownLeft}px`
		node.style.top = `${dropdownTop}px`
	}
	function hoist(node){
		document.body.append(node)
	}
</script>

<style>
	.calendar {
		min-width: 300px;
		top: 0;
		position: absolute;
		background: white;
		z-index: 999;
		display: grid;
		grid-template-columns: repeat(7, 1fr);
		grid-gap: 10px;
		place-items: center;
		box-shadow: 0 0 4px #ccc;
		padding: 20px;
		border-radius: 25px;
	}
	.close{
		width: 100%;
		grid-column: 1 / -1;
		display: flex;
		justify-content: flex-end;
	}
	.day {
		border-radius: 50px;
		width: 36px;
		height: 36px;
		display: flex;
		margin: 0;
		color: #555;
    	font-weight: 500;
		/* 		border: solid 1px #aaa; */
		justify-content: center;
		align-items: center;
		user-select: none;
		cursor: pointer;
		/* transition: ease all 0.2s; */
		font-size: 12px;
	}
	.day:hover {
		/* 		box-shadow: 0px 0px 5px #ddd; */
		background: #eee;
	}
	.day:active {
		background-color: #fff;
		box-shadow: 0px 0px 0 #fff;
	}
	.month {
		font-size: 20px;
		margin: 3px;
		grid-column: 1 / -1;
		display: flex;
		align-items: center;
		justify-content: space-between;
		width: 100%;
	}
	.disabled {
		color: #ccc;
		/* 		border: solid 1px #ccc; */
	}
	.weekday {
		user-select: none;
		font-weight: bold;
		color: #333;
		font-size: 14px;
	}
	.flip {
		transform: rotate(180deg);
	}
	.month-text,
	svg,
	.button {
		user-select: none;
		cursor: pointer;
	}
	.month-text{
		color: #555;
    	font-weight: 500;
	}
	.button {
		position: relative	;
		top: 0;
		/* width: 90%; */
		padding: 0;
		font-size: 12px;
		font-weight: 500;
		display: inline-flex;
		align-items: center;
		justify-content: space-between;
		border-radius: 2px;
	}
	.selected {
		background-color: #e0f7f7;
	}
	.selected:hover {
		background-color: #e0f7f7 !important;
	}
	main {
		width: auto;
		box-sizing: border-box;
		display: block;
		position: relative;
	}
	.month-picker {
		width: 100%;
		grid-column: 1 / -1;
		display: grid;
		grid-gap: 5px;
		grid-template-columns: repeat(3, 1fr);
		grid-template-rows: repeat(4, 1fr);
	}
	.month-element {
		width: 100%;
		height: 45px;
		display: flex;
		align-items: center;
		justify-content: center;
		/* 		border: solid 1px #ddd; */
		cursor: pointer;
		user-select: none;
		/* transition: all 0.2s; */
	}
	.month-element:hover {
		background-color: #f0f0f0;
		/* 		box-shadow: 0px 0px 5px #ddd; */
	}
	.month-element:active {
		background-color: #fff;
		box-shadow: 0px 0px 0 #fff;
	}
	.fade {
		opacity: 0.4;
		filter: blur(1px);
	}
	.year-text {
		cursor: text;
		outline: none;
	}
	.date{
		padding: 15px;
		background: #f7f7f7;
		border-radius: 5px;
	}
	.date::first-letter{
		text-transform: lowercase !important;
	}
</style>

<svelte:window
	on:click={(e) => {
		!e.target.getAttribute('belongs') || e.target.getAttribute('belongs') != id ? reset() : ''
	}} />
	<main bind:this={main} belongs={id} style={mainStyle}>
	<div belongs={id} class="button" on:click={() => (open = !open)}>

		<span on:click={() => (open = !open)}>
			<slot name="icon"></slot>
		</span>
		
		<span class="date" style={dateStyle} belongs={id}>{val ? getDate(val, false) : placeholder}</span>

		<!-- 		{d} de {months[m]} del {y} -->
	</div>
	{#if open == true}
		<div belongs={id} use:position class="calendar" transition:scale>
			<!-- Close button -->
			<div class="close" belongs={id}>
				<svg xmlns="http://www.w3.org/2000/svg" height="28" viewBox="0 0 24 24" width="28"><path d="M0 0h24v24H0V0z" fill="none"/><path fill="#333" d="M18.3 5.71c-.39-.39-1.02-.39-1.41 0L12 10.59 7.11 5.7c-.39-.39-1.02-.39-1.41 0-.39.39-.39 1.02 0 1.41L10.59 12 5.7 16.89c-.39.39-.39 1.02 0 1.41.39.39 1.02.39 1.41 0L12 13.41l4.89 4.89c.39.39 1.02.39 1.41 0 .39-.39.39-1.02 0-1.41L13.41 12l4.89-4.89c.38-.38.38-1.02 0-1.4z"/></svg>
			</div>
			
			<h4 belongs={id} class="month">
				<svg
					belongs={id}
					on:click={substract}
					class="flip"
					xmlns="http://www.w3.org/2000/svg"
					width="28"
					height="28"
					viewBox="0 0 512 512">
					<polyline
						belongs={id}
						points="262.62 336 342 256 262.62 176"
						style="fill:none;stroke:#333;stroke-linecap:round;stroke-linejoin:round;stroke-width:40px" />
					<line
						belongs={id}
						x1="330.97"
						y1="256"
						x2="170"
						y2="256"
						style="fill:none;stroke:#333;stroke-linecap:round;stroke-linejoin:round;stroke-width:40px" />
					<path
						belongs={id}
						d="M256,448c106,0,192-86,192-192S362,64,256,64,64,150,64,256,150,448,256,448Z"
						style="fill:none;stroke:#333;stroke-miterlimit:10;stroke-width:40px" />
				</svg>
				<span belongs={id} class="month-text">
					<span
						class="month-text"
						belongs={id}
						on:click={(e) => (monthPicker = !monthPicker)}>
						{months[m]}
					</span>
					-
					<span
						class="year-text"
						on:keydown={handleKey}
						contenteditable
						belongs={id}
						on:blur={check}>
						{y}
					</span>
				</span>
				<svg
					belongs={id}
					on:click={add}
					xmlns="http://www.w3.org/2000/svg"
					width="28"
					height="28"
					viewBox="0 0 512 512">
					<polyline
						belongs={id}
						points="262.62 336 342 256 262.62 176"
						style="fill:none;stroke:#333;stroke-linecap:round;stroke-linejoin:round;stroke-width:40px" />
					<line
						belongs={id}
						x1="330.97"
						y1="256"
						x2="170"
						y2="256"
						style="fill:none;stroke:#333;stroke-linecap:round;stroke-linejoin:round;stroke-width:40px" />
					<path
						belongs={id}
						d="M256,448c106,0,192-86,192-192S362,64,256,64,64,150,64,256,150,448,256,448Z"
						style="fill:none;stroke:#333;stroke-miterlimit:10;stroke-width:40px" />
				</svg>
			</h4>
			{#if monthPicker == false}
				{#each days as wd}
					<span belongs={id} class="weekday">{wd}</span>
				{/each}
				{#each Array(offset(m, y))
					.fill()
					.map((e, i) => prevOffset + i) as day, i}
					<span class:fade belongs={id} class="day disabled">{day + 1}</span>
				{/each}
				{#each Array(daysInMonth(m, y)) as _, day}
					<span
						class:fade
						belongs={id}
						class="day {day + 1 == d ? 'selected' : ''}"
						on:click={(e) => selection(day + 1)}>
						{day + 1}
					</span>
				{/each}
				{#each Array(tail) as _, day}
					<span class:fade belongs={id} class="day disabled">{day + 1}</span>
				{/each}
			{:else}
				<div belongs={id} class="month-picker">
					{#each shortMonths as month, i}
						<span
							on:click={(e) => {
								m = i
								monthPicker = false
							}}
							belongs={id}
							class="month-element {i == m ? 'selected' : ''}">
							{month}
						</span>
					{/each}
				</div>
			{/if}
			
		</div>
	{/if}
</main>
