<script>
    import Chart from 'chart.js/auto';
    import { onMount } from 'svelte';
  
    export let chartValues = [1, 1, 1, 1, 1, 1, 6, 2, 3, 2, 1];
    export let chartLabels = ['02-Apr', '05-Apr', '06-Apr', '07-Apr', '10-Apr', '14-Apr', '23-Apr', '26-Apr', '27-Apr', '29-Apr', '30-Apr'];
    let ctx;
  
    onMount(() => {
      const canvas = document.getElementById('myChartTime');
      ctx = canvas.getContext('2d');
      var chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: chartLabels,
          datasets: [{
            label: 'detected',
            data: chartValues
          }]
        },
        options: {
          indexAxis: 'x',
          // Elements options apply to all of the options unless overridden in a dataset
          // In this case, we are setting the border of each horizontal bar to be 2px wide
          elements: {
            bar: {
              borderWidth: 2,
            }
          },
          responsive: true,
          plugins: {
            legend: {
              position: 'right',
            }
          }
        }
      });
    });
  </script>


  <canvas id="myChartTime"></canvas>


