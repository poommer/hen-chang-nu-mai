<script >
import moment from 'moment-timezone';
import Maps from './chartAndMaps/maps.svelte';
import TimePeriod from './chartAndMaps/timePeriod.svelte';
import Predition from './chartAndMaps/predition.svelte';
import DayChart from './chartAndMaps/dayChart.svelte';
import TimeChart from './chartAndMaps/timeChart.svelte'

let DataListAll = [];
let DataListAllStatusLoad = null;
let currantMaps;
let setLavalSce;

let stDate;
let endDate;
let Day = false
let Night = false
let btnActive = false

async function getAllData(filter_dateStart, filter_dateEnd, filter_TimePeriod, filter_DeviceID) {
        let dateStart = filter_dateStart === ''? moment(filter_dateStart).tz('Asia/Bangkok').format('YYYY-MM-DD') : '';
        let dateEnd = filter_dateEnd === '' ? moment(filter_dateEnd).tz('Asia/Bangkok').format('YYYY-MM-DD') : '';
    
        let FormatDataDay = { total: 0 }
        let FormatDataTime = {
          TimeRange1: 0, // 06:00 - 09:00,
          TimeRange2: 0, // 09:00 - 12:00,
          TimeRange3: 0, // 12:00 - 15:00,
          TimeRange4: 0, // 15:00 - 18:00,
          TimeRange5: 0, // 18:00 - 21:00,
          TimeRange6: 0, // 21:00 - 24:00,
          TimeRange7: 0, // 00:00 - 03:00,
          TimeRange8: 0 // 03:00 - 06:00
        }
        let formatTimePeriod = {Day:0, Night:0}
        let dataEx = {
            deviceInfo:{},
            getDay:{labels:[], dataset:[]},
            getTime:{labels:['06:00 - 09:00', '09:00 - 12:00', '12:00 - 15:00', '15:00 - 18:00', '18:00 - 21:00', '21:00 - 24:00', '00:00 - 03:00', '03:00 - 06:00'], dataset:[]},
            getTimePeriod:{dataset:[]}
        }
    
    
        let responseDataGet = await  fetch( 'https://script.google.com/macros/s/AKfycbxMioYOWhkiyVSAKgRcSVyVNmXr5YUXzTPakbeQzv6sO-7xJ2wY6k0eB9IRvI0lCkbe/exec')
        if (!responseDataGet.ok) {
          throw new Error('Network response was not ok');
        }
    
        let response = await responseDataGet.json();

        var query_dvInfo = response.filter((val) => {
              return val.DvID === filter_DeviceID;
            });
        
        query_dvInfo.forEach((val)=>{
            dataEx.deviceInfo.DvID = val.DvID;
            dataEx.deviceInfo.address = val.address
        })
        
    
        if(filter_dateStart === '' || filter_dateEnd === ''){
          if (filter_TimePeriod === 'All'){
            var query = response.filter((val) => {
              return val.DvID === filter_DeviceID;
            });
          
            query.forEach(async (val) => {
              // timePeriod
              if(val.timePeriod === 'Night'){
                formatTimePeriod.Night += 1
              } 
              else{
                formatTimePeriod.Day += 1
              }
              
              // get DeviceInfo
                let dateLabel_DvInfo = moment(val.date).tz('Asia/Bangkok').format('YYYY-MMM-DD')
                if (!dataEx.getDay.labels.includes(dateLabel_DvInfo)) {
                  dataEx.getDay.labels.push(dateLabel_DvInfo);
                }
      
                if (FormatDataDay.hasOwnProperty(dateLabel_DvInfo)) {
                    FormatDataDay[dateLabel_DvInfo] += 1
                    FormatDataDay.total += 1
                }
                else {
                    FormatDataDay[dateLabel_DvInfo] = 1
                    FormatDataDay.total += 1
                }
      
      
              // device time
      
              let timeVal = moment(val.time, 'HH:mm').tz('Asia/Bangkok').format('HH:mm')
              if (timeVal > moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('09:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange1 += 1
              }
      
              else if (timeVal > moment('09:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange2 += 1
              }
      
              else if (timeVal > moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('15:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange3 += 1
              }
      
              else if (timeVal > moment('15:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange4 += 1
              }
      
              else if (timeVal > moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('21:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange5 += 1
              }
      
              else if (timeVal > moment('21:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('23:59', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange6 += 1
              }
      
              else if (timeVal >= moment('24:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('03:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange7 += 1
              }
      
              else if (timeVal > moment('03:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange8 += 1
              }
    
              // timeperiod
              
      
            })
      
            dataEx.getDay.labels.forEach((val) => {
                   dataEx.getDay.dataset.push(FormatDataDay[val])
              });
            for(let i = 1; i <= dataEx.getTime.labels.length; i++){
                  dataEx.getTime.dataset.push(FormatDataTime['TimeRange'+i])
                // console.log(FormatDataTime['TimeRange'+i])
              }
            }
    
          else{
            var query = response.filter((val) => {
              return val.timePeriod === filter_TimePeriod && val.DvID === filter_DeviceID;
            });
    
            query.forEach(async (val) => {
              // timePeriod
              if(val.timePeriod === 'Night'){
                formatTimePeriod.Night += 1
              } 
              else{
                formatTimePeriod.Day += 1
              }
              
              // get DeviceInfo
                let dateLabel_DvInfo = moment(val.date).tz('Asia/Bangkok').format('YYYY-MMM-DD')
                if (!dataEx.getDay.labels.includes(dateLabel_DvInfo)) {
                  dataEx.getDay.labels.push(dateLabel_DvInfo);
                }
    
                if (FormatDataDay.hasOwnProperty(dateLabel_DvInfo)) {
                    FormatDataDay[dateLabel_DvInfo] += 1
                    FormatDataDay.total += 1
                }
                else {
                    FormatDataDay[dateLabel_DvInfo] = 1
                    FormatDataDay.total += 1
                }
    
    
              // device time
    
              let timeVal = moment(val.time, 'HH:mm').tz('Asia/Bangkok').format('HH:mm')
              if (timeVal > moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('09:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange1 += 1
              }
    
              else if (timeVal > moment('09:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange2 += 1
              }
    
              else if (timeVal > moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('15:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange3 += 1
              }
    
              else if (timeVal > moment('15:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange4 += 1
              }
    
              else if (timeVal > moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('21:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange5 += 1
              }
    
              else if (timeVal > moment('21:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('23:59', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange6 += 1
              }
    
              else if (timeVal >= moment('24:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('03:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange7 += 1
              }
    
              else if (timeVal > moment('03:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange8 += 1
              }
    
            })
    
            dataEx.getDay.labels.forEach((val) => {
                   dataEx.getDay.dataset.push(FormatDataDay[val])
              });
            for(let i = 1; i <= dataEx.getTime.labels.length; i++){
                  dataEx.getTime.dataset.push(FormatDataTime['TimeRange'+i])
                // console.log(FormatDataTime['TimeRange'+i])
              }
    
            
          }
        }
    
        else{
          if (filter_TimePeriod === 'All'){
            var query = response.filter((val) => {
              return val.DvID === filter_DeviceID;
            });
          query.forEach(async (val) => {
            // timePeriod
            if(val.timePeriod === 'Night'){
              formatTimePeriod.Night += 1
            } 
            else{
              formatTimePeriod.Day += 1
            }
            // get DeviceInfo
              let dateLabel_DvInfo = moment(val.date).tz('Asia/Bangkok').format('YYYY-MMM-DD')
              if (!dataEx.getDay.labels.includes(dateLabel_DvInfo)) {
                dataEx.getDay.labels.push(dateLabel_DvInfo);
              }
    
              if (FormatDataDay.hasOwnProperty(dateLabel_DvInfo)) {
                  FormatDataDay[dateLabel_DvInfo] += 1
                  FormatDataDay.total += 1
              }
              else {
                  FormatDataDay[dateLabel_DvInfo] = 1
                  FormatDataDay.total += 1
              }
            
          
            // device time
    
            let timeVal = moment(val.time, 'HH:mm').tz('Asia/Bangkok').format('HH:mm')
            if (timeVal > moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('09:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                FormatDataTime.TimeRange1 += 1
            }
    
            else if (timeVal > moment('09:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                FormatDataTime.TimeRange2 += 1
            }
    
            else if (timeVal > moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('15:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                FormatDataTime.TimeRange3 += 1
            }
    
            else if (timeVal > moment('15:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                FormatDataTime.TimeRange4 += 1
            }
    
            else if (timeVal > moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('21:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                FormatDataTime.TimeRange5 += 1
            }
    
            else if (timeVal > moment('21:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('23:59', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                FormatDataTime.TimeRange6 += 1
            }
    
            else if (timeVal >= moment('24:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('03:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                FormatDataTime.TimeRange7 += 1
            }
    
            else if (timeVal > moment('03:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                FormatDataTime.TimeRange8 += 1
            }
    
          })
    
          dataEx.getDay.labels.forEach((val) => {
                 dataEx.getDay.dataset.push(FormatDataDay[val])
            });
          for(let i = 1; i <= dataEx.getTime.labels.length; i++){
                dataEx.getTime.dataset.push(FormatDataTime['TimeRange'+i])
              // console.log(FormatDataTime['TimeRange'+i])
            }
          }
    
          else{
            var query = response.filter((val) => {
              return val.timePeriod === filter_TimePeriod && val.DvID === filter_DeviceID;
            });
    
            query.forEach(async (val) => {
              // timePeriod
              if(val.timePeriod === 'Night'){
                formatTimePeriod.Night += 1
              } 
              else{
                formatTimePeriod.Day += 1
              }
              // get DeviceInfo
                let dateLabel_DvInfo = moment(val.date).tz('Asia/Bangkok').format('YYYY-MMM-DD')
                if (!dataEx.getDay.labels.includes(dateLabel_DvInfo)) {
                  dataEx.getDay.labels.push(dateLabel_DvInfo);
                }
    
                if (FormatDataDay.hasOwnProperty(dateLabel_DvInfo)) {
                    FormatDataDay[dateLabel_DvInfo] += 1
                    FormatDataDay.total += 1
                }
                else {
                    FormatDataDay[dateLabel_DvInfo] = 1
                    FormatDataDay.total += 1
                }
    
    
              // device time
    
              let timeVal = moment(val.time, 'HH:mm').tz('Asia/Bangkok').format('HH:mm')
              if (timeVal > moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('09:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange1 += 1
              }
    
              else if (timeVal > moment('09:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange2 += 1
              }
    
              else if (timeVal > moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('15:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange3 += 1
              }
    
              else if (timeVal > moment('15:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange4 += 1
              }
    
              else if (timeVal > moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('21:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange5 += 1
              }
    
              else if (timeVal > moment('21:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('23:59', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange6 += 1
              }
    
              else if (timeVal >= moment('24:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('03:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange7 += 1
              }
    
              else if (timeVal > moment('03:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') && timeVal <= moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')) {
                  FormatDataTime.TimeRange8 += 1
              }
    
            })
    
            dataEx.getDay.labels.forEach((val) => {
                   dataEx.getDay.dataset.push(FormatDataDay[val])
              });
            for(let i = 1; i <= dataEx.getTime.labels.length; i++){
                  dataEx.getTime.dataset.push(FormatDataTime['TimeRange'+i])
                // console.log(FormatDataTime['TimeRange'+i])
              }
    
    
          }
        }
    
        ['Day','Night'].forEach((val) => {
          dataEx.getTimePeriod.dataset.push(formatTimePeriod[val])
        } )
      
    
    
        // ==============================================================================================================
    
        // let secLev = setSecuritylevel(dataEx.getTime)
      
      
      
        return dataEx;
      }

  // ฟังก์ชันที่ใช้ในการรับค่าจาก Maps component
  function handleDataListAll(value) {
    DataListAll = value;
  }

  function handleDataListAllStatusLoad(value) {
    DataListAllStatusLoad = value;
  }

function handleletCurrantMaps(value) {
    currantMaps = value;
  }
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
             document.getElementById('btn-filter').removeAttribute('disabled')
        } else{
            document.getElementById('btn-filter').setAttribute('disabled', '')
            alert('The end date must be greater than the start date.')
            event.target.id === 'filter-DateSt' ? stDate = '' :  endDate = ''
        }
       
    } 
  }

async function filterData() {
    DataListAllStatusLoad = false
    let Datafilter;
    if((stDate !== undefined && stDate !== null && stDate !== '' ) && (endDate !== undefined && endDate !== null && endDate !== '')){
        if( Day && Night){
        Datafilter = await getAllData(stDate, endDate, 'All', currantMaps)
        } else if(Day){
         Datafilter = await getAllData(stDate, endDate, 'Day', currantMaps)
        }else if(Night){
         Datafilter = await getAllData(stDate, endDate, 'Night', currantMaps)
        }
    
    }else{
        if( !Day && !Night){
           DataListAllStatusLoad = Error
        } else{
            if(Day && Night){
                 Datafilter = await getAllData('', '', 'All', currantMaps)
            }else if(Day){
            Datafilter = await getAllData('', '', 'Day', currantMaps)
            }else if(Night){
            Datafilter = await getAllData('', '', 'Night', currantMaps)
            }
            DataListAll = [Datafilter]
            DataListAllStatusLoad = true
        }
        
        

            
        
    }
    

    
    console.log(DataListAll);


    
}

</script>

<div class="w-full flex  justify-center gap-4">
   
    <section class="w-6/12 min-h-[43rem] bg-white p-[0.5rem] rounded-[10px] shadow-[0_0_5px_.5px_#7f7f7fb3]">
        <h2 class="flex items-center text-3xl text-red-700 font-bold">
            <img
                src="https://cdn-icons-png.flaticon.com/128/3425/3425073.png" alt=""
                class="w-[30px]">
                detection location map
        </h2>
        <Maps  
        bind:setLavalSce={setLavalSce}
        bind:DataListAll={DataListAll}
        bind:DataListAllStatusLoad={DataListAllStatusLoad}
        bind:currantMaps={currantMaps}
        on:DataListAll={handleDataListAll}
        on:currantMaps={handleletCurrantMaps}
        on:DataListAllStatusLoad={handleDataListAllStatusLoad}
        />
    </section>

    <section class="w-6/12 min-h-[43rem] flex flex-col  bg-white p-[0.5rem] rounded-[10px] shadow-[0_0_5px_.5px_#7f7f7fb3]">
        <div class="flex justify-between items-center">
            <h2 class="flex items-center text-3xl text-[#216b99] font-bold">
                <img
                    src="https://cdn-icons-png.flaticon.com/128/9195/9195785.png" alt=""
                    class="w-[30px]">
                information
            </h2>
            <!-- <button class="shadow-[1px_1px_3px_.5px_#7f7f7fb3] p-[.25rem] rounded-[.25rem]" on:click={()=>{cleckFilter = !cleckFilter}}><img src="https://cdn-icons-png.flaticon.com/128/10833/10833610.png" alt="" class="w-[30px]"></button>
      -->  </div> 
       


        {#if DataListAllStatusLoad === null}
        <div class="h-[40rem] flex flex-col items-center justify-center">
            <div class="w-[25rem] h-[11rem] flex flex-col items-center justify-between text-center bg-amber-100 border-amber-400 border-[2px] p-4 rounded-[.5rem] ">
               <img src="https://cdn.pixabay.com/animation/2023/04/28/18/34/18-34-10-554_512.gif" alt="" class="w-[50px]"> 
               <h1 class="text-center text-3xl font-bold ">
                    
                    Not information! <br>  
                    
                </h1>
                <h1 class="flex items-center justify-center text-[18px] font-bold">
                    please click 
                    <div class="relative bg-stone-50 rounded-[.25rem]  before:contents-[''] ml-2 mr-2 p-[0.5rem]] before:absolute before:w-[.75rem] before:h-[.7rem] before:bg-[#B41412] before:top-[7px] before:left-[10px] before:z-0">
                        <img src="img/location-pin.png" alt="" class="w-[35px] relative z-1">
                    </div> 
                    on map
                </h1>
            </div>
            
        </div>
            
            
        {:else if DataListAllStatusLoad === false}
            <div class="w-full h-full flex gap-1 justify-center items-center">
                <span class="relative flex h-[2rem] w-[2rem]">
                    <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-sky-400 opacity-75"></span>
                    <span class="relative inline-flex rounded-full h-[2rem] w-[2rem] bg-sky-500"></span>
                
                </span>
                <div class="text-2xl">loading...</div>
            </div>
        {:else if DataListAllStatusLoad === true}
            <h1>loaded!</h1>
            <div class=" bg-[#ebebeb] p-[.5rem] w-[100%] rounded-[.5rem] border-b-[2px] border-b-[#468999] text-[#686868]  groupFilter-item">
                <div id="group-filterDate" class="flex justify-between gap-1">
                    <div class="flex flex-col">
                        <label for="filter-DateSt">Date start:</label>
                        <input type="date" name="filter-DateSt" id="filter-DateSt" class="w-[11.6rem] h-[30px] outline-none text-[14px] border-[#468999] border-[2px] rounded-[5px]" on:change={useFucnCheckDate} bind:value={stDate}>
                    </div>

                    <div class="flex flex-col">
                        <label for="filter-DateEnd">Date end:</label>
                        <input type="date" name="filter-DateEnd" id="filter-DateEnd" class="w-[11.6rem] h-[30px] outline-none text-[14px] border-[#468999] border-[2px] rounded-[5px]" on:change={useFucnCheckDate} bind:value={endDate}>
                    </div>

                    <div class="flex flex-col ">
                        <label for="">timePr</label>
                        <div class="flex">
                            <label for="filter-day" class={Day ? "bg-[#F3CF29] text-[#468999] p-[0.25rem] cursor-pointer ml-[0.5rem] rounded-[.25rem]":"bg-white p-[0.25rem] cursor-pointer  rounded-[.25rem]"}>
                                <input type="checkbox" name="filter-timePr" id="filter-day" value="Day"
                                    style="display: none;" bind:checked={Day} on:change={()=>{
                                        if((stDate === '' || stDate === undefined || stDate === null || stDate !== '') && (endDate === '' || endDate === undefined || endDate === null || endDate !== '') || (Day || Night)){
                                            document.getElementById('btn-filter').removeAttribute('disabled')
                                        }else{
                                            document.getElementById('btn-filter').setAttribute('disabled', '')
                                        }
                                    }}>
                                Day
                            </label>

                            <label for="filter-night" class={Night ? "bg-[#F3CF29] text-[#468999] p-[0.25rem] cursor-pointer  rounded-[.25rem]":"bg-white p-[0.25rem] cursor-pointer ml-[0.5rem] rounded-[.25rem]"}>
                                <input type="checkbox" name="filter-timePr" id="filter-night" value="Night"
                                    style="display: none;" bind:checked={Night} on:change={()=>{
                                        if((stDate === '' || stDate === undefined || stDate === null || stDate !== '') && (endDate === '' || endDate === undefined || endDate === null || endDate !== '') || (Day || Night)){
                                            document.getElementById('btn-filter').removeAttribute('disabled')
                                        }else{
                                            document.getElementById('btn-filter').setAttribute('disabled', '')
                                        }
                                    }}>
                                Night
                            </label>
                            
                        </div>

                       
                    </div>
                    <div class="flex flex-col gap-[0.2rem]">
                        <button class="w-[4.3rem] bg-[#468999] ml-2 p-[0.25rem] cursor-pointer rounded-[.25rem] text-white disabled:bg-gray-400 disabled:text-gray-200 disabled:cursor-default" id="btn-filter" disabled on:click={filterData}>ค้นหา</button>
                        <button type="reset" class="w-[4.3rem] bg-[#ffffff] ml-2 p-[0.25rem] cursor-pointer rounded-[.25rem] text-[#468999] border-[#468999] border-[1px] disabled:bg-gray-400 disabled:text-gray-200 disabled:cursor-default" on:click={()=>{
                            Night = false
                            Day = false
                            endDate = ''
                            stDate = ''
                        }}>reset</button>
                    </div>
                    
                </div>


            </div>

            {#each DataListAll as data }
               <div class="w-full flex gap-2 mt-2">
                <div class="w-6/12 flex flex-col gap-2">
                    <div class="bg-white border-[3px] border-[#b1b1b1] rounded-[.5rem] p-2">
                        <h1 class="text-xl font-bold">device infomation</h1>
                        <p><span class="text-[16px] font-bold mr-1">Device ID:</span>{data.deviceInfo.DvID}</p>
                        <p><span class="text-[16px] font-bold mr-1">address:</span>{data.deviceInfo.address}</p>
                    </div>

                    <div class="bg-white border-[3px] border-[#b1b1b1] rounded-[.5rem] p-2">
                        <h1 class="text-xl font-bold">Security level</h1>
                        <Predition dataset={setLavalSce} />
                    </div>
                </div>

                <div class="w-6/12 bg-white border-[3px] border-[#b1b1b1] rounded-[.25rem] p-2">
                    <h1 class="text-xl font-bold">TimePeriod</h1>
                    <TimePeriod chartValues={data.getTimePeriod.dataset} />
                </div>
                    
                </div> 

                <!-- getTime: {labels: Array(8), dataset: Array(8)}
                getDay: {labels: Array(46), dataset: Array(46)}
-->
                <div class="w-full bg-white border-[3px] border-[#b1b1b1] rounded-[.5rem] p-2 mt-2">
                    <h1 class="text-xl font-bold">chart day</h1>
                    <DayChart chartLabels={data.getDay.labels} chartValues={data.getDay.dataset} />
                </div>
                
                <div class="w-full bg-white border-[3px] border-[#b1b1b1] rounded-[.5rem] p-2 mt-2">
                    <h1 class="text-xl font-bold">chart time</h1>
                    <TimeChart chartLabels={data.getTime.labels} chartValues={data.getTime.dataset} />
                </div>

            {/each}
           
         

        
            
        {:else if DataListAllStatusLoad === Error }
        <div class="h-[40rem] flex flex-col items-center justify-center">
            <div class="w-[35rem] h-[15rem] flex flex-col items-center justify-between text-center bg-rose-100 border-rose-600 border-[2px] p-4 rounded-[.5rem] ">
               <img src="https://assets-v2.lottiefiles.com/a/b5641ed8-1152-11ee-ada0-8f4e8e17569e/Y6ez38CH2M.gif" alt="" class="w-[80px]"> 
               <h1 class="text-center text-3xl font-bold text-rose-700">
                    
                An error occurred. <br>  
                    
                </h1>
                <h1 class="flex items-center justify-center text-[18px] font-bold">
                    Please enter start and end dates. or choose a time period
                </h1>
                <h1 class="flex items-center justify-center text-[18px] font-bold">
                    click 
                    <div class="relative bg-stone-50 rounded-[.25rem]  before:contents-[''] ml-2 mr-2 p-[0.5rem]] before:absolute before:w-[.75rem] before:h-[.7rem] before:bg-[#B41412] before:top-[7px] before:left-[10px] before:z-0">
                        <img src="img/location-pin.png" alt="" class="w-[35px] relative z-1">
                    </div> 
                    on map to visit device information.
                </h1>
            </div>
            
        </div>
        {/if}

    </section>

</div>
