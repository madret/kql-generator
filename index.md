<html>
<head>
	<title>KQL Hunting Generator</title>
	<style>
		.container {
			width: 500px;
			margin: 50px auto;
			font-family: Arial, sans-serif;
		}

		h1 {
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
			var selectedProcesses = getSelectedValues("processes");
			var selectedCommands = getSelectedValues("commands");
			var selectedColumns = getSelectedValues("columns");

			// Generate the query
			var query = "union " + selectedTables.join(", ");
			if (timeRange !== "") {
				query += " | where Timestamp > ago(" + timeRange + ")";
			}
			if (selectedProcesses.length > 0) {
				query += ' | where FileName in~ ("' + selectedProcesses.join('", "') + '")';
			}
			if (selectedCommands.length > 0) {
				query += ' | where ProcessCommandLine has_any ("' + selectedCommands.join('", "') + '")';
			}
			if (selectedColumns.length > 0) {
				query += " | project " + selectedColumns.join(", ");
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
		<h1>KQL Hunting Generator</h1>

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
		<div class="label">2. Set Time Range:</div>
		<input type="text" id="time-range" placeholder="e.g., 7d" />
<br>
		<div class="label">3. Select Processes:</div>
		<select class="multi-select" id="processes" multiple>
    <option value="AccCheckConsole.exe">AccCheckConsole.exe</option>
    <option value="adplus.exe">adplus.exe</option>
    <option value="AgentExecutor.exe">AgentExecutor.exe</option>
    <option value="AppInstaller.exe">AppInstaller.exe</option>
    <option value="Appvlp.exe">Appvlp.exe</option>
    <option value="Aspnet_Compiler.exe">Aspnet_Compiler.exe</option>
    <option value="At.exe">At.exe</option>
    <option value="Atbroker.exe">Atbroker.exe</option>
    <option value="Bash.exe">Bash.exe</option>
    <option value="Bginfo.exe">Bginfo.exe</option>
    <option value="Bitsadmin.exe">Bitsadmin.exe</option>
    <option value="Cdb.exe">Cdb.exe</option>
    <option value="CertOC.exe">CertOC.exe</option>
    <option value="CertReq.exe">CertReq.exe</option>
    <option value="Certutil.exe">Certutil.exe</option>
    <option value="Cmstp.exe">Cmstp.exe</option>
    <option value="Cmd.exe">Cmd.exe</option>
    <option value="Cmdkey.exe">Cmdkey.exe</option>
    <option value="cmdl32.exe">cmdl32.exe</option>
    <option value="ConfigSecurityPolicy.exe">ConfigSecurityPolicy.exe</option>
    <option value="Conhost.exe">Conhost.exe</option>
    <option value="Control.exe">Control.exe</option>
    <option value="Csc.exe">Csc.exe</option>
    <option value="Cscript.exe">Cscript.exe</option>
    <option value="CustomShellHost.exe">CustomShellHost.exe</option>
    <option value="DataSvcUtil.exe">DataSvcUtil.exe</option>
    <option value="Desktopimgdownldr.exe">Desktopimgdownldr.exe</option>
    <option value="DeviceCredentialDeployment.exe">DeviceCredentialDeployment.exe</option>
    <option value="Devinit.exe">Devinit.exe</option>
    <option value="Devtoolslauncher.exe">Devtoolslauncher.exe</option>
    <option value="Dfsvc.exe">Dfsvc.exe</option>
    <option value="Diantz.exe">Diantz.exe</option>
    <option value="Diskshadow.exe">Diskshadow.exe</option>
    <option value="Dnscmd.exe">Dnscmd.exe</option>
    <option value="Dotnet.exe">Dotnet.exe</option>
    <option value="Esentutl.exe">Esentutl.exe</option>
    <option value="Eventvwr.exe">Eventvwr.exe</option>
    <option value="Expand.exe">Expand.exe</option>
    <option value="Explorer.exe">Explorer.exe</option>
    <option value="Extexport.exe">Extexport.exe</option>
    <option value="Extrac32.exe">Extrac32.exe</option>
    <option value="Findstr.exe">Findstr.exe</option>
    <option value="Finger.exe">Finger.exe</option>
    <option value="fltMC.exe">fltMC.exe</option>
    <option value="Forfiles.exe">Forfiles.exe</option>
    <option value="Ftp.exe">Ftp.exe</option>
        <option value="GfxDownloadWrapper.exe">GfxDownloadWrapper.exe</option>
    <option value="Hh.exe">Hh.exe</option>
    <option value="IMEWDBLD.exe">IMEWDBLD.exe</option>
    <option value="Ie4uinit.exe">Ie4uinit.exe</option>
    <option value="Ieexec.exe">Ieexec.exe</option>
    <option value="Ilasm.exe">Ilasm.exe</option>
    <option value="Infdefaultinstall.exe">Infdefaultinstall.exe</option>
    <option value="Installutil.exe">Installutil.exe</option>
    <option value="Jsc.exe">Jsc.exe</option>
    <option value="Ldifde.exe">Ldifde.exe</option>
    <option value="Makecab.exe">Makecab.exe</option>
    <option value="Mavinject.exe">Mavinject.exe</option>
    <option value="Microsoft.Workflow.Compiler.exe">Microsoft.Workflow.Compiler.exe</option>
    <option value="Mmc.exe">Mmc.exe</option>
    <option value="MpCmdRun.exe">MpCmdRun.exe</option>
    <option value="Msbuild.exe">Msbuild.exe</option>
    <option value="Msconfig.exe">Msconfig.exe</option>
    <option value="Msdt.exe">Msdt.exe</option>
    <option value="Msedge.exe">Msedge.exe</option>
    <option value="Mshta.exe">Mshta.exe</option>
    <option value="Msiexec.exe">Msiexec.exe</option>
    <option value="Mshtml.dll">Mshtml.dll</option>
    <option value="Mshtmled.exe">Mshtmled.exe</option>
    <option value="MsoHtmEd.exe">MsoHtmEd.exe</option>
    <option value="Mspub.exe">Mspub.exe</option>
    <option value="msxsl.exe">msxsl.exe</option>
    <option value="ntdsutil.exe">ntdsutil.exe</option>
    <option value="OpenConsole.exe">OpenConsole.exe</option>
    <option value="Powerpnt.exe">Powerpnt.exe</option>
    <option value="Powershell.exe">Powershell.exe</option>
    <option value="powershell_ise.exe">powershell_ise.exe</option>
    <option value="Procdump.exe">Procdump.exe</option>
    <option value="ProtocolHandler.exe">ProtocolHandler.exe</option>
    <option value="rcsi.exe">rcsi.exe</option>
    <option value="Remote.exe">Remote.exe</option>
    <option value="Rundll32.exe">Rundll32.exe</option>
    <option value="Runexehelper.exe">Runexehelper.exe</option>
    <option value="Runonce.exe">Runonce.exe</option>
    <option value="Runscripthelper.exe">Runscripthelper.exe</option>
    <option value="Sc.exe">Sc.exe</option>
    <option value="Schtasks.exe">Schtasks.exe</option>
    <option value="Scriptrunner.exe">Scriptrunner.exe</option>
    <option value="Setres.exe">Setres.exe</option>
    <option value="SettingSyncHost.exe">SettingSyncHost.exe</option>
    <option value="Shell32.dll">Shell32.dll</option>
    <option value="Stordiag.exe">Stordiag.exe</option>
    <option value="SyncAppvPublishingServer.exe">SyncAppvPublishingServer.exe</option>
    <option value="Syncappvpublishingserver.vbs">Syncappvpublishingserver.vbs</option>
<option value="Sqldumper.exe">Sqldumper.exe</option>
<option value="Sqlps.exe">Sqlps.exe</option>
<option value="SQLToolsPS.exe">SQLToolsPS.exe</option>
<option value="Squirrel.exe">Squirrel.exe</option>
<option value="te.exe">te.exe</option>
<option value="Tracker.exe">Tracker.exe</option>
<option value="Update.exe">Update.exe</option>
<option value="VSIISExeLauncher.exe">VSIISExeLauncher.exe</option>
<option value="vsjitdebugger.exe">vsjitdebugger.exe</option>
<option value="Wfc.exe">Wfc.exe</option>
<option value="Winword.exe">Winword.exe</option>
<option value="Wsl.exe">Wsl.exe</option>
<option value="vsls-agent.exe">vsls-agent.exe</option>
<option value="CL_LoadAssembly.ps1">CL_LoadAssembly.ps1</option>
<option value="CL_Mutexverifiers.ps1">CL_Mutexverifiers.ps1</option>
<option value="CL_Invocation.ps1">CL_Invocation.ps1</option>
<option value="Launch-VsDevShell.ps1">Launch-VsDevShell.ps1</option>
<option value="Manage-bde.wsf">Manage-bde.wsf</option>
<option value="Pubprn.vbs">Pubprn.vbs</option>
<option value="Syncappvpublishingserver.vbs">Syncappvpublishingserver.vbs</option>
<option value="UtilityFunctions.ps1">UtilityFunctions.ps1</option>
<option value="winrm.vbs">winrm.vbs</option>
<option value="Pester.bat">Pester.bat</option>

			<!-- Add more process options as needed -->
		</select>
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
		<label class="label">5. Select Result Columns:</label>

		<select class="multi-select" id="columns" multiple>
    <option value="AccountDisplayName">AccountDisplayName</option>
    <option value="AccountName">AccountName</option>
    <option value="AccountType">AccountType</option>
    <option value="ActionType">ActionType</option>
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
    <option value="Timestamp">Timestamp</option>
    <option value="Title">Title</option>
    <option value="ZapTime">ZapTime</option>
			<!-- Add more column options as needed -->
		</select>
<br>
		<button onclick="generateQuery()">Generate Query</button>
<br>
		<div class="code-box" id="output"></div>
	</div>
</body>
</html>

