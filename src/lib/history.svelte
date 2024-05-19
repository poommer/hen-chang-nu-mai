<script>
    import { onMount } from 'svelte';

    let statusLoad = false

    let pageCur = 1;
    let dataList;
    let dataset = []
    let menuData;
    let cateGroup = [] ;

    let currantMenu = 0;
    let prev;
    let next

    let DataPagination

    let textFrom;

    var newLatest = null ;
var newLatestD = null ;

var countUpdate = 0;
let updateData = undefined;

let statusUpdateLoad = false;

    async function getData() { 
        console.log('loading data');
        const response = await fetch('https://script.google.com/macros/s/AKfycbxMioYOWhkiyVSAKgRcSVyVNmXr5YUXzTPakbeQzv6sO-7xJ2wY6k0eB9IRvI0lCkbe/exec');
        if (!response.ok) {
            throw new Error('Network response was not ok');
        }
    
       
        let data = await response.json();
        data = data.sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp)); 
        if(newLatest === null && newLatestD === null){
            newLatest = data[0].timestamp
            newLatestD = data[0].DvID
          } 
          else if (newLatest !== data[0].timestamp && newLatestD !== data[0].DvID){
            // $('#update-gif').hide();
             newLatest = data[0].timestamp
            newLatestD = data[0].DvID
            updateData = true
            countUpdate += 1
          }


        if(dataList === undefined || statusUpdateLoad === true){
            dataList = data
        }
        // console.log(dataList.length);
        
        // console.log(dataList.length);
        console.log('loaded data');
        console.log({newLatest, newLatestD});
        statusLoad = true
    }



async function pagination(pagenum){

    if(dataList === undefined){
        await getData()
    }
    const page = parseInt(pagenum)
    const numberPage = 50;
    let totalPage =  dataList.length/numberPage
    
    let res = []

    if(totalPage % 1 !== 0){ totalPage = parseInt(totalPage) + 1} 

    if (page > totalPage || !page || page < 1) {
        res.push({msg:'Page not found'});
    }

    const start = (page - 1) * numberPage;
    const end = start + numberPage;
    const result = dataList.slice(start, end);
    
    // console.log({totalPage, page, start, end, result});
     menuData = {totalPage, page, start, end, result}
     dataset = menuData.result
console.log(dataList);
     textFrom = `data: ${menuData.start + 1} - ${menuData.end} from ${dataList.length} | page: ${pageCur} of ${totalPage}`

}

async function loadNewData() {
    statusUpdateLoad = true
    updateData= undefined;
    await getData()
    pagination(pageCur)
}


pagination(pageCur)
.then(()=>{
    
    let menuTotal = (menuData.totalPage/10) % 1 !== 0 ? parseInt((menuData.totalPage/10)) : parseInt((menuData.totalPage/10)) +1

     let start;
     let end;
    for(let y = 0; y<=menuTotal; y++){
       start = y === 0? 1 : (y*10)+1;
       end = y === 0 ? 10 : y === menuTotal ?  menuData.totalPage : start+9;

        cateGroup.push([])
         for(let i = start; i<= end; i++){
            cateGroup[y].push(i)

         }
    }

    DataPagination = cateGroup[currantMenu]
    cateGroup = cateGroup
    console.log(DataPagination);
})

setInterval(async () => {
    await getData()
    pagination(pageCur)
    console.log(updateData);
  }, 20000);



  

</script>

{#if !statusLoad}
<div class="w-full h-full flex gap-1 justify-center items-center">
    <span class="relative flex h-[2rem] w-[2rem]">
        <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-sky-400 opacity-75"></span>
        <span class="relative inline-flex rounded-full h-[2rem] w-[2rem] bg-sky-500"></span>
    
    </span>
    <div class="text-2xl">loading...</div>
</div>


{:else}

<div class="flex flex-col gap-4" id="showData">
    <div>
        <div class={`w-full flex ${updateData === true? 'justify-between' : 'justify-end'}  items-end p-2 text-zinc-600`}>
            {#if updateData === true}
            <div class="flex justify-center items-center gap-2">
                 <h1 class="text-rose-600 font-bold">new detected {countUpdate}</h1>
                <button class="p-1 rounded bg-slate-400 text-slate-700" on:click={loadNewData}>reload</button>
            </div>
               
            {/if}
            
            <h1>{textFrom}</h1>
        </div>
        <table class="w-full rounded-[.5rem] overflow-hidden shadow-[2px_2px_5px_.5px_#7f7f7fb3]">
            <thead class="text-left bg-[#468999] text-white text-lg">
                <th class="p-2">#</th>
                    <th>Timestamp</th>
                <th>device ID</th>
                <th>time period</th>
                <th>address</th>
            </thead>

            <tbody>
                {#each dataset as data, index }
                    <tr class={` border-y-2 bg-gray-50  border-y-slate-100 ${index+1 === dataset.length ? 'border-b-[#468999] border-b-[.5rem] ' : ''} hover:text-teal-800 hover:font-bold hover:bg-slate-300  focus:bg-red-700  even:bg-gray-100  `}>
                        <td class={`p-3`}>{menuData.start + index +1}</td>
                        <td>{data.timestamp}</td>
                        <td>{data.DvID}</td>
                        <td>{data.timePeriod}</td>
                        <td>{data.address}</td>
                </tr>
                {/each}
                
            </tbody>
        </table>
    </div>

    <div class="flex gap-2 justify-end">
        {#each  cateGroup as group,groupIndex }
        {#if groupIndex === currantMenu }
        <button class={`rounded-[.25rem] text-lg w-8 h-8  ${groupIndex !== 0 ? ' bg-neutral-500 text-white' : 'bg-slate-200 text-white'} disabled:cursor-default`} disabled={groupIndex === 0 ? true : false} on:click={() => {if(groupIndex !== 0) {currantMenu -= 1} }}>{'<'}</button>
        {#each group as itemNum }
                    <button class={`rounded-[.25rem] text-lg w-8 h-8 ${pageCur === itemNum ? 'bg-slate-600 text-gray-200' : 'text-slate-500 bg-white border-[1px] border-slate-500'}` } on:click={ () =>{
                pageCur = itemNum
                pagination(itemNum)
                document.getElementById('main').scrollTop = 0
                }}>{itemNum}</button>
        {/each}
        <button class={`rounded-[.25rem] text-lg w-8 h-8  ${groupIndex+1 !== cateGroup.length ? ' bg-neutral-500 text-white ' : 'bg-slate-200 text-white'} disabled:cursor-default`} disabled={groupIndex+1 === cateGroup.length ? true : false} on:click={() => {if(groupIndex+1 !== cateGroup.length) {currantMenu += 1} }}>{'>'}</button>
        {/if} 
        {/each}

    </div>
</div>



{/if}