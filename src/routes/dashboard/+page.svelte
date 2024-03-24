<script>
  import { authHandlers, authStore } from "../../store/store";
  import { getDoc, doc, setDoc } from "firebase/firestore";
  import { fade } from "svelte/transition";
  import { db } from "../../lib/firebase/firebase";

  let shoppingList = [];
  let inputValue = "";
  let error = false;

  authStore.subscribe((curr) => {
    shoppingList = curr.data.shoppingList || [];
  });

  function addItem() {
    error = false;
    if (!inputValue) {
      error = true;
    }
    shoppingList = [...shoppingList, inputValue];
    inputValue = "";
  }

  function deleteItem(index) {
    let newShoppingList = [...shoppingList].filter((val, i) => {
      return i != index;
    });
    shoppingList = newShoppingList;
  }

  async function saveShoppingList() {
    try {
      const userRef = doc(db, "users", $authStore.user.uid);
      await setDoc(
        userRef,
        {
          shoppingList: shoppingList,
        },
        { merge: true }
      );
    } catch (err) {
      console.log("There was an error saving your shopping list", err);
    }
  }
</script>


{#if !$authStore.loading}
<div class="min-h-screen bg-white">
<div class="flex flex-col max-w-xs mx-auto my-8">
<div class="flex items-center justify-between">
<p class="text-cyan-400 cursor-pointer hover:text-cyan-600" on:click={saveShoppingList}>Save</p>
<p class="text-cyan-400 cursor-pointer hover:text-cyan-600" on:click={authHandlers.logout}>Logout</p>

</div>
<img class="w-48 mx-auto ml-16" src="doggo.png" alt="Shopping Cart Logo">
<input
class="text-center text-xl font-sans text-black bg-[#dce1eb] border-0 p-4 rounded-lg my-2.5"
class:errorBorder={error}
type="text"
bind:value={inputValue}
on:keydown={(e) => e.key === "Enter" && addItem()}
id="input-field"
placeholder="Bread"
/>
<button class="text-center text-xl font-sans text-white bg-blue-600 border-0 p-4 mb-3 rounded-lg hover:bg-blue-900 cursor-pointer" on:click={addItem}>Add</button>
<ul class="flex flex-wrap gap-2.5 list-none p-0" id="shopping-list">
{#each shoppingList as item, index (index)}
<li
class="bg-blue-100 text-black text-xl text-center flex-grow rounded-md p-4 shadow-md hover:bg-blue-200 hover:cursor-pointer"
transition:fade={{ duration: 500 }}
on:click={() => deleteItem(index)}
>
{item}
</li>
{/each}
</ul>
</div>
</div>
{/if}
  
  <style>
    input[type="text"]:focus {
      outline: 3px solid rgb(147 197 253);
    }
    .errorBorder { 
     border: 2px solid red; } 
  </style>  
