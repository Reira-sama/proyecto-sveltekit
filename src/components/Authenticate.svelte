<script>
    import { authHandlers } from "../store/store";

let email="";
let password="";
let confirmPass="";
let error=false;
let register=false;
let authenticating=false;

async function handleAuthenticate(){
    if(authenticating){
        return;
    }

    if(!email || !password || (register && !confirmPass)){
        error=true;
        return;
    }
    authenticating= true;
    
    try{
        if(!register){
      await  authHandlers.login(email, password);
    }else{
        await authHandlers.signup(email, password);
    }
    }catch(err){
        console.log("there was an auth error", err);
    }
    
}

function handleRegister(){
    register=!register;
}
</script>

<div class="authContainer">
    <form>
        <h1>{register ? 'Register':'Login'}</h1>
        {#if error}
        <p class="error">The Information you have entered is not correct</p>
        {/if}
        <label >
            <p class={email ? 'above' : 'center'}>Email</p>
            <input bind:value={email} type="email" placeholder="Email"/>
        </label>
       
        <label >
            <p class={password ?'above' : 'center'}>Password</p>
            <input  bind:value={password} type="password" placeholder="Passsword"/>
        </label>
       {#if register}
        
      
        <label >
            <p class={confirmPass ? 'above' : 'center'}>Confirm Password</p>
            <input  bind:value={confirmPass} type="password" placeholder="Confirm Password"/>
        </label>
        {/if}
        <button on:click={handleAuthenticate} type="button" class="submitBtn">
        {#if authenticating}
        <i class="fa-solid fa-spinner spin"/>

        {:else}
            Submit
        {/if}
        </button>
    </form>
    <div class="option">
        <p>Or</p>
        {#if register}
        <div>
        <p class="error">Alreday have an account?</p>
        <p on:click={handleRegister} on:keydown={()=>{}}>Login</p>
    </div>
        {:else}
        <div>
            <p>Don't have an account?</p>
            <p on:click={handleRegister} on:keydown={()=>{}}>Register</p>
        </div>
        {/if}
    </div>
</div>


<style>
    .authContainer{
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        flex: 1;
    }
    form{
        display: flex;
        flex-direction: column;
        gap: 8px;
     
    }
        form, .option{
    width: 400px;
        max-width: 100%;
        margin: 0 auto;
}
    form input{
        width: 100%;
    }

    h1{
        text-align: center;
        font-size: 3rem;
    }

    form label{
        position: relative;
        border: 1px solid blueviolet;
        border-radius: 5px;
    }
    form input{
        border: none;
        background: transparent;
        color: white;
        padding: 14px;
    }
    form input:focus{
        border: none;
        outline: none;
    }
    form button{
        background: blueviolet;
        color: white;
        border: none;
        padding: 14px 0;
        border-radius: 5px;
        cursor: pointer;
        font-size: 1rem;
        display: grid;
        place-items: center;
    }
     form button:hover{
        background: rgb(186, 46, 218);
     }
     .above,
      .center{
        position:absolute;
        transform:translateY(-50%);
        pointer-events: none;
        color: white;
        border-radius: 4px;
        padding: 0 6px;
        font-size: 0.8rem;
     }
     .above{
        top:0;
        left: 24px;
        background: rgb(132, 7, 248);
        border: 1px solid rgb(132, 7, 248);
        font-size: 0.7rem;
     }
     .center{
        top:50%;
        left: 6px;
        border: 1px solid transparent;
        opacity: 0;
     }
    .error{
        color: coral;
        font-size: 0.9rem;
        text-align: center;
    }

    .option{
        padding: 14px 0;
        overflow: hidden;
        font-size: 0.9rem;
        display: flex;
        flex-direction: column;
        gap: 4px;
    }

    .option > p{
        position: relative;
        text-align: center;
        width: fit-content;
        margin: 0 auto;
        padding: 0 8px;
    }

    .option > p::after, 
    .option > p::before{
        position: absolute;
        content: "";
        top:50%;
       
        transform: translateY(-50%);
        width: 100vw;
        height: 1.5px;
        background: white;
    }
    .option > p::after{
        right: 100%;
    }
    .option > p::before{
        left: 100%;
    }
    .option div{
        display: flex;
        align-items: center;
        gap: 8px;
        justify-content: center;
    }

    .option div p:last-of-type{
        color: deeppink;
        cursor: pointer;
    }
    .spin{
        animation: spin 2s linear infinite;
    }
    @keyframes spin{
        from{
            transform: rotate(0deg);
        }to{
            transform: rotate(360deg);
        }
    }
</style>