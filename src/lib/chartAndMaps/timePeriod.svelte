<script>
    import Chart from 'chart.js/auto';
    import { onMount } from 'svelte';
  
    export let chartValues = [1, 1];
    let ctx;
  
    onMount(() => {
      const canvas = document.getElementById('ChartsPeriod');
      ctx = canvas.getContext('2d');
      var chart = new Chart(ctx, {
        type: 'pie',
        data:  {
          labels: [
            'Day',
            'Night'
          ],
          datasets: [{
            label: ' detected',
            data: chartValues,
            backgroundColor: [
              'rgb(255, 205, 86)',
              'rgb(54, 162, 235)'
            ],
            hoverOffset: 2
          }]
        },
        options: {
          width:210,
          height:210
        }
      });
    });
  </script>
<div class="relative w-[100%] h-[80%]  flex items-center justify-center ">
  <canvas id="ChartsPeriod"></canvas>
</div>

