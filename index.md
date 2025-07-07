<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Kusto Query Language Hunting Generator</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #ffffff;
      color: #000000;
      transition: background-color 0.3s, color 0.3s;
    }

    body.dark {
      background-color: #121212;
      color: #f0f0f0;
    }

    .container {
      width: 90%;
      max-width: 600px;
      margin: 40px auto;
    }

    h1, h3, h6 {
      text-align: center;
    }

    .label {
      font-weight: bold;
      margin: 15px 0 5px;
    }

    .multi-select, .select, input[type="text"] {
      width: 100%;
      padding: 8px;
      box-sizing: border-box;
    }

    .code-box {
      border: 1px solid #ccc;
      background-color: #f5f5f5;
      padding: 0;
      white-space: pre-wrap;
      overflow-wrap: break-word;
      transition: all 0.3s ease-in-out;
      opacity: 0;
      max-height: 0;
      overflow: hidden;
    }

    body.dark .code-box {
      background-color: #1e1e1e;
      border-color: #444;
    }

    .code-box.show {
      opacity: 1;
      padding: 10px;
      max-height: 1000px;
    }

    button {
      padding: 10px;
      margin-top: 10px;
      cursor: pointer;
      width: 100%;
    }

    .dark-toggle {
      margin-top: 20px;
      text-align: center;
    }

    a {
      display: block;
      text-align: center;
      margin-top: 20px;
    }
  </style>

  <script>
    function generateQuery() {
      const Tables = document.getElementById("tables").value;
      const timeRange = document.getElementById("time-range").value;
      const selectedProcesses = document.getElementById("processes").value;
      const selectedAccount = document.getElementById("account-name").value;
      const selectedDevice = document.getElementById("device-name").value;
      const selectedintIP = document.getElementById("intIP").value;
      const selectedextIP = document.getElementById("extIP").value;
      const selectedUrl = document.getElementById("url").value;
      const selectedCommands = getSelectedValues("commands");
      const selectedColumns = getSelectedValues("columns");
      const selectedTop = getSelectedValues("top");

      let query = `search "${Tables}"`;
      if (timeRange !== "") {
        query += ` | where Timestamp > ago(${timeRange})`;
      }

      if (selectedProcesses !== "") {
        const processList = selectedProcesses.split(',').map(p => p.trim()).filter(p => p);
        query += ` | where FileName in~ ("${processList.join('", "')}")`;
      }

      if (selectedCommands.length > 0) {
        query += ` | where ProcessCommandLine has_any ("${selectedCommands.join('", "')}")`;
      }

      if (selectedAccount !== "") {
        query += ` | where AccountName contains "${selectedAccount}"`;
      }

      if (selectedDevice !== "") {
        query += ` | where DeviceName contains "${selectedDevice}"`;
      }

      if (selectedintIP !== "") {
        query += ` | where LocalIP contains "${selectedintIP}"`;
      }

      if (selectedextIP !== "") {
        query += ` | where RemoteIP contains "${selectedextIP}"`;
      }

      if (selectedUrl !== "") {
        query += ` | where RemoteUrl contains "${selectedUrl}"`;
      }

      if (selectedColumns != "") {
        query += selectedColumns.join(", ");
      }

      if (selectedTop != "") {
        query += ` | top ${selectedTop.join(", ")} by Timestamp`;
      }

      const output = document.getElementById("output");
      output.textContent = query;
      output.classList.add("show");
    }

    function getSelectedValues(selectId) {
      const select = document.getElementById(selectId);
      const selectedValues = [];
      for (let i = 0; i < select.options.length; i++) {
        if (select.options[i].selected) {
          selectedValues.push(select.options[i].value);
        }
      }
      return selectedValues;
    }

    function copyToClipboard() {
      const text = document.getElementById("output").textContent;
      navigator.clipboard.writeText(text).then(() => {
        alert("Query copied to clipboard!");
      });
    }

    function toggleDark() {
      document.body.classList.toggle("dark");
    }
  </script>
</head>

<body>
  <div class="container">
    <h1>KQL Hunting Query Generator</h1>
    <h3>Microsoft Defender XDR focussed.</h3>
    <h6 style="color: #FF0000;">Input fields are optional, if left blank they are ignored.</h6>

    <div class="label">1. Enter search:</div>
    <input type="text" id="tables" value="*" />

    <div class="label">2. Set Time Range:</div>
    <input type="text" id="time-range" placeholder="e.g., 1d" />

    <div class="label">3. Enter user account name:</div>
    <input type="text" id="account-name" placeholder="e.g., administrator" />

    <div class="label">4. Enter device name:</div>
    <input type="text" id="device-name" placeholder="e.g., DESKTOP-777" />

    <div class="label">5. Enter source IP:</div>
    <input type="text" id="intIP" placeholder="<Local IP>" />

    <div class="label">6. Enter destination IP:</div>
    <input type="text" id="extIP" placeholder="<Remote IP>" />

    <div class="label">7. Enter url:</div>
    <input type="text" id="url" placeholder="<domain name>" />

    <div class="label">8.1. Enter file name(s):</div>
    <input type="text" id="processes" placeholder="e.g., powershell.exe, cmd.exe" oninput="generateQuery()" />

    <label class="label">8.2. Select Command-line arguments:</label>
    <select class="multi-select" id="commands" multiple>
      <option value="DownloadFile">DownloadFile</option>
      <option value="DownloadData">DownloadData</option>
      <option value="DownloadString">DownloadString</option>
      <option value="WebRequest">WebRequest</option>
      <option value="WebClient">WebClient</option>
      <option value="Invoke-Item">Invoke-Item</option>
      <option value="Invoke-WebRequest">Invoke-WebRequest</option>
      <option value="Invoke-Expression">Invoke-Expression</option>
      <option value="Start-Process">Start-Process</option>
      <option value="Out-File">Out-File</option>
      <option value="Hidden">Hidden</option>
      <option value="Shellcode">Shellcode</option>
      <option value="http">http</option>
      <option value="https">https</option>
    </select>

    <label class="label">9. Select project Column mode:</label>
    <select class="select" id="columns">
      <option value="">All</option>
      <option value=" | project $table,Timestamp,AccountName,AccountUpn,DeviceName,LocalIP,RemoteIP,Port,FileName,FolderPath,ProcessCommandLine,DestinationDeviceName,DestinationIPAddress,DestinationPort,RemoteUrl,ActionType,LogonType,ProcessVersionInfoOriginalFileName,ProcessId,ProcessTokenElevation,ProcessCreationTime,LogonId,SHA1,SHA256,FileSize,InitiatingProcessAccountName,InitiatingProcessFileName,InitiatingProcessFolderPath,InitiatingProcessCreationTime">Network mode</option>
    </select>

    <label class="label">10. Select the maximum result limit:</label>
    <select class="select" id="top">
      <option value="200">200</option>
      <option value="50">50</option>
      <option value="100">100</option>
      <option value="300">300</option>
      <option value="500">500</option>
      <option value="1000">1000</option>
    </select>

    <button onclick="generateQuery()">Generate Query</button>
    <button onclick="copyToClipboard()">Copy to Clipboard</button>

    <div class="code-box" id="output"></div>

    <div class="dark-toggle">
      <label><input type="checkbox" onchange="toggleDark()" /> Enable Dark Mode</label>
    </div>

    <a href="https://twitter.com/b41ss">@b41ss</a>
  </div>
</body>
</html>
