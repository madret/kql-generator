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
			var selectedTables = getSelectedValues("tables");
			var timeRange = document.getElementById("time-range").value;
			var selectedProcesses = document.getElementById("processes").value;
			var selectedCommands = getSelectedValues("commands");
			var selectedColumns = getSelectedValues("columns");
			var selectedTop = getSelectedValues("top");

			// Generate the query
			var query = "union " + selectedTables.join(", ");
			if (timeRange !== "") {
				query += " | where Timestamp > ago(" + timeRange + ")";
			}
			if (selectedProcesses !== "") {
                                query += ' | where FileName in~ ' + '("' + selectedProcesses + '")';
			}
			if (selectedCommands.length > 0) {
				query += ' | where ProcessCommandLine has_any ("' + selectedCommands.join('", "') + '")';
			}
			if (selectedColumns.length > 0) {
				query += " | project Timestamp, " + selectedColumns.join(", ");
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

		<div class="label">1. Select table(s):</div>
		<select class="multi-select" id="tables" multiple>
    <option value="AADSignInEventsBeta">AADSignInEventsBeta</option>
    <option value="AADSpnSignInEventsBeta">AADSpnSignInEventsBeta</option>
    <option value="AlertEvidence">AlertEvidence</option>
    <option value="AlertInfo">AlertInfo</option>
    <option value="BehaviorEntities">BehaviorEntities</option>
    <option value="BehaviorInfo">BehaviorInfo</option>
    <option value="CloudAppEvents">CloudAppEvents</option>
    <option value="DeviceEvents">DeviceEvents</option>
    <option value="DeviceFileCertificateInfo">DeviceFileCertificateInfo</option>
    <option value="DeviceFileEvents">DeviceFileEvents</option>
    <option value="DeviceImageLoadEvents">DeviceImageLoadEvents</option>
    <option value="DeviceInfo">DeviceInfo</option>
    <option value="DeviceLogonEvents">DeviceLogonEvents</option>
    <option value="DeviceNetworkEvents">DeviceNetworkEvents</option>
    <option value="DeviceNetworkInfo">DeviceNetworkInfo</option>
    <option value="DeviceProcessEvents">DeviceProcessEvents</option>
    <option value="DeviceRegistryEvents">DeviceRegistryEvents</option>
    <option value="DeviceTvmHardwareFirmware">DeviceTvmHardwareFirmware</option>
    <option value="DeviceTvmInfoGathering">DeviceTvmInfoGathering</option>
    <option value="DeviceTvmInfoGatheringKB">DeviceTvmInfoGatheringKB</option>
    <option value="DeviceTvmSecureConfigurationAssessment">DeviceTvmSecureConfigurationAssessment</option>
    <option value="DeviceTvmSecureConfigurationAssessmentKB">DeviceTvmSecureConfigurationAssessmentKB</option>
    <option value="DeviceTvmSoftwareEvidenceBeta">DeviceTvmSoftwareEvidenceBeta</option>
    <option value="DeviceTvmSoftwareInventory">DeviceTvmSoftwareInventory</option>
    <option value="DeviceTvmSoftwareVulnerabilities">DeviceTvmSoftwareVulnerabilities</option>
    <option value="DeviceTvmSoftwareVulnerabilitiesKB">DeviceTvmSoftwareVulnerabilitiesKB</option>
    <option value="EmailAttachmentInfo">EmailAttachmentInfo</option>
    <option value="EmailEvents">EmailEvents</option>
    <option value="EmailPostDeliveryEvents">EmailPostDeliveryEvents</option>
    <option value="EmailUrlInfo">EmailUrlInfo</option>
    <option value="IdentityDirectoryEvents">IdentityDirectoryEvents</option>
    <option value="IdentityInfo">IdentityInfo</option>
    <option value="IdentityLogonEvents">IdentityLogonEvents</option>
    <option value="IdentityQueryEvents">IdentityQueryEvents</option>
    <option value="UrlClickEvents">UrlClickEvents</option>
			<!-- Add more table options as needed -->
		</select>
<br>
<br>
		<div class="label">2. Set Time Range:</div>
		<input type="text" id="time-range" placeholder="e.g., 7d" />
<br>
<br>
		<div class="label">3. Enter process name:</div>
		<input type=text id="processes" placeholder="e.g., powershell.exe"/>
<br>
<br>
		<label class="label">4. Select Command-line strings:</label>
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
		<label class="label">5. Select Result Columns (project):</label>

		<select class="multi-select" id="columns" multiple>
    <option value="AccountDisplayName">AccountDisplayName</option>
    <option value="AccountName">AccountName</option>
    <option value="AccountType">AccountType</option>
    <option value="AccountUpn">AccountUpn</option>
    <option value="AlertId">AlertId</option>
    <option value="Application">Application</option>
    <option value="Category">Category</option>
    <option value="City">City</option>
    <option value="Country">Country</option>
    <option value="Department">Department</option>
    <option value="DeviceId">DeviceId</option>
    <option value="DeviceName">DeviceName</option>
    <option value="FileName">FileName</option>
    <option value="InitiatingProcessFileName">InitiatingProcessFileName</option>
    <option value="InitiatingProcessParentFileName">InitiatingProcessParentFileName</option>
    <option value="JobTitle">JobTitle</option>
    <option value="LogonType">LogonType</option>
    <option value="NetworkMessageId">NetworkMessageId</option>
    <option value="ProcessCommandLine">ProcessCommandLine</option>
    <option value="Protocol">Protocol</option>
    <option value="RecipientEmailAddress">RecipientEmailAddress</option>
    <option value="SenderFromAddress">SenderFromAddress</option>
    <option value="Severity">Severity</option>
    <option value="SHA256">SHA256</option>
    <option value="Subject">Subject</option>
    <option value="TargetAccountSid">TargetAccountSid</option>
    <option value="ThreatTypes">ThreatTypes</option>
    <option value="Title">Title</option>
    <option value="Url">Url</option>
    <option value="UrlChain">UrlChain</option>
    <option value="Workload">Workload</option>
    <option value="ZapTime">ZapTime</option>
			<!-- Add more column options as needed -->
		</select>
<br>
<br>
    <label class="label">6. Select the maximum result limit:</label>
<br>
		<select class="select" id="top">
		<option value="50">50</option>
		<option value="100">100</option>
		<option value="200">200</option>
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

