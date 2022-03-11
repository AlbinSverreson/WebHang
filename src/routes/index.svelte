<script>
  import wordlist from "./words.js"
  var letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', 'å', 'ä', 'ö']
  var win = false
  var view_words = false
  var tbx = null
	var guess = ""
	var info = ""
  let height = 0
  let y = 0
	var words = []
	var guesses = new Set([])
	var wrong = 0
	var length = 0
	var gamestr_array = new Array()
  var guesses_str = ""
	$: lives = 11 - wrong
	$: alive = lives != 0
	
	function start_game(){
    win = false
		guess = ""
		wrong = 0
		guesses = new Set([])
    guesses_str = ""
		info = "Gissa på en bokstav!"
		length = Math.floor(Math.random() * (6 - 3) ) + 3;
		gamestr_array = new Array(length).fill("_")
		words = wordlist.filter(w => w.length == length)
	}

  function restart(){
    start_game()
  }
	
	function most_common_place(letter){
		var common_place =0
		var highest = 0
    var i
		for(i=0; i<length; i++){
			var nbr_at_i = words.filter(w => w.charAt(i)==letter).length
			if(nbr_at_i>highest){
				highest = nbr_at_i
				common_place = i
			}
		}
    return common_place
	}

	function make_guess() {
    tbx.focus()
    guess = guess.toLowerCase()
		if(guess.length == 1&&!(guesses.has(guess))&&letters.includes(guess)){
      guesses.add(guess)
      guesses_str = Array.from(guesses).join()

      var common_place = most_common_place(guess)
      var removeguess_len = words.filter(w => !(w.includes(guess))).length

      if(removeguess_len<=words.length/3 || removeguess_len==0){
        words = words.filter(w => w.charAt(common_place)==guess)
        gamestr_array[common_place]=guess
        info = "Rätt! Gissa igen!"
        if(words.length == 1 && gamestr_array.join("") == words[0]){
          win = true
          info = "Du vann! Ordet var " + words[0] + "<br><br>"+"Du gissade på: "+ guesses_str + "<br><br>"
        }
      }
      else{
        wrong++
        info = "Fel! Gissa igen!"
        words = words.filter(w => !(w.includes(guess)))
        if(wrong == 11){
          info = "Du förlorade!<br>Ordet var " + words[Math.floor(Math.random()*words.length)].toString() + ".<br>Du gissade på " + guesses_str
        }
      }
    }
    else if(guesses.has(guess)){
      info = "Du har redan gissat på " + guess.toString()+"!"
    }
    else{
      info = "Du måste gissa på en bokstav!"
    }
		guess = ""
	}

  function handle_key(event){
    if(event.keyCode === 13){
      make_guess()
    } 
  }
  
  function toggle_words(){
    view_words = !view_words
  }
start_game()
</script>
<svelte:window bind:outerHeight={height} bind:scrollY={y}/>
<svelte:head>
<title>Sverreson</title>
</svelte:head>
<main>
{#if view_words}
   <div id="word_list">
     <br>
     <h3> Här är alla ord jag kan! </h3>
     <button id="tillbaks" on:click={toggle_words}>Tillbaks</button>
     <p>
      {wordlist.join(", ")}
     </p>
     <h4> Många va? </h4>
    <br>
  </div>
{:else}
  <img
    alt="Här ska det sitta en glytt och fiska men något har gått sönder.."
    id="fishing"
    src="fishing.png"
  >
  <div id="box">
    <p>
	    {@html info}
    </p>
    <p>
      {#if alive && !win}
	      Du har {lives} liv kvar.
        <br>
        {#if guesses.size>0}
	        Hittills har du gissat på: {guesses_str}
        {:else}
          Du har inte gissat på något än.
        {/if}
        <br>
        {gamestr_array.join(" ")}
      {/if}
    </p>
    {#if alive && !win}
      <input on:keyup={handle_key} bind:value={guess} bind:this={tbx}>
      <br>
      <button on:click={make_guess}>Gissa</button>
    {:else}
      <button on:click={restart}>Börja om</button>
      <br>
    {/if}
<br>
<svg height="80" width="100">
  {#if wrong>0}
    <circle cx="50" cy="80" r="30" 
    stroke="white" stroke-width="2" fill="#e87696"
    />
  {/if}
  {#if wrong>1}
    <line x1="50" y1="50" x2="50" y2="1" stroke="white" stroke-width="2"/>
  {/if}
  {#if wrong>2}
    <line x1="50" y1="2" x2="85" y2="2" stroke="white" stroke-width="2"/>
  {/if}
  {#if wrong>3}
    <line x1="50" y1="17" x2="65" y2="2" stroke="white" stroke-width="2"/>
  {/if}
  {#if wrong>4}
    <line x1="85" y1="17" x2="85" y2="2" stroke="white" stroke-width="2"/>
  {/if}
  {#if wrong>5}
    <circle cx="85" cy="20" r="5" 
    stroke="white" stroke-width="2" fill="#e87696"
    />
  {/if}
  {#if wrong>6}
    <line x1="85" y1="25" x2="85" y2="40" stroke="white" stroke-width="2"/>
  {/if}
  {#if wrong>7}
    <line x1="85" y1="26" x2="75" y2="30" stroke="white" stroke-width="2"/>
  {/if}
  {#if wrong>8}
    <line x1="85" y1="26" x2="95" y2="30" stroke="white" stroke-width="2"/>
  {/if}
  {#if wrong>9}
    <line x1="85" y1="40" x2="92" y2="50" 
    stroke="white" stroke-width="2"
    />
  {/if}
  {#if wrong>10}
    <line x1="85" y1="40" x2="78" y2="50" 
    stroke="white" stroke-width="2"
    />
  {/if}
</svg>
  </div>
    <p id="bottom_text" on:click={toggle_words} 
      class:scrolling="{height+y>653}"
      class:static="{height+y<=653}">
        Vad är det här för jävla ord egentligen??
  </p>
{/if}
</main>

<style>
:global(body){
  background-color: #ffffff
}
.scrolling{
  position: fixed;
  right: 0;
  bottom: 0;
}
.static{
  position: absolute;
  float: right;
  right: 0;
  top: 613px;
}
#bottom_text{
  font-size: 13px;
  text-decoration: underline;
  color: #ea5d85;
  cursor: pointer;
}
#box{
  position: absolute;
  padding-top: 80px;
  background-color: #e87696;
  color: #ffffff;
  left: 50%;
  margin-left: -125px;
  margin-top: 200px;
  text-align: center;
  height: 325px;
  width: 250px;
}
#fishing{
  position: absolute;
  margin-top: 10px;
  left: 50%;
  margin-left: -305px;
  width: 295px;
  height: 824px;
  z-index: 10;
}
#word_list{
  margin: auto;
  width: 80%;
  text-align: center;
  background-color: #f280a1;
  color: #ffffff;
  word-wrap: break-word;
}
input{
  width: 55px;
  text-align: center;
  color: #ffffff;
  background-color: #f8a9c0;
}
input, button{
  font: 400 13.3333px Arial;
  height: 32px;
  border: 1px solid #ccc;
  margin: 0 0 0.5em 0;
  box-sizing: border-box;
  border-radius: 2px;
}
input, button{
	font-family: inherit;
	font-size: inherit;
	padding: 0.4em;
	margin: 0 0 0.5em 0;
	box-sizing: border-box;
	border: 1px solid #ccc;
	border-radius: 2px;
}
button{
  min-width: 55px;
  text-align: center;
  background-color: #ea5d85;
  color: #ffffff;
  cursor: pointer;
}
#tillbaks{
  min-width: 70px;
  text-align: center;
  background-color: #ea5d85;
  color: #ffffff;
  cursor: pointer;
}
</style>
