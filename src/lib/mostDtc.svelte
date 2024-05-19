<script>
import moment from 'moment-timezone';
import ChartDay from './chartMost/chartDay.svelte';
import ChartPeriod from './chartMost/chartPeriod.svelte';

import { onMount } from 'svelte';

let stDateRange = moment().tz('Asia/Bangkok').format('YYYY-MM-DD'); 
let endDateRange = moment().tz('Asia/Bangkok').format('YYYY-MM-DD');
let valrangeD = 'Today';
let setDateFilter = {Start:moment().tz('Asia/Bangkok').clone().add(0, 'days').format('YYYY-MM-DD'), End:moment().tz('Asia/Bangkok').clone().add(0, 'days').format('YYYY-MM-DD')};

let statusLoadTab1 = false
let DataSetMost = []

let NewDataStatus = false

    onMount(
        async () => {
        await GetDataTab1(stDateRange, endDateRange);
    }
    )
    
function formatDateToYYYYMMDD(date) {
  var formattedDate = new Date(date).toISOString().split('T')[0];
  return formattedDate;
}

function checkEndDate(startDate, endDate) {
 
  if(startDate !== '' ||  endDate !== ''){
     // แปลงวันที่เป็น Date objects
    startDate = formatDateToYYYYMMDD(startDate)
    endDate = formatDateToYYYYMMDD(endDate)
    var start = new Date(startDate);
    var end = new Date(endDate);

    // เปรียบเทียบ timestamp ของวันที่เริ่มต้นและสิ้นสุด
    return end.getTime() >= start.getTime();
  }
  
}


function checkDateEnd001(){
    let btnRange = document.getElementById('btn-query');
    let endDate = document.getElementById('endDate');

    if(checkEndDate(stDateRange, endDateRange)){
        btnRange.removeAttribute("disabled")
        setDateFilter.Start = stDateRange
        setDateFilter.End = endDateRange
    }else{
        endDateRange = ""
        btnRange.setAttribute("disabled", "")
        alert('The end date must be greater than the start date.')
    }
}

console.log(setDateFilter);


function changeRangeDate(event){
     valrangeD = event.target.value

    if(valrangeD !== 'custom'){
        statusLoadTab1 = false
        
        
        var dateObject = moment().tz('Asia/Bangkok');
        if(valrangeD === 'Today'){
        setDateFilter.Start = dateObject.format('YYYY-MM-DD')
        setDateFilter.End = dateObject.format('YYYY-MM-DD')
        }

        else if(valrangeD === 'yesterday'){
        setDateFilter.Start = dateObject.clone().add(-1, 'days').format('YYYY-MM-DD')
        setDateFilter.End = dateObject.clone().add(-1, 'days').format('YYYY-MM-DD')
        }
        
        else if(valrangeD === '7days'){
            setDateFilter.Start = dateObject.clone().add(-7, 'days').format('YYYY-MM-DD')
            setDateFilter.End = dateObject.clone().add(-1, 'days').format('YYYY-MM-DD')
        
        
        }

        else if(valrangeD === '14days'){
            setDateFilter.Start = dateObject.clone().add(-14, 'days').format('YYYY-MM-DD')
            setDateFilter.End = dateObject.clone().add(-1, 'days').format('YYYY-MM-DD')
        
        }

        else if(valrangeD === '28days'){
            setDateFilter.Start = dateObject.clone().add(-28, 'days').format('YYYY-MM-DD')
            setDateFilter.End = dateObject.clone().add(-1, 'days').format('YYYY-MM-DD')
        
        }

        else if(valrangeD === 'thisMonth'){
        let dateCheck30_31 = moment().tz('Asia/Bangkok').subtract(0, 'months').format('MM')

        if(dateCheck30_31 === '04' || dateCheck30_31 === '06' || dateCheck30_31 === '09' || dateCheck30_31 === '11'){
            setDateFilter.Start = moment('01','DD').tz('Asia/Bangkok').subtract(0, 'months').format('YYYY-MM-DD');
            setDateFilter.End = moment('30','DD').tz('Asia/Bangkok').subtract(0, 'months').format('YYYY-MM-DD');
        }

        else if(dateCheck30_31 === '01' || dateCheck30_31 === '03' || dateCheck30_31 === '05' || dateCheck30_31 === '07' || dateCheck30_31 === '08' || dateCheck30_31 === '10' || dateCheck30_31 === '12'){
            setDateFilter.Start = moment('01','DD').tz('Asia/Bangkok').subtract(0, 'months').format('YYYY-MM-DD');
            setDateFilter.End = moment('31','DD').tz('Asia/Bangkok').subtract(0, 'months').format('YYYY-MM-DD');
        }

        else if(dateCheck30_31 === '28'){
            setDateFilter.Start = moment('01','DD').tz('Asia/Bangkok').subtract(0, 'months').format('YYYY-MM-DD');
            setDateFilter.End = moment('28','DD').tz('Asia/Bangkok').subtract(0, 'months').format('YYYY-MM-DD');
        }

        else if(dateCheck30_31 === '29'){
            setDateFilter.Start = moment('01','DD').tz('Asia/Bangkok').subtract(0, 'months').format('YYYY-MM-DD');
            setDateFilter.End = moment('29','DD').tz('Asia/Bangkok').subtract(0, 'months').format('YYYY-MM-DD');
        }
        
        
        }

        else if(valrangeD === 'lastMonth'){
            let dateCheck30_31 = moment().tz('Asia/Bangkok').subtract(1, 'months').format('MM')

            if(dateCheck30_31 === '04' || dateCheck30_31 === '06' || dateCheck30_31 === '09' || dateCheck30_31 === '11'){
                setDateFilter.Start = moment('01','DD').tz('Asia/Bangkok').subtract(1, 'months').format('YYYY-MM-DD');
                setDateFilter.End = moment('30','DD').tz('Asia/Bangkok').subtract(1, 'months').format('YYYY-MM-DD');
            }

            else if(dateCheck30_31 === '01' || dateCheck30_31 === '03' || dateCheck30_31 === '05' || dateCheck30_31 === '07' || dateCheck30_31 === '08' || dateCheck30_31 === '10' || dateCheck30_31 === '12'){
                setDateFilter.Start = moment('01','DD').tz('Asia/Bangkok').subtract(1, 'months').format('YYYY-MM-DD');
                setDateFilter.End = moment('31','DD').tz('Asia/Bangkok').subtract(1, 'months').format('YYYY-MM-DD');
            }

            else if(dateCheck30_31 === '28'){
                setDateFilter.Start = moment('01','DD').tz('Asia/Bangkok').subtract(1, 'months').format('YYYY-MM-DD');
                setDateFilter.End = moment('28','DD').tz('Asia/Bangkok').subtract(1, 'months').format('YYYY-MM-DD');
            }

            else if(dateCheck30_31 === '29'){
                setDateFilter.Start = moment('01','DD').tz('Asia/Bangkok').subtract(1, 'months').format('YYYY-MM-DD');
                setDateFilter.End = moment('29','DD').tz('Asia/Bangkok').subtract(1, 'months').format('YYYY-MM-DD');
            }
        }

        else if(valrangeD === 'thisYear'){
                setDateFilter.Start = moment('01-01','MM-DD').tz('Asia/Bangkok').format('YYYY-MM-DD');
                setDateFilter.End = moment('12-31','MM-DD').tz('Asia/Bangkok').format('YYYY-MM-DD');
        }

        else if(valrangeD === 'lastYear'){
            setDateFilter.Start = moment('01-01','MM-DD').tz('Asia/Bangkok').subtract(1, 'years').format('YYYY-MM-DD');
            setDateFilter.End = moment('12-31','MM-DD').tz('Asia/Bangkok').subtract(1, 'years').format('YYYY-MM-DD');
        }
        console.log(setDateFilter);
        let mosData = GetDataTab1(setDateFilter.Start,setDateFilter.End)
    }

}

async function GetDataTab1(Start,End){
    try {
    const response = await fetch(`https://script.google.com/macros/s/AKfycbyI8meJvnOHbvIlTWGdEavzr5rq_ddSgi-v6sRwlvjpcRVjZ8dWWVrzN0RUePTxPep9eA/exec?dateStart=${Start}&dateEnd=${End}`);
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    
    let data = await response.json();
    data.selected = valrangeD
    // .then((val)=>{
        console.log('Loaded');
        statusLoadTab1 = true
        DataSetMost = [data]

        
        console.log(DataSetMost);
        return data

  } catch (error) {
    console.log(error);
  }
}



function btnRangeCustom() {
    statusLoadTab1 = false;
    GetDataTab1(setDateFilter.Start,setDateFilter.End)
}
</script>


    <section class="container  p-[0.5rem] flex">

       
            {#if statusLoadTab1 }
                <div class="w-full min-h-full flex gap-4">
                   {#each DataSetMost as Data }   
                    <div class="box-1 w-6/12 min-h-full  flex flex-col gap-3">
                          
                                <div class="card w-full h-[12rem] flex flex-col justify-between bg-white p-[0.5rem] rounded-[10px] shadow-[0_0_5px_.5px_#7f7f7fb3]">
                                    <h2 class="flex items-center text-3xl text-[#216b99] font-bold">
                                        <img
                                            src="https://cdn-icons-png.flaticon.com/128/9195/9195785.png" alt=""
                                            class="w-[30px]">
                                            information & latest detected
                                    </h2>
                                    {#if Object.keys(Data.newData).length === 0}
                                    <div class="flex justify-center items-center gap-1">
                                        <h1 class="text-3xl text-center text-green-600 font-bold">Not detected</h1>
                                        <img class="w-[52px]" src="https://www.icegif.com/wp-content/uploads/icegif-3608.gif" alt="">
                                    </div>
                                        
                                        <div class="w-2 h-2 "></div>

                                    {:else}
                                        <div class="flex justify-between h-6/12 mt-[0.5rem]">
                                        <div class="group-text flex flex-col">
                                            <p class="text-[18px] font-bold">timestamp</p>
                                            <p class="text-[16px]">{Data.newData.infoDv.timestamp}</p>
                                        </div>

                                        <div class="group-text flex flex-col">
                                            <p class="text-[18px] font-bold">Device ID</p>
                                            <p class="text-[16px]">{Data.newData.infoDv.DvId}</p>
                                        </div>

                                        <div class="group-text flex flex-col">
                                            <p class="text-[18px] font-bold">Time Period</p>
                                            <p class="text-[16px]">{Data.newData.infoDv.timeP}</p>
                                        </div>
                                        </div>


                                        <div class="flex  mt-[0.5rem]">
                                            <div class="group-text flex flex-col">
                                                <p class="text-[18px] font-bold">address</p>
                                                <p class="text-[16px]">{Data.newData.infoDv.address}</p>
                                            </div>
                                        </div>
                                    {/if}
                                    
                                    
                                    
                                    
                                </div>

                                <div class="w-full flex gap-4">
                                    <div class="numberrr w-6/12 flex flex-col  gap-2">
                                        <h2 class="flex items-center text-2xl text-[#389625] font-bold">
                                            <img
                                                src="https://cdn-icons-png.flaticon.com/128/10588/10588422.png" alt=""
                                                class="w-[40px] mr-2">
                                                count of detected
                                        </h2>
                                        {#if Object.keys(Data.newData).length === 0}
                                        <div class="contentNumberr relative h-[6rem] shadow-[0_0_5px_1px_#7f7f7fb3] rounded-[.5rem] border-l-[.3rem] border-l-[#58cc41] ">
                                            <div class="w-full flex flex-col gap-2 justify-between absolute bottom-0 p-2">
                                                <p class="text-[20px] flex items-center">0
                                                    <span class="text-[16px] text-green-600 font-bold ml-1">
                                                        not detected!
                                                        
                                                    </span>
                                                </p>
                                                <p class="w-full flex justify-end text-[14px]">total from range</p>
                                            </div>
                                        </div>


                                        <div class="contentNumberr relative h-[6rem] shadow-[0_0_5px_1px_#7f7f7fb3] rounded-[.5rem] border-l-[.3rem] border-l-[#bd5626] ">
                                            <div class="w-full flex flex-col gap-2 justify-between absolute bottom-0 p-2">
                                                <p class="text-[20px] flex items-center">0
                                                    <span class="text-[16px] text-green-600 font-bold ml-1">
                                                        not detected!
                                                        
                                                    </span>
                                                </p>
                                                <p class="w-full flex justify-end text-[14px]">Today</p>
                                            </div>
                                        </div>


                                        <div class="contentNumberr relative h-[6rem] shadow-[0_0_5px_1px_#7f7f7fb3] rounded-[.5rem] border-l-[.3rem] border-l-[#4d1ac5] ">
                                            <div class="w-full flex flex-col gap-2 justify-between absolute bottom-0 p-2">
                                                <p class="text-[20px] flex items-center">0
                                                    <span class="text-[16px] text-green-600 font-bold ml-1">
                                                        not detected!
                                                        
                                                    </span>
                                                </p>
                                                <p class="w-full flex justify-end text-[14px]">all</p>
                                            </div>
                                        </div>

                                        {:else}

                                        <div class="contentNumberr relative h-[6rem] shadow-[0_0_5px_1px_#7f7f7fb3] rounded-[.5rem] border-l-[.3rem] border-l-[#58cc41] ">
                                            <div class="w-full flex flex-col gap-2 justify-between absolute bottom-0 p-2">
                                                <p class="text-[18px]">{Data.newData.totalfromRamge}</p>
                                                <p class="w-full flex justify-end text-[14px]">total from range</p>
                                            </div>
                                        </div>


                                        <div class="contentNumberr relative h-[6rem] shadow-[0_0_5px_1px_#7f7f7fb3] rounded-[.5rem] border-l-[.3rem] border-l-[#bd5626] ">
                                            <div class="w-full flex flex-col gap-2 justify-between absolute bottom-0 p-2">
                                                <p class="text-[18px]">{Data.newData.TodayT}</p>
                                                <p class="w-full flex justify-end text-[14px]">Today</p>
                                            </div>
                                        </div>


                                        <div class="contentNumberr relative h-[6rem] shadow-[0_0_5px_1px_#7f7f7fb3] rounded-[.5rem] border-l-[.3rem] border-l-[#4d1ac5] ">
                                            <div class="w-full flex flex-col gap-2 justify-between absolute bottom-0 p-2">
                                                <p class="text-[18px]">{Data.newData.TotalAll}</p>
                                                <p class="w-full flex justify-end text-[14px]">all</p>
                                            </div>
                                        </div>

                                        {/if}
                                    </div>

                                    <div class="rangeDate w-6/12">
                                        <h2 class="flex items-center gap-1 text-2xl text-slate-500 font-bold">
                                            <div class="w-[40px] mr-2 bg-slate-300 p-1 rounded-full flex items-center justify-center">
                                                <img
                                                src="https://cdn-icons-png.flaticon.com/128/16056/16056916.png" alt=""
                                                class="w-[350px] ">
                                            </div>
                                            
                                                date range
                                        </h2>
                                       
                                       
                                        <div class="flex flex-col">
                                            <label for="dateRange">date range</label>
                                            <select name="dateRange" id="dateRange" class="outline-none h-[40px] text-[16px] border-[1px] border-[#468999] rounded-[5px]" on:change={changeRangeDate} bind:value={valrangeD}>
                                                <option id="7days"  value="7days">last 7 days</option>
                                                <option id="14days"  value="14days">last 14 days</option>
                                                <option id="28days"  value="28days">last 28 days</option>
                                                <option id="Today"  value="Today">Today</option>
                                                <option id="yesterday"  value="yesterday">yesterday</option>
                                                <option id="thisMont"  value="thisMonth">this month</option>
                                                <option id="lastMont"  value="lastMonth">last month</option>
                                                <option id="thisYea"  value="thisYear">this year</option>
                                                <option id="lastYea"  value="lastYear">last year</option>
                                                <option id="custom"  value="custom">custom</option>
                                            </select>
                                        </div>
                                        

                                        {#if valrangeD === 'custom'}
                                            <div class="flex flex-col"  id="dateRangeCustom">

                                                <div class="flex flex-col">
                                                    <label for="startDate">start Date</label>
                                                    <input type="date" id="startDate" class="outline-none h-[40px] text-[16px] border-[1px] border-[#468999] rounded-[5px]" bind:value={stDateRange} on:change={checkDateEnd001}>
                                                </div>

                                                <div class="flex flex-col">
                                                    <label for="startDate">End date</label>
                                                    <input type="date" id="endDate" class="outline-none h-[40px] text-[16px] border-[1px] border-[#468999] rounded-[5px]" bind:value={endDateRange} on:change={checkDateEnd001}>
                                                </div>

                                                <button id="btn-query" class="w-full cursor-pointer bg-[#468999] border-none p-[.25rem] rounded-[.25rem] text-white text-[18px] shadow-[0_0_3px_1px_#4646468e] mt-3 disabled:cursor-default disabled:bg-[#b7b7b7] disabled:text-[#464646]  hover:bg-[#366b79] hover:text-[#ffffff] hover:shadow-[0_0_3px_1px_#4646468e]"  disabled on:click={(btnRangeCustom)}>filter</button>

                                            </div>
                                        {/if}
                                    </div>
                                </div>

                            
                     
                    </div>


                    <div class="box-2 w-6/12 min-h-full flex flex-col gap-3">
                        <div class="card w-full h-[50%] flex flex-col justify-between  bg-white p-[0.5rem] rounded-[10px] shadow-[0_0_5px_.5px_#7f7f7fb3]">
                            <h2 class="flex items-center text-2xl  text-sky-500 font-bold">
                            <img
                                src="https://cdn-icons-png.flaticon.com/128/3281/3281306.png" alt=""
                                class="w-[40px] mr-2">
                                detected chart
                        </h2>
                            {#if Object.keys(Data.newData).length === 0}
                            <div class="w-[100%] h-[100%] flex items-center justify-center p-2">
                                <div class="flex justify-center items-center gap-1">
                                    <h1 class="text-3xl text-center text-green-600 font-bold">Not detected</h1>
                                    <img class="w-[52px]" src="https://www.icegif.com/wp-content/uploads/icegif-3608.gif" alt="">
                                </div>
                            </div>
                            {:else}
        
                                <ChartDay chartLabels={Data.newData.labels} chartValues={Data.newData.Dataset} />

                            
                            {/if}

                            <div class="w-2 h-10 bg-transparent"></div>
                        </div>

                        <div class="card w-full h-[50%] flex flex-col justify-between bg-white p-[0.5rem] rounded-[10px] shadow-[0_0_5px_.5px_#7f7f7fb3] mb-2">
                            <h2 class="flex items-center text-2xl text-rose-400 font-bold">
                                <img
                                    src="https://cdn-icons-png.flaticon.com/128/3616/3616111.png" alt=""
                                    class="w-[40px] mr-2">
                                    detected chart
                            </h2>
                            {#if Object.keys(Data.newData).length === 0}

                                <div class="flex justify-center items-center gap-1">
                                    <h1 class="text-3xl text-center text-green-600 font-bold">Not detected</h1>
                                    <img class="w-[52px]" src="https://www.icegif.com/wp-content/uploads/icegif-3608.gif" alt="">
                                </div>
                            
                            {:else}
                            <ChartPeriod chartValues={[Data.newData.conutTimePeriod.Night, Data.newData.conutTimePeriod.Day]} />

                            {/if}
                            <div class="w-2 h-5 bg-transparent"></div>
                        </div>
                    </div>
            {/each}   

                </div>

            {:else if statusLoadTab1 === 'error' }

                <div class="w-full h-full flex gap-1 justify-center items-center">
                    <span class="relative flex h-[2rem] w-[2rem]">
                        <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-sky-400 opacity-75"></span>
                        <span class="relative inline-flex rounded-full h-[2rem] w-[2rem] bg-sky-500"></span>
                      
                    </span>
                <div class="text-2xl text-red-500">An error occurred. Please reload the page.</div>
                    
                </div>

            {:else }
                <div class="w-full h-full flex gap-1 justify-center items-center">
                    <span class="relative flex h-[2rem] w-[2rem]">
                        <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-sky-400 opacity-75"></span>
                        <span class="relative inline-flex rounded-full h-[2rem] w-[2rem] bg-sky-500"></span>
                    
                    </span>
                    <div class="text-2xl">loading...</div>
                </div>

                {/if}
        











    </section>
