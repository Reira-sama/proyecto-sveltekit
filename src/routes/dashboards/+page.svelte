<script>
    import { db } from "../../lib/firebase/firebase";
    import { getDoc,doc, setDoc } from 'firebase/firestore'
    import { authHandlers, authStore } from "../../store/store";
    import TodoItem from "../../components/TodoItem.svelte";

    let todoList=[];
    let currTodo="";
    let error=false;

    authStore.subscribe((curr)=>{
        todoList =curr.data.todos;
    });

    function addTodo(){
        error=false;
        if(!currTodo){
            error=true;
        }
        todoList=[...todoList, currTodo];
        currTodo="";
    }

    function editTodo(index){
        console.log(index);
        let newTodoList= todoList.filter((val, i)=>{
            console.log(i, index, i!==index);
            return i!=index;
        });
        currTodo=todoList[index];
        todoList=newTodoList;
    }
    function removeTodo(index){
        console.log(index);
        let newTodoList= todoList.filter((val, i)=>{
            console.log(i, index, i!==index);
            return i!=index;
        });
        
        todoList=newTodoList;
    }
    async function saveTodos(){
        try{
            const userRef = doc(db,"users", $authStore.user.uid);  
            await setDoc(
                userRef,
        {
            
            todos:todoList,
     
        },
        {merge:true}
        );
              }
    catch(err){
        console.log("there was an erro saving your information")
    }
}
</script>

{#if !$authStore.loading}
    

<div class="mainContainer">
    <div class="headerContainer">
        <h1>Todo List</h1>
        <div class="headerBtns">
        <button on:click={saveTodos}><i class="fa-regular fa-floppy-disk"></i><p>Save</p></button>
        <button on:click={authHandlers.logout}><i class="fa-solid fa-right-from-bracket"></i><p>Logout</p></button>
    </div>
    </div>

    <main>
        {#if todoList.length ===0}
        <p>Yoy have nothing to do!</p>
        {/if}
        {#each todoList as todo, index}
        <TodoItem todo={todo} index={index} removeTodo={removeTodo} editTodo={editTodo}/>
        {/each}
    </main>

    <div class={"enterTodo"+ (error ?'errorBorder':"")}>
        <input bind:value={currTodo} type="text" placeholder="Enter todo"/>
        <button on:click={addTodo}>
            ADD
        </button>
    </div>     
  
</div>
{/if}
<style>
    .mainContainer{
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        gap:24px;
        padding: 24px;
        width: 100%;
        max-width: 1000px;
        margin:0 auto;
    }
    .headerContainer{
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
    .headerBtns{
        display: flex;
        align-items: center;
        gap: 14xp;
    }
    .headerContainer button{
        background: #6408b5;
        color: white;
        padding: 10px 18px;
        border: none;
        border-radius: 4px;
        font-weight: 700;
        display: flex;
        align-items: center;
        gap: 10px;
        cursor: pointer;
    }
    .headerContainer button:hover{
        opacity: 0.7;
    }
    .main{
        display: flex;
        flex-direction: column;
        gap:8px;
        flex: 1;
    }
    
    
    .enterTodo{
        display: flex;
        align-items: stretch;
        border: 1px solid #3619d5;
        border-radius: 5px;
        overflow: hidden;
    }
    .errorBorder{
        border-color: coral !important;
    }

    .enterTodo input{
        background: transparent;
        border: none;
        padding: 14px;
        color: white;
        flex: 1;
    }

    .enterTodo input:focus{
        outline: none;
    }

    .enterTodo button{
        padding: 0 28px;
        background: #6408b5;
        border: none;
        color: rgb(194, 36, 170);
        font-weight: 600;
        cursor: pointer;
    }
    .enterTodo button:hover{
        background: transparent;
    }
</style>