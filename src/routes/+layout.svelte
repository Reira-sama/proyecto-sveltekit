<script>
    import {onMount}from 'svelte'
    import {auth, db} from '../lib/firebase/firebase'
    import { getDoc,doc, setDoc } from 'firebase/firestore'
    import {authStore} from '../store/store'
    

    const nonAuthRoutes=['/','product'];

    onMount(()=>{
        console.log("Mounting")
        const unsubcribe= auth.onAuthStateChanged(async(user)=>
    {
        const currentPath = window.location.pathname;
        if (!user && !nonAuthRoutes.includes(currentPath)){
            window.location.href="/";
            return;
        }
        if(user && currentPath ==="/"){
            window.location.href = "/dashboards";
            return;
        }
        if(!user){
            return;
        }


        let dataToSetToStore;
        const docRfef = doc(db,"users",user.uid);
        const docSnap = await getDoc(docRfef);


        if(!docSnap.exists()){
            const userRef =doc(db,"user", user.uid);
            dataToSetToStore={
                email:user.email,
                    todos:[]
            };
            await setDoc( userRef, dataToSetToStore,{merge:true});
        } else{
            const userData= docSnap.data();
            dataToSetToStore=userData;
        }
        authStore.update((curr)=>{
            return{
                ...curr,
                user,
                data:dataToSetToStore,
                loading:false,
            }
        })
    });
    })
</script>

<div class="mainContainer">
    <slot />
</div>

<style>

.mainContainer{
    min-height: 100vh;
    background-image: linear-gradient(to right, #2a0e4e, #a01ba3);
    color: white;
    position: relative;
    display: flex;
    flex-direction: column;

}
</style>
