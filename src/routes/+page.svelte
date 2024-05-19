<script>
    import Header from "../lib/header.svelte";
    import MostDtc from "../lib/mostDtc.svelte"
    import Device from "../lib/device.svelte";
    import History from "../lib/history.svelte";
    import FormExport from "../lib/formExport.svelte";

    let compoCurrant = 1

   

</script>


<div class="w-full">
    <header class="sticky top-0 z-50">
        <Header></Header>
    </header>
    

    <main class="w-full h-full flex flex-col  items-center p-10 ">
        
        <div class="mainContent w-[70%] min-h-[80%]" >
            <div class="grounp-tap w-[100%]  flex place-items-end gap-1 pl-1">
             <button class={compoCurrant === 1?
            "bg-white text-[#c50000] h-[3rem] w-[12rem] relative flex items-center justify-center  text-[18px] font-bold p-[0.25rem] rounded-t-[0.5rem] border-x-[#468999] border-t-[#468999] border-x-[.1rem] border-t-[.1rem]   after:contents-[''] after:w-full after:h-1  after:bg-white after:absolute after:bottom-[-1px] ":
            "flex items-center justify-center  text-[16px] w-[10rem] p-[0.25rem] rounded-t-[0.5rem] bg-[#468999] text-white"}  
            type="button" 
            on:click={() => {compoCurrant = 1}}>
                
                <img src="https://cdn-icons-png.flaticon.com/128/9356/9356599.png" alt="" class="w-[30px] mr-1">
                Most detected!
            </button>
             <button class={compoCurrant === 2?
            "bg-white text-[#15750f] h-[3rem] w-[12rem] relative flex items-center justify-center  text-[18px] font-bold p-[0.25rem] rounded-t-[0.5rem] border-x-[#468999] border-t-[#468999] border-x-[.1rem] border-t-[.1rem]   after:contents-[''] after:w-full after:h-1  after:bg-white after:absolute after:bottom-[-1px]":"flex items-center justify-center  text-[16px] w-[10rem] font-bold p-[0.25rem] rounded-t-[0.5rem] bg-[#468999] text-white"}  type="button" on:click={() => {compoCurrant = 2}}>
        
                    <!-- svelte-ignore a11y-missing-attribute -->
                    <img src="https://cdn-icons-png.flaticon.com/128/2025/2025523.png" class="w-[30px] mr-1">
                    history
            </button>
             <button class={compoCurrant === 3?
            "bg-white text-[#2b40a0] h-[3rem] w-[12rem] relative flex items-center justify-center  text-[18px] font-bold p-[0.25rem] rounded-t-[0.5rem] border-x-[#468999] border-t-[#468999] border-x-[.1rem] border-t-[.1rem]   after:contents-[''] after:w-full after:h-1  after:bg-white after:absolute after:bottom-[-1px]":"flex items-center justify-center  text-[16px] w-[10rem] font-bold p-[0.25rem] rounded-t-[0.5rem] bg-[#468999] text-white"}  type="button" on:click={() => {compoCurrant = 3}}>
                
                <img src="https://cdn-icons-png.flaticon.com/128/738/738639.png" alt="" class="w-[30px] mr-1">
                All Device
            </button>

            <button class={compoCurrant === 4?
                "bg-white text-[#2b40a0] h-[3rem] w-[12rem] relative flex items-center justify-center  text-[18px] font-bold p-[0.25rem] rounded-t-[0.5rem] border-x-[#468999] border-t-[#468999] border-x-[.1rem] border-t-[.1rem]   after:contents-[''] after:w-full after:h-1  after:bg-white after:absolute after:bottom-[-1px]":"flex items-center justify-center  text-[16px] w-[10rem] font-bold p-[0.25rem] rounded-t-[0.5rem] bg-[#468999] text-white"}  type="button" on:click={() => {compoCurrant = 4}}>
                    
                    <img src="https://cdn-icons-png.flaticon.com/128/738/738639.png" alt="" class="w-[30px] mr-1">
                    export Data
                </button>

            
           
            </div>

            <div class="mainContent h-[45rem] bg-white border-[#468999] border-[1px] p-[0.5rem] overflow-y-auto" id="main">
                {#if compoCurrant === 1 }
                    <MostDtc></MostDtc>
                    

                {:else if compoCurrant === 2}
                    <History />
                {:else if compoCurrant === 3}
                    
                    <Device />
                {:else if compoCurrant === 4}
                    <h1>export</h1>
                    <FormExport />
                {:else}
                    <h1>error</h1>
                {/if}

            </div>
        </div>
            
    </main>


</div>

<style>
    /* .btn{

    } */
</style>