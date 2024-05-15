<script>
    import moment from 'moment-timezone';
    import FormExport from './formExport.svelte';
    export let jsonData;
    let statusLoad = false;
    let fileName =  `export-detection_${moment().tz('Asia/Bangkok').format('YYYY-MM-DD_HH-mm-ss')}`


function jsonToCsv(items) {
  const header = Object.keys(items[0]);
  const headerString = header.join(',');
  // handle null or undefined values here
  const replacer = (key, value) => value ?? '';
  const rowItems = items.map((row) =>
    header
      .map((fieldName) => JSON.stringify(row[fieldName], replacer))
      .join(',')
  );
  // join header and body, and break into separate lines
  const csv = [headerString, ...rowItems].join('\r\n');
  return csv;
}

function downloadJson() {
        const a = document.createElement("a");
		  const file = new Blob([JSON.stringify(jsonData)], { type:  "text/plain" });
		  a.href = URL.createObjectURL(file);
		  a.download = fileName+'.json';
		  a.click();
    //     //   download(content, fileName, contentType)
    // download(JSON.stringify(jsonData), "yourfile.json", "text/plain")
}

function downloadCSV() {
    let csvText = jsonToCsv(jsonData)
        const a = document.createElement("a");
		  const file = new Blob([csvText], { type:  "text/plain" });
		  a.href = URL.createObjectURL(file);
		  a.download = fileName+'.csv';
		  a.click();
    //     //   download(content, fileName, contentType)
    // download(JSON.stringify(jsonData), "yourfile.json", "text/plain")
}

function downloadExcel() {
    let csvText = jsonToCsv(jsonData)
        const a = document.createElement("a");
		  const file = new Blob([csvText], { type:  "text/plain" });
		  a.href = URL.createObjectURL(file);
		  a.download = 'yourfile.xlsx';
		  a.click();
    //     //   download(content, fileName, contentType)
    // download(JSON.stringify(jsonData), "yourfile.json", "text/plain")
}





// console.log(jsonToCsv(jsonData));
</script>


    <button on:click={downloadJson}  class="w-[5rem] p-1 cursor-pointer border-2 border-neutral-600 rounded ">JSON</button>
    <button on:click={downloadCSV} class="w-[5rem] p-1 cursor-pointer border-2 border-neutral-600 rounded">CSV</button>
    <button on:click={downloadExcel} class="w-[5rem] p-1 cursor-pointer border-2 border-neutral-600 rounded" disabled>Excel</button>

    <!-- <FormExport /> -->