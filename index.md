<html>
<head>
	<title>Kusto Query Language Hunting Generator</title>
	<style>
		.container {
			width: 500px;
			margin: 50px auto;
			font-family: Arial, sans-serif;
		}

		h1 {
			text-align: center;
		}
		
		h3 {
			text-align: center;
		}
		h6 {
			text-align: center;
		}
		.label {
			font-weight: bold;
			margin-bottom: 10px;
		}

		.multi-select {
			width: 100%;
			height: 200px;
		}

		.code-box {
			border: 1px solid #ccc;
			background-color: #f5f5f5;
			padding: 10px;
			white-space: pre-wrap;
			overflow-wrap: break-word;
		}
	</style>
	<script>
		function generateQuery() {
			// Retrieve user-selected options
                        var Tables = document.getElementById("tables").value;
			var timeRange = document.getElementById("time-range").value;
			var selectedProcesses = document.getElementById("processes").value;
			var selectedAccount = document.getElementById("account-name").value;
			var selectedDevice = document.getElementById("device-name").value;
			var selectedintIP = document.getElementById("intIP").value;
			var selectedextIP = document.getElementById("extIP").value;
			var selectedUrl = document.getElementById("url").value;
			var selectedCommands = getSelectedValues("commands");
			var selectedColumns = getSelectedValues("columns");
			var selectedTop = getSelectedValues("top");

			// Generate the query
			var query = "search " + '"' + Tables + '"';
			if (timeRange !== "") {
				query += " | where Timestamp > ago(" + timeRange + ")";
			}
			if (selectedProcesses !== "") {
                                query += ' | where FileName in~ ' + '("' + selectedProcesses + '")';
			}
			if (selectedCommands.length > 0) {
				query += ' | where ProcessCommandLine has_any ("' + selectedCommands.join('", "') + '")';
			}
			if (selectedAccount !== "") {
                                query += ' | where AccountName contains ' + '"' + selectedAccount + '"';
			}
			if (selectedDevice !== "") {
                                query += ' | where DeviceName contains ' + '"' + selectedDevice + '"';
			}
			if (selectedintIP !== "") {
                                query += ' | where LocalIP contains ' + '"' + selectedintIP + '"';
			}
			if (selectedextIP !== "") {
                                query += ' | where RemoteIP contains ' + '"' + selectedextIP + '"';
			}
			if (selectedUrl !== "") {
                                query += ' | where RemoteUrl contains ' + '"' + selectedUrl + '"';
			}
			if (selectedColumns !== "") {
				query += selectedColumns.join(", ");
			}
			if (selectedTop !== "") {
				query += " | top " + selectedTop.join(", ") + " by Timestamp";
			}

			// Update the output code box
			document.getElementById("output").textContent = query;
		}

		function getSelectedValues(selectId) {
			var select = document.getElementById(selectId);
			var selectedValues = [];
			for (var i = 0; i < select.options.length; i++) {
				if (select.options[i].selected) {
					selectedValues.push(select.options[i].value);
				}
			}
			return selectedValues;
		}
	</script>
</head>
<body>
	<div class="container">
		<h1>KQL Hunting query Generator</h1>
		<h3>Microsoft 365 Defender focussed.</h3>
  		<h6>Input fields are optional, if left blank they are ignored.</h6>

		<div class="label">1. Enter search:</div>
		<input type="text" id="tables" placeholder="Can be any string search, default value is a wildcard." value="*"/>
<br>
<br>
		<div class="label">2. Set Time Range:</div>
		<input type="text" id="time-range" placeholder="e.g., 1d" />
<br>
<br>
		<div class="label">3. Enter user account name:</div>
		<input type=text id="account-name" placeholder="e.g., administrator"/>
<br>
<br>
		<div class="label">4. Enter device name:</div>
		<input type=text id="device-name" placeholder="e.g., DESKTOP-777"/>
<br>
<br>
		<div class="label">5. Enter source IP:</div>
		<input type=text id="intIP" placeholder="<Local IP>"/>
<br>
<br>
		<div class="label">6. Enter destination IP:</div>
		<input type=text id="extIP" placeholder="<Remote IP>"/>
<br>
<br>
		<div class="label">7. Enter url:</div>
		<input type=text id="url" placeholder="<domain name>"/>
<br>
<br>

		<div class="label">8.1. Enter process name:</div>
		<input type=text id="processes" placeholder="e.g., powershell.exe"/>
<br>
<br>
		<label class="label">8.2. Select Command-line strings:</label>
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
<option value="WebClient">WebClient</option>
<option value="Shellcode">Shellcode</option>
<option value="http">http</option>
<option value="https">https</option>
			<!-- Add more command options as needed -->
		</select>
<br>
<br>
		<label class="label">9. Select Result Column mode (project):</label>
<br>
	<select class="select" id="columns">
  	<option value=" | project $table,Timestamp,AccountName,AccountUpn,DeviceName,LocalIP,RemoteIP,Port,FileName,FolderPath,ProcessCommandLine,DestinationDeviceName,DestinationIPAddress,DestinationPort,RemoteUrl,ActionType,LogonType,ProcessVersionInfoOriginalFileName,ProcessId,ProcessTokenElevation,ProcessCreationTime,LogonId,SHA1,SHA256,FileSize,InitiatingProcessAccountName,InitiatingProcessFileName,InitiatingProcessFolderPath,InitiatingProcessCreationTime">Network mode</option>
	</select>
<br>
<br>
    <label class="label">10. Select the maximum result limit:</label>
<br>
		<select class="select" id="top">
		<option value="200">200</option>
		<option value="50">50</option>
		<option value="100">100</option>
		<option value="300">300</option>
		<option value="500">500</option>
		<option value="1000">1000</option>
		</select>
<br>
<br>
		<button onclick="generateQuery()">Generate Query</button>
<br>
<br>
		<div class="code-box" id="output"></div>
	</div>
<br>
<br>	
<a href="https://twitter.com/b41ss">@b41ss</a>	
</body>
</html>
