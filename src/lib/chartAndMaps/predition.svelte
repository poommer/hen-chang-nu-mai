<script>
export let dataset;
export let Cssprogress = 'progress-bar height:40px; transition: width .5s ease-linear;';

// {persen: 19.696969696969695, Leval: 'safe', rangeT: '24:00 - 06:00'}

switch(dataset.Leval){
    case 'safe':
        Cssprogress = `height:40px; width:${parseInt(dataset.persen)}%; background-color:${'#089a00'}; transition: width .5s ease-linear;`
        break
    case 'warning':
        Cssprogress = `height:40px; width:${parseInt(dataset.persen)}%; background-color:${'#F3CF29'}; transition: width .5s ease-linear;`
        break
    case 'danger':
        Cssprogress = `height:40px; width:${parseInt(dataset.persen)}%; background-color:${'#c50000'}; transition: width .5s ease-linear;`
        break
    
}


</script>

<div class="flex flex-col justify-center gap-[.4rem]">
    <div class="w-full bg-[#dcdcdc] rounded-full overflow-hidden progress-container">
        <div class={Cssprogress} id="progress-bar" style={Cssprogress}></div>
    </div>
    <div class="flex justify-between items-center w-full">
        <small>safe</small>
        <small>warning</small>
        <small>danger</small>
    </div>
    <div class="flex justify-center font-bold text-[#468999]">
        {dataset.rangeT}, {parseInt(dataset.persen)}% were detected.
    </div>
</div>

<style>
    @keyframes progressAnimation {  
        from {width: 0px;}
        to {}
    }
</style>