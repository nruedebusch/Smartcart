<script>
  import { authHandlers } from "../store/store";
  let email = "";
  let password = "";
  let confirmPass = "";
  let error = false;
  let register = false;
  let authenticating = false;

  async function handleAuthenticate() {
    if (authenticating) {
      return;
    }
    if (!email || !password || (register && !confirmPass)) {
      error = true;
      return;
    }
    authenticating = true;

    try {
      if (!register) {
        await authHandlers.login(email, password);
      } else {
        await authHandlers.signup(email, password);
      }
    } catch (err) {
      console.log("There was an auth error", err);
      error = true;
      authenticating = false;
    }
  }

  function handleRegister() {
    register = !register;
  }
</script>

<div class="flex flex-col items-center justify-center flex-1 p-6"> 
  <form class="flex flex-col gap-4 w-full max-w-md mx-auto"> 
    <h1 class="text-center text-5xl">{register ? "Register" : "Login"}</h1> {#if error} 
    <p class="text-red-400 text-sm text-center">The information you have entered is not correct</p> 
    {/if} 
    <label class="relative border border-blue-900 rounded-md focus-within:border-blue-600"> 
      <p class={email ? " above " : " center"}>Email</p> 
      <input bind:value={email} type="email" placeholder="Email" class="w-full bg-transparent text-white p-3.5 border-none" />
    </label> 
    <label class="relative border border-blue-900 rounded-md focus-within:border-blue-600"> 
      <p class={password ? " above " : " center"}>Password</p> 
      <input on:keydown={(e) => e.key === "Enter" && handleAuthenticate()} bind:value={password} type="password" placeholder="Password" class="w-full bg-transparent text-white p-3.5 border-none" /> 
    </label> {#if register} 
    <label class="relative border border-blue-900 rounded-md focus-within:border-blue-600"> 
      <p class={confirmPass ? " above " : " center"}>Confirm Password</p>
       <input on:keydown={(e) => e.key === "Enter" && handleAuthenticate()} bind:value={confirmPass} type="password" placeholder="Confirm Password" class="w-full bg-transparent text-white p-3.5 border-none" /> 
    </label> 
    {/if}

  <button on:click={handleAuthenticate} type="button" class="bg-blue-900 cursor-pointer text-white border-none py-3.5 rounded text-base grid place-items-center hover:bg-blue-600">
    {#if authenticating}
      <i class="fa-solid fa-spinner loadingSpinner" />
    {:else}
      Submit
    {/if}
  </button>
</form> 
<div class="options overflow-hidden py-3.5 w-full max-w-md mx-auto"> 
  <p>Or</p>
   {#if register} 
  <div class="flex items-center gap-2 justify-center"> <p>Already have an account?</p> 
    <p on:click={handleRegister} on:keydown={() => {}} class="text-cyan-400 cursor-pointer">Login</p> 
  </div>
   {:else} 
   <div class="flex items-center gap-2 justify-center"> 
    <p>Don't have an account?</p> <p on:click={handleRegister} on:keydown={() => {}} class="text-cyan-400 cursor-pointer">Register</p> 
  </div>
   {/if} 
  </div>
 </div> 

<style> 
  .above,
  .center {
    position: absolute;
    transform: translateY(-50%);
    pointer-events: none;
    color: white;
    border-radius: 4px;
    padding: 0 6px;
    font-size: 0.8rem;
  }

  .above {
    top: 0;
    left: 24px;
    background: navy;
    border: 1px solid blue;
    font-size: 0.7rem;
  }

  .center {
    top: 50%;
    left: 6px;
    border: 1px solid transparent;
    opacity: 0;
  }

  input:focus{
    outline: none;
    border: none;
  }

  .options > p {
    position: relative;
    text-align: center;
    width: fit-content;
    margin: 0 auto;
    padding: 0 8px;
  }

  .options > p::after,
  .options > p::before {
    position: absolute;
    content: "";
    top: 50%;
    transform: translateY(-50%);
    width: 100vw;
    height: 1.5px;
    background: white;
  }

  .options > p::after {
    right: 100%;
  }

  .options > p::before {
    left: 100%;
  }

  .loadingSpinner {
    animation: spin 1s linear infinite;
  }

  @keyframes spin {
    from {
      transform: rotate(0deg);
    }
    to {
      transform: rotate(360deg);
    }
  }
</style>