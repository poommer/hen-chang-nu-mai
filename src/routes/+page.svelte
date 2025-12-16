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
        <Header />
    </header>
    

    <main class="w-full h-full flex flex-col  items-center p-10 ">
        
        <div class=" w-[70%] min-h-[80%]" >
            <div class="grounp-tap w-[100%]  flex place-items-end gap-1 pl-1">

             <button 
             class={compoCurrant === 1?
            "bg-white h-[3rem] w-[12rem] relative flex gap-1 items-center justify-center  text-[18px] font-bold p-[0.25rem] rounded-t-[0.5rem] border-x-[#468999] border-t-[#468999] border-x-[.1rem] border-t-[.1rem]   after:contents-[''] after:w-full after:h-1  after:bg-white after:absolute after:bottom-[-1px]":"flex gap-1 items-center justify-center  text-[16px] w-[10rem] font-bold p-[0.25rem] rounded-t-[0.5rem] bg-[#468999] text-white"}  
            type="button" 
            on:click={() => {compoCurrant = 1}}
            >
            <svg
                xmlns="http://www.w3.org/2000/svg"
                width="20"
                height="20"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class={`fill-none ${compoCurrant === 1? "stroke-black" : "stroke-white "}`}
                >
                <path d="M12 13m-2 0a2 2 0 1 0 4 0a2 2 0 1 0 -4 0" />
                <path d="M13.45 11.55l2.05 -2.05" />
                <path d="M6.4 20a9 9 0 1 1 11.2 0z" />
                </svg>

                dashboard
            </button>

             <button 
             class={compoCurrant === 2?
            "bg-white h-[3rem] w-[12rem] relative flex gap-1 items-center justify-center  text-[18px] font-bold p-[0.25rem] rounded-t-[0.5rem] border-x-[#468999] border-t-[#468999] border-x-[.1rem] border-t-[.1rem]   after:contents-[''] after:w-full after:h-1  after:bg-white after:absolute after:bottom-[-1px]":"flex gap-1 items-center justify-center  text-[16px] w-[10rem] font-bold p-[0.25rem] rounded-t-[0.5rem] bg-[#468999] text-white"}  
            type="button" 
            on:click={() => {compoCurrant = 2}}
            >
        
                    <!--
                    tags: [search, see, past, card, website]
                    category: System
                    version: "1.7"
                    unicode: "ebea"
                    -->
                    <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="20"
                    height="20"
                    viewBox="0 0 24 24"
                    stroke-width="2"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    class={`fill-none ${compoCurrant === 2? "stroke-black" : "stroke-white "}`}
                    >
                    <path d="M12 8l0 4l2 2" />
                    <path d="M3.05 11a9 9 0 1 1 .5 4m-.5 5v-5h5" />
                    </svg>

                    history
            </button>
             <button 
             class={compoCurrant === 3?
            "bg-white h-[3rem] w-[12rem] relative flex gap-1 items-center justify-center  text-[18px] font-bold p-[0.25rem] rounded-t-[0.5rem] border-x-[#468999] border-t-[#468999] border-x-[.1rem] border-t-[.1rem]   after:contents-[''] after:w-full after:h-1  after:bg-white after:absolute after:bottom-[-1px]":"flex gap-1 items-center justify-center  text-[16px] w-[10rem] font-bold p-[0.25rem] rounded-t-[0.5rem] bg-[#468999] text-white"}  type="button" on:click={() => {compoCurrant = 3}}>
                
                <!--
                tags: [computer, monitor, apple, desktop, pc]
                category: Devices
                version: "2.6"
                unicode: "f7a7"
                -->
                <svg
                xmlns="http://www.w3.org/2000/svg"
                width="20"
                height="20"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class={`fill-none ${compoCurrant === 3? "stroke-black" : "stroke-white "}`}
                >
                <path d="M3 4a1 1 0 0 1 1 -1h16a1 1 0 0 1 1 1v12a1 1 0 0 1 -1 1h-16a1 1 0 0 1 -1 -1v-12z" />
                <path d="M3 13h18" />
                <path d="M8 21h8" />
                <path d="M10 17l-.5 4" />
                <path d="M14 17l.5 4" />
                </svg>

                All Device
            </button>

             <button 
             class={compoCurrant === 4?
            "bg-white h-[3rem] w-[12rem] relative flex gap-1 items-center justify-center  text-[18px] font-bold p-[0.25rem] rounded-t-[0.5rem] border-x-[#468999] border-t-[#468999] border-x-[.1rem] border-t-[.1rem]   after:contents-[''] after:w-full after:h-1  after:bg-white after:absolute after:bottom-[-1px]":"flex gap-1 items-center justify-center  text-[16px] w-[10rem] font-bold p-[0.25rem] rounded-t-[0.5rem] bg-[#468999] text-white"}  type="button" on:click={() => {compoCurrant = 4}}>
                
                <!--
                tags: [document, import, page, move]
                category: Document
                version: "1.56"
                unicode: "f033"
                -->
                <svg
                xmlns="http://www.w3.org/2000/svg"
                width="20"
                height="20"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class={`fill-none ${compoCurrant === 4? "stroke-black" : "stroke-white "}`}
                >
                <path d="M14 3v4a1 1 0 0 0 1 1h4" />
                <path d="M17 21h-10a2 2 0 0 1 -2 -2v-14a2 2 0 0 1 2 -2h7l5 5v11a2 2 0 0 1 -2 2z" />
                <path d="M15 15h-6" />
                <path d="M11.5 17.5l-2.5 -2.5l2.5 -2.5" />
                </svg>


                export
            </button>

    
            
           
            </div>

            <div class=" h-[46rem] bg-white border-[#468999] border-[1px] p-[0.5rem] overflow-y-auto" id="main">
                {#if compoCurrant === 1 }
                    <MostDtc />
                    

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