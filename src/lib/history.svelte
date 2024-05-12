<script>
    import { onMount } from 'svelte';
    let pageCur = 1;
    let dataList;
    let dataset = []
    let menuData;
    async function getData() { 
        console.log('loading data');
        const response = await fetch('https://script.google.com/macros/s/AKfycbxMioYOWhkiyVSAKgRcSVyVNmXr5YUXzTPakbeQzv6sO-7xJ2wY6k0eB9IRvI0lCkbe/exec');
        if (!response.ok) {
            throw new Error('Network response was not ok');
        }
    
       
        let data = await response.json();
        dataList = data
        console.log('loaded data');
    }



async function pagination(pagenum){

    if(dataList === undefined){
        await getData()
    }
    const page = parseInt(pagenum)
    const numberPage = 20;
    let totalPage =  dataList.length/20
    
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
     console.log(menuData)
     console.log(dataset);
     

}
let cateGroup = [] ;
pagination(pageCur)

.then(()=>{
    
    for(let i = 1; i<= menuData.totalPage; i++){
        
        cateGroup.push(i)

    }
    cateGroup = cateGroup
    console.log(cateGroup);
})



// {


// let start
// let end
// let menuAll = 29
// let coutMenu = menuAll/10
// coutMenu = coutMenu % 1 !== 0 ?  parseInt(coutMenu) + 1 : parseInt(coutMenu)

// let menucur = 1

  



// function chanegMenu(indexMenu) {

// let i = start
// let y = end
//  let menuNext =  menucur === coutMenu ? 0 : menucur + 1
// let menuPrev = menucur === 1 ? 0 : menucur - 1    
// if(menucur === coutMenu){
//     start = menucur !== 1 ? ((menucur - 1)* 10)+1 : menucur  ;
//     end = menuAll ;
// }else{
//     start = menucur !== 1 ? ((menucur - 1)* 10)+1 : menucur  ;
//     end = menucur !== 1 ? start + 9 : 10;
// } 

// while(i <= y){
//     console.log(i);
//     i++
// }

// console.log({ start, end,menuNext, menuPrev,menucur });
// }
//  }




</script>


<div>
    <table>
        <thead>
            <th>Timestamp</th>
            <th>device ID</th>
            <th>time period</th>
            <th>address</th>
        </thead>

        <tbody>
            {#each dataset as data }
                <tr>
                <td>{data.timestamp}</td>
                <td>{data.DvID}</td>
                <td>{data.timePeriod}</td>
                <td>{data.address}</td>
            </tr>
            {/each}
            
        </tbody>
    </table>
</div>

<div class="flex gap-2">
    {#each cateGroup as itemNum }
        <button class="box-prevPage bg-slate-500 text-white rounded-xl text-lg w-8 h-8" on:click={ () =>{
            pageCur = itemNum
            pagination(itemNum)
        }
        }>{itemNum}</button>
    {/each}

   </div>