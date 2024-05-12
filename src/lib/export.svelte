<script>
    let jsonData = [
{timestamp: '2023-07-20 00:56:00', date: '2023-07-20', time: '01:14:15', timePeriod: 'Night', DvID: 'Dv02'}, 
{timestamp: '2023-07-20 13:49:00', date: '2023-07-20', time: '14:07:45', timePeriod: 'Day', DvID: 'Dv01'},
{timestamp: '2023-08-02 09:15:00', date: '2023-08-02', time: '09:33:17', timePeriod: 'Day', DvID: 'Dv04'}, 
{timestamp: '2023-08-04 01:01:00', date: '2023-08-04', time: '01:19:14', timePeriod: 'Night', DvID: 'Dv04'},
{timestamp: '2023-08-05 16:32:00', date: '2023-08-05', time: '16:50:43', timePeriod: 'Day', DvID: 'Dv03'}, 
{timestamp: '2023-08-06 04:02:00', date: '2023-08-06', time: '04:20:48', timePeriod: 'Night', DvID: 'Dv04'},
{timestamp: '2023-08-06 16:34:00', date: '2023-08-06', time: '16:52:02', timePeriod: 'Day', DvID: 'Dv03'}, 
{timestamp: '2023-08-06 22:42:00', date: '2023-08-06', time: '23:00:05', timePeriod: 'Night', DvID: 'Dv03'},
{timestamp: '2023-08-07 19:24:00', date: '2023-08-07', time: '19:42:50', timePeriod: 'Night', DvID: 'Dv01'},
{timestamp: '2023-08-11 07:54:00', date: '2023-08-11', time: '08:12:04', timePeriod: 'Day', DvID: 'Dv01'},
{timestamp: '2023-08-11 16:52:00', date: '2023-08-11', time: '17:10:33', timePeriod: 'Day', DvID: 'Dv01'}
];

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
		  a.download = 'yourfile.json';
		  a.click();
    //     //   download(content, fileName, contentType)
    // download(JSON.stringify(jsonData), "yourfile.json", "text/plain")
}

function downloadCSV() {
    let csvText = jsonToCsv(jsonData)
        const a = document.createElement("a");
		  const file = new Blob([csvText], { type:  "text/plain" });
		  a.href = URL.createObjectURL(file);
		  a.download = 'yourfile.csv';
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
console.log(jsonToCsv(jsonData));
</script>

<div>
    <button on:click={downloadJson}>download Json</button>
    <button on:click={downloadCSV}>download CSV</button>
    <button on:click={downloadExcel}>download excel</button>
</div>