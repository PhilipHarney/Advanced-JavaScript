1. You can now make your own Pokémon objects, containing all the information you think is important about a Pokémon. However, you might have noticed that it takes a few seconds for the page to load every time it refreshes. To really get an idea of this, change the call to `buildDex` so it's got a larger value for `pokemonCount` passed to it. Try `buildDex(10)` and see how long that takes. It's because the code has to go off and fetch the info from PokeAPI every time the page loads. Wouldn't it be nice to store that information so you didn't have to wait? You can do that! You can use **JavaScript** to store the info and pictures on the user's computer so that once they've downloaded them the first time they don't have to wait for them again! You'll do this using a part of **JavaScript** called `localStorage`.

2. What you want the code to do here is:
  * Check if there are already saved Pokémon and if there are enough of them to match `pokemonCount` (more is fine!)
  * If yes:
    * Great! Use those Pokémon!
  * If no:
    * Fetch the Pokémon info
    * Fetch the Pokémon pictures
    * Make the Pokémon **objects**
    * Save them, so you can use them next time

3. A lot of that looks like the code in `getPokemon` right now. In fact, this is basically what you want `getPokemon` to do: give you Pokemon. It should figure out how to get them and send them back, no matter where it found them. Let's start by wrapping the existing code in an `if` statement that tests for saved Pokémon (we'll worry about the count and saving parts in the next few steps):

  ```JavaScript
  async function getPokemon(pokemonCount){
    if(localStorage.getItem("pokemon") === null) {
      pokemon = []
      await fetchManyPokemon(pokemonCount)
      await fetchPokemonImages()
      await makePokemonList(pokemonCount)
      // save the pokemon list
    }
    else {
      // get the pokemon list
    }
  }
  ```

  The only part of this code that should look new to you is `localStorage.getItem("pokemon") === null`. This is **calling** the `getItem` `function` of `localStorage`, which comes built into **JavaScript**, and asking if it has an item called "pokemon". If it does, it will return that item. If it doesn't, it will return `null`, a special value that basically means "nothing". The three equals signs (`===`), you may remember, are how we check that two things are the same. So, if there is no value for "pokemon" stored in `localStorage` then **JavaScript** will **return** `null` and the test will pass, running the code to `fetch` and create your Pokémon. Otherwise, the `else` block runs… we'd better fill that in!

4. `localStorage` isn't as clever as the rest of **JavaScript**; it can only store text **strings**. Since your Pokemon are complex **objects**, and you have a whole **array** of them, that's a problem. However, you've already seen the solution! **JSON** is JavaScript Object Notation, specifically made to store complex **JavaScript** **objects** as **strings**. Thankfully, it's really easy to turn an **object** into **JSON** and back. You just need to add one line into the `if` statement to save the Pokémon list, like so:

  ```JavaScript
  if(localStorage.getItem("pokemon") === null) {
    pokemon = []
    await fetchManyPokemon(pokemonCount)
    await fetchPokemonImages()
    await makePokemonList(pokemonCount)
    localStorage.setItem("pokemon", JSON.stringify(pokemon))
  }
  ```
  
  This line **sets** the value **returned** by `JSON.stringify(pokemon)` as associated with the **key** `"pokemon"` in `localStorage`. As you might guess, `JSON.stringify()` turns the object passed to it into a **JSON** **string**!

5. Now you need to get those values back, if they already exist, and store them in the `pokemon` **array**, which is where the rest of your code will be looking for them. Notice how filling the same **array** in either situation means that only the `getPokemon` `function` needs to care about where the Pokémon are coming from!
  As you may have guessed, this is another quick call to `localStorage` and `JSON`. This time, it's `localStorage.getItem()` to **get** what you **set** previously and, once you've got it, `JSON.parse()` to turn it back into an **object**.

  ```JavaScript
  else {
    pokemon = JSON.parse(localStorage.getItem("pokemon"))
  }
  ```

6. Finally, you need to add the check for the situation where you have three Pokémon stored, change the call to `getDex(15)`, and still just get three Pokémon back! Right now, the `if` is just checking that there are *any* stored and, if so, jumping to the `else` of just using them. You need to add a check where first it checks for *any* and then for *at least* the right number. What that actually means is checking that the length of the stored **array** is not less than the value of `pokemonCount`. Update the `if` statement to do that like so:

  ```JavaScript
    if(localStorage.getItem("pokemon") === null || JSON.parse(localStorage.getItem("pokemon")).length < pokemonCount) {
  ```
  Remember that `||` (or) means that if the condition on either side is `true` then the whole statement counts as `true`. Likewise, `&&` (and) means that both conditions need to be `true` for the statement to count as `true`.

7. Now watch how fast the page loads, once it's had a chance to save the Pokémon!