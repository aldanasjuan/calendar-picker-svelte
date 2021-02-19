# calendar-picker-svelte

Calendar Picker with no dependencies. Download the .svelte file or copy-paste it into your component.

## Usage ##
```svelte
<script>
  import Picker from './picker.svelte'
  let currentDate = new Date() // or undefined or anything else.
  let dateWithEvent
</script>

<Picker bind:value={currentDate}>
  <svg slot="icon">
    ...svg stuff
  </svg>
</Picker>

<Picker on:change={e => dateWithEvent = e.detail}/>

```

## Props ##
### init
By default it will start the value with new Date(), meaning right now. If you pass init={false} it will start at undefined or if a value is provided it will start there.
It will accept anything that new Date() accepts to construct a date.
```svelte
<Picker init={false} value={1613756268477}/>

<Picker init={false} value={"19 feb 1976"}/>
```
### locales
This component was meant to be used in spanish but as you may see in the code, you can pass **shortMonths**, **days** and **months** to change the labels. 
Days start at monday. All three props are an array of strings with the appropiate length like 12 months and 7 days. Not much error checking there so I don't know what happens if you pass a longer or shorter array. Feel free to try!
```svelte
<Picker shortMonths={["Jan", "Feb", "Mar"]} days={["M", "T", "W", "T", "F", "S", "S"]} /
```

### icon
The only slot available is the icon slot. You can put whatever in there. Clicking it will trigger the calendar to open. 
```svelte
<Picker>
  <img slot="icon" src="some.svg" style="width: 12px; height: 12px;"/>
</Picker>

<Picker>
  <i slot="icon" class="material-icons-something" youget="the-point"/>
</Picker>
```
### missing stuff
At the moment you can't disabled future or past dates. Feel free to change the code if you need that. There are no plans to include that but maybe if there's interest.
