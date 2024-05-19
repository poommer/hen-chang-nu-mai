<script >
    import moment from 'moment-timezone';

    
    import { onMount } from 'svelte';
    (g => { var h, a, k, p = "The Google Maps JavaScript API", c = "google", l = "importLibrary", q = "__ib__", m = document, b = window; b = b[c] || (b[c] = {}); var d = b.maps || (b.maps = {}), r = new Set, e = new URLSearchParams, u = () => h || (h = new Promise(async (f, n) => { await (a = m.createElement("script")); e.set("libraries", [...r] + ""); for (k in g) e.set(k.replace(/[A-Z]/g, t => "_" + t[0].toLowerCase()), g[k]); e.set("callback", c + ".maps." + q); a.src = `https://maps.${c}apis.com/maps/api/js?` + e; d[q] = f; a.onerror = () => h = n(Error(p + " could not load.")); a.nonce = m.querySelector("script[nonce]")?.nonce || ""; m.head.append(a) })); d[l] ? console.warn(p + " only loads once. Ignoring:", g) : d[l] = (f, ...n) => r.add(f) && u().then(() => d[l](f, ...n)) })
        ({ key: "AIzaSyCtu4ay6A9yIe35MSAJ3Jr1p07S1DvgiZ0", v: "weekly" });
    
    
    let map;
    export let currantMaps = null
    export let DataListAll = []
    export let DataListAllStatusLoad = null ;
    export let setLavalSce;
    
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

    function setprogress(dataAA){

    let now = moment().tz('Asia/Bangkok').format('HH:mm')
    let data = dataAA.dataset
    let presenDD = 0
    let rangeT = null

    let sumData = 0;
    data.forEach((val)=>{
    sumData += val
    })

    let persen = data.map((val) => {
    return (val*100)/sumData
    })

    if(
    now >= moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') &&
    now < moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')
    ) {
    presenDD = persen[0]+persen[1] ;
    rangeT = '06:00 - 12:00'
    }

    else if(
    now >= moment('12:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') &&
    now < moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')
    ) {
    presenDD = persen[2]+persen[3] ;
    rangeT = '12:00 - 18:00'
    }

    else if(
    now >= moment('18:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') &&
    now <= moment('23:59', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')
    ) {
    presenDD = persen[4] + persen[5];
    rangeT = '18:00 - 24:00'
    }

    else if(
    now >= moment('24:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm') &&
    now < moment('06:00', 'HH:mm').tz('Asia/Bangkok').format('HH:mm')
    ) {
    presenDD = persen[6] + persen[7] ;
    rangeT = '24:00 - 06:00'
    }

    if(presenDD <= 33.33){
    return {persen:presenDD, Leval:'safe', rangeT:rangeT}
    } else if(presenDD > 33.33 && presenDD <= 66.66){
    return {persen:presenDD, Leval:'warning', rangeT:rangeT}
    } else{
    return {persen:presenDD, Leval:'danger', rangeT:rangeT}
    }

    }
    
    async function initMap() {
      // The location of Uluru
      const position = {lat: 13.568249 , lng:101.509455 };
      // Request needed libraries.
      //@ts-ignore
      const { Map } = await google.maps.importLibrary("maps");
      const { AdvancedMarkerElement } = await google.maps.importLibrary("marker");
    
    
      // The map, centered at Uluru
      map = new Map(document.getElementById("map"), {
        zoom: 6.5,
        center: position,
        mapId: "802dff93db0ce925",
      });
    
    
      try {
        const response = await fetch('https://script.google.com/macros/s/AKfycby9vPBs9n5GyRhCXiUNcAypI1q5MfcDoRYPuU17miFmQwrYHqhRIhUuXDKZqNrZuwJX/exec');
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        const data = await response.json();
    
       
        data.forEach((val) => {
        let marker = new google.maps.Marker({
            position: val.pos,
            map: map,
            title: `${val.DvId} | ${val.address}`
        });
      
        marker.addListener('click',async  function() { 
            DataListAllStatusLoad = false
            currantMaps = val.DvId
            try {
        let data = await getAllData('', '', 'All', currantMaps);
        DataListAll = [data];
        DataListAllStatusLoad = true;
        console.log(DataListAll);
        setLavalSce = setprogress(DataListAll[0].getTime)
        console.log(setLavalSce);
    } catch (error) {
        console.log(error);
        DataListAllStatusLoad = 'Error';
    }
          })
       })
      }
    
    
    
    
    
     catch (error) {
        console.error('There was a problem with the fetch operation:', error);
      }
    
    }
    
    onMount(
        async () =>{
            initMap()
        }
    )
    
    </script>
    <div id="map" class="w-full h-[92%] mt-2"></div>