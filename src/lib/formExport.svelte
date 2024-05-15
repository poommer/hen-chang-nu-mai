<script>
import Export from "./export.svelte";
import moment from 'moment-timezone';
let currantPage = 3

let DvIdList = []

let stDate = null;
let endDate = null;
let Day = false
let Night = false

let dataExport = [];

let statusGetData = null;


function formatDateToYYYYMMDD(date) {
  var formattedDate = new Date(date).toISOString().split('T')[0];
  return formattedDate;
}

function checkEndDate(startDate, endDate) {
  // แปลงวันที่เป็น Date objects
  startDate = formatDateToYYYYMMDD(startDate)
  endDate = formatDateToYYYYMMDD(endDate)
  var start = new Date(startDate);
  var end = new Date(endDate);

  // เปรียบเทียบ timestamp ของวันที่เริ่มต้นและสิ้นสุด
  return end.getTime() >= start.getTime();
}

function useFucnCheckDate(event){

event.target.id === 'filter-DateSt' ? stDate = event.target.value : endDate = event.target.value

if( (stDate !== undefined && stDate !== null && stDate !== '' ) && endDate !== undefined && endDate !== null && endDate !== ''){
    if(checkEndDate(stDate, endDate))   {
         console.log('yet!')
    } else{
        alert('The end date must be greater than the start date.')
        event.target.id === 'filter-DateSt' ? stDate = '' :  endDate = ''
    }
   
} 
}
// let SetTimePeriod = ti
async function getData(dataDevice, start, end, timeperiod) {

    // console.log({dataDevice, start, end, timeperiod});

    let responseDataGet = await  fetch( 'https://script.google.com/macros/s/AKfycbxMioYOWhkiyVSAKgRcSVyVNmXr5YUXzTPakbeQzv6sO-7xJ2wY6k0eB9IRvI0lCkbe/exec')
        if (!responseDataGet.ok) {
          throw new Error('Network response was not ok');
        }
    
        let response = await responseDataGet.json();

        let SetTimePe;

        if(timeperiod.Day){
            SetTimePe = 'Day' ;
        } else if(timeperiod.Night){
            SetTimePe = 'Night' ;
        } else{
            SetTimePe = 'all' ;
        }


       
        var query = await response.filter((val) => {
            
              return dataDevice.some(device => device.DvID === val.DvID && device.status === true) || dataDevice.every(device => device.DvID === val.DvID && device.status === false)
            });


            if(timeperiod.Day && !timeperiod.Night){
                console.log('Day');
                query = await query.filter(val => {return val.timePeriod === 'Day' })
            }else if(!timeperiod.Day && timeperiod.Night){
                    console.log('Night');
                    query = await query.filter(val => { return val.timePeriod === 'Night' })
                    
            }



            if(stDate !== null){
                start = moment(start).tz('Asia/Bangkok').format('YYYY-MM-DD')
                end = moment(end).tz('Asia/Bangkok').format('YYYY-MM-DD')

                query = await query.filter((val) => {
            
                return moment(val.date).tz('Asia/Bangkok').format('YYYY-MM-DD') >= start && moment(val.date).tz('Asia/Bangkok').format('YYYY-MM-DD') <= end
                });

                console.log({start, end});
            }

            
            dataExport = query

            currantPage = true
            statusGetData = true
        console.log(dataExport);
        

}


async function getDeviceData(){
    const response = await fetch('https://script.google.com/macros/s/AKfycby9vPBs9n5GyRhCXiUNcAypI1q5MfcDoRYPuU17miFmQwrYHqhRIhUuXDKZqNrZuwJX/exec');
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        const data = await response.json();
    
       
        data.forEach((val) => {
           DvIdList.push({DvID:val.DvId, status:false})
        }) 
        
        
}



getDeviceData()
.then(()=>{
    console.log(DvIdList);
   DvIdList = DvIdList
})

// function queryData() {
//     getData()
// }


</script>



<div class="flex justify-center items-center">
    <div class="flex gap-1 items-center justify-center">
    <div class="flex flex-col gap-1 justify-center items-center">
        <div class={`w-[3rem] h-[3rem] rounded-full  flex justify-center items-center text-xl ${currantPage >= 1 ? 'bg-green-600 text-white':'bg-slate-200'}`}>1</div>
        <p class={`${currantPage >= 1 || currantPage === true ? 'text-green-600':'text-slate-500'} text-[12px]`}>Select Device</p>
    </div>
    
    <div class="flex flex-col gap-1 justify-center items-center">
       <div class={`w-[10rem] h-[.5rem] rounded-full   ${currantPage >= 2 || currantPage === true ? 'bg-green-600 text-white':'bg-slate-200'}` }></div>
        <div class="h-4"></div>
    </div>
    
    <div class="flex flex-col gap-1 justify-center items-center">
    <div class={`w-[3rem] h-[3rem] rounded-full  flex justify-center items-center text-xl ${currantPage >= 2 || currantPage === true ? 'bg-green-600 text-white':'bg-slate-200'}`}>2</div>
    <p class={`${currantPage >= 2 || currantPage === true ? 'text-green-600':'text-slate-500'} text-[12px]`}>filter Data</p>
</div>

    <div class="flex flex-col gap-1 justify-center items-center">
    <div class={`w-[10rem] h-[.5rem] rounded-full  ${currantPage === true ? 'bg-green-600 text-white':'bg-slate-200'}`}></div>
    <div class="h-4"></div>
    </div>
    <div class="flex flex-col gap-1 justify-center items-center">
        <div class={`w-[3rem] h-[3rem] rounded-full flex justify-center items-center text-xl ${currantPage === true ? 'bg-green-600 text-white':'bg-slate-200 text-slate-500'}`}>3</div>
        <p class={`${currantPage === true ? 'text-green-600':  'text-slate-500'} text-[12px]`}>your Data</p>
    </div>
</div>
</div>


<div>
    {#if currantPage === 1}
        <div class="flex justify-center items-center m-4 gap-4">
            {#each DvIdList as data }
                <label for={data.DvID} class={`w-[5rem] h-[2rem] text-[20px] flex items-center justify-center p-2 rounded cursor-pointer  ${data.status === true ? 'bg-gray-500 text-white ': 'bg-slate-200 text-red-700'}`}>
                    {data.DvID}
                    <input type="checkbox" name="Dvid" id={data.DvID} value={data.DvID} class="hidden" bind:checked={data.status} on:change={(val) => {data.status = val.target.checked ;if(Object.values(DvIdList).some(val => val.status === true)){console.log('yet!!');}}}>
                </label>
                    
            {/each}
        
        </div>
        

    {:else if currantPage === 2}
        <h1>filter Data</h1>
        <div class="flex p-4 text-[20px] justify-center">
                
                <div class="flex flex-col">
                    <div class="flex flex-col">
                        <label for="filter-DateSt">Date start:</label>
                        <input type="date" name="filter-DateSt" id="filter-DateSt" class="w-[13rem] h-[2.5rem] outline-none text-[16px] border-[#468999] border-[2px] rounded-[5px]" on:change={useFucnCheckDate} bind:value={stDate}>
                    </div>

                    <div class="flex flex-col">
                        <label for="filter-DateEnd">Date end:</label>
                        <input type="date" name="filter-DateEnd" id="filter-DateEnd" class="w-[13rem] h-[2.5rem] outline-none text-[16px] border-[#468999] border-[2px] rounded-[5px]" on:change={useFucnCheckDate} bind:value={endDate}>
                    </div>
                </div>
                    

                    <div class="flex flex-col ml-4">
                        <label for="">time period</label>
                        <div class="flex gap-2 justify-center items-center">
                            <label for="filter-day" class={ `w-[5rem] h-[2.5rem] text-center p-[0.25rem] cursor-pointer rounded-[.25rem] ${Day ? "bg-[#F3CF29] text-[#468999]":" bg-slate-200 cursor-pointer"}`}>
                                <input type="checkbox" name="filter-timePr" id="filter-day" value="Day"
                                    style="display: none;" bind:checked={Day}>
                                Day
                            </label>

                            <label for="filter-night" class={`w-[5rem] h-[2.5rem] text-center p-[0.25rem] cursor-pointer rounded-[.25rem] ${Night ? "bg-[#F3CF29] text-[#468999]":" bg-slate-200 cursor-pointer"}`}>
                                <input type="checkbox" name="filter-timePr" id="filter-night" value="Night"
                                    style="display: none;" bind:checked={Night}>
                                Night 
                            </label>
                            
                        </div>

                       
                    </div>
    
                    
                
        </div>

        

    
    {:else}
    <div class="w-full  h-[30rem] flex flex-col justify-center items-center gap-4">
        <h1 class="text-3xl font-bold">success, Your data is ready to be exported.</h1>
        <p class=" text-lg">Please Click on the file button you want to download.</p>
        

        <div>
            <Export din jsonData={dataExport} />
        </div>

        <div>
            <button class=" rounded-[.25rem] text-lg w-[8rem] border-neutral-500 border-[1px] bg-white text-neutral-500" on:click={() => {currantPage = 1}}>back to filter</button>
        <button class="rounded-[.25rem] text-lg w-[8rem]  bg-neutral-500 text-white" on:click={() => {Object.values(DvIdList).some(val => val.status === true ? val.status = false : ''); endDate = null; stDate =null; Day=false; Night=false; currantPage = 1 }}>reset</button>
    
        </div>
    </div>
        
        {/if}
</div>


<div class="w-full flex justify-end gap-2">

    <button class={`rounded-[.25rem] text-lg w-[5rem] border-neutral-500 border-[1px] bg-white text-neutral-500 ${currantPage === 2 ? '' : 'hidden'}`} on:click={() => {currantPage -= 1}}>back</button>
    <button class={`rounded-[.25rem] text-lg w-[5rem]  bg-neutral-500 text-white ${currantPage === 1 ? '' : 'hidden'} disabled:bg-neutral-200 disabled:text-neutral-500`} disabled={!Object.values(DvIdList).some(val => val.status === true)} on:click={() => {currantPage += 1}}>next</button>
    <button class={`rounded-[.25rem] text-lg w-[5rem] bg-green-600 text-white disabled:bg-green-300 disabled:text-gray-100 ${currantPage === 2 || statusGetData === 'loading'  ? '' : 'hidden'}  ${statusGetData === 'loading'? 'cursor-wait':''}`} disabled={statusGetData === 'loading'?  true:false} on:click={() => {
        statusGetData = 'loading'
        getData(DvIdList,stDate,endDate, {Day:Day, Night:Night})
        .than(()=>{
            dataExport = dataExport
        }) 
        }}>done</button>
</div>
