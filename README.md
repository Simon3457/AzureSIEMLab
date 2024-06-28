# Azure Sentinel SIEM Lab

<h2><b>Description</b></h2>
This lab goes through the process of creating a Windows 10 honeypot virtual machine on Azure to leave open for any attackers to access. We will extract the failed RDP logs from the Event Viewer using a custom PowerShell script and send it to an IP geolocation API that will return the geolocation data we'll use to map out the attacks globally on Azure Sentinel. 
<br />


<h2><b>Languages and Utilities</b></h2>

- <b>PowerShell</b>
- <b>https://www.ipgeolocation.io</b>

<h2><b>Environments</b></h2>

- <b>Azure</b>
- <b>Windows 10</b> (22H2)

<h2><b>Lab walk-through:</b></h2>
<ol>
  <li><b>Create Free Azure Account</b>
    <ol>
      <li>Go to https://azure.microsoft.com/en-ca/free/</li>
      <li>Click on “Start Free” button & fill out personal information </li>
    </ol>
  </li>
  <br />
  <br />
  
  <li><b>Create Honey Pot Virtual Machine</b>
    <ol>
      <li>Go to https://portal.azure.com/?quickstart=true#create/Microsoft.WindowsServer2016Datacenter-ARM </li>
      <li>Create resource group & determine VM settings (enter info on screenshot, use your own login info at the bottom)</li>
      <br />
      <p align="center">
        Creating Azure VM: <br/>
        <img src="https://i.imgur.com/OgaJzo8.png" height="50%" width="50%" alt="Azure SIEM Lab Steps"/>
      </p>
      <br />
      <li>Check the licensing agreement, click “Next: Disks >” & then click “Next: Networking >”</li>
      <li>Next to “NIC network security group”, select “Advanced” option</li>
      <li>Next to “Configure network security group”, click on “create new” link located under the dropdown menu</li>
      <li>Remove the default firewall rule & click “+ Add an inbound rule” & add the following information
        <ul><li>The goal is to create an inbound firewall rule to allow all network traffic to enter the newly created virtual machine</li></ul>
      </li>
      <br />
      <p align="center">
        Open Firewall Rule for network discoverability: <br/>
        <img src="https://i.imgur.com/D6b4YSk.png" height="50%" width="50%" alt="Azure SIEM Lab Steps"/>
      </p>
      <br />
      <li>Click “Review + create” & then “Create” blue buttons located at the bottom left of the screen
        <ul><li>Deployment will take a while to complete, but while that’s going on we can go to the next step</li></ul>
      </li>
    </ol>
  </li>
  <br />
  <br />
  
  <li><b>Create & setup Log Analytics Workspace</b>
    <ol>
      <li>Go to Microsoft Azure home page</li>
      <li>On the search bar, type “log analytics workspace” & click on that option</li>
      <li>On the log analytics workspaces page, click on “+ Create” button, enter information
          <ul><li>Use resource group that you created in step 2(i)</li></ul>
      </li>
      <br />
      <p align="center">
        Creating Log Analytics Workspace:  <br/>
        <img src="https://i.imgur.com/pVAqaEQ.png" height="50%" width="50%" alt="Azure SIEM Lab Steps"/>
      </p>
      <br />
      <li>Go back to Microsoft Azure home page, under “Azure services” click “+ Create a resource”</li>
      <li>On search bar, type “security center”, click on the option</li>
      <li>Click on “Create” under “Azure Security Center” option</li>
      <li>Click on “Open Defender for Cloud” white button at the bottom left of the screen</li>
      <li>Under “Management” tab, click “Environment settings” option & click on arrow beside “Azure subscription 1” & click on the workspace we created earlier</li>
      <br />
      <p align="center">
        Setting up Log Analytics Workspace:  <br/>
        <img src="https://i.imgur.com/hdHaLKN.png" height="70%" width="70%" alt="Azure SIEM Lab Steps"/>
      </p>
      <br />
      <li>Click “Save” and “Confirm” (every option should be on except for “SQL servers on machines”)</li>
      <li>Under “Settings” located towards the left of the screen, click on “Data collection”</li>
      <li>Click on “All Events” option & click “save”</li>
      <li>Go back to log analytics workspace & click on workspace created earlier</li>
      <li>Under “Classic” tab, click “Virtual Machines (deprecated)” & then click the honey pot VM</li>
      <li>Click on the “Connect” button located at the top left of the screen</li>
    </ol>
  </li>
  <br />
  <br />
  
  <li><b>Create Azure Sentinel</b>
    <ol>
      <li>Go to Microsoft Azure home page</li>
      <li>On the search bar, type “Azure sentinel” & click on that option</li>
      <li>Click on “Create Microsoft Sentinel” blue button located in the middle of the page</li>
      <li>Select the workspace we created earlier & click “Add” blue button located at the bottom left of the screen</li>
    </ol>
  </li>
  <br />
  <br />
  
  <li><b>Connect to the Honey Pot Virtual Machine</b>
    <ol>
      <li>Go to Microsoft Azure home page</li>
      <li>On the search bar, type “Virtual machines” & click on that option</li>
      <li>Select the honeypot VM we created earlier & copy the Public IP address field</li>
      <li>Open Remote Desktop Connection (RDP) app from start menu</li>
      <li>Paste the IP address in the “Computer” field & click “Connect”</li>
      <li>Click “Ok” on warning pop-up</li>
      <li>On the credentials pop-up, click on “More choices” & click “Use a different account”</li>
      <li>Enter admin credentials & click “Yes” on warning pop-up</li>
      <li>Set up VM (privacy settings; turn everything OFF)</li>
      <li>When opening VM, Networks blue tab will open on the right, click “Yes”</li>
    </ol>
  </li>
  <br />
  <br />

  <li><b>Setup Honey Pot Virtual Machine</b>
    <ol>
      <li>Go to start menu of the VM, type in “Event Viewer” & click on the app </li>
      <li>Click the “Windows Logs” left side tab & select “Security” option
        <ul><li>This is where all the failed login logs will be located</li></ul>
      </li>
      <li>Go to start menu of the VM & type in “wf.msc”, click on the appropriate application</li>
      <li>Click on “Windows Defender Firewall Properties” link & turn off firewall settings
        <ol>
          <li>On “Domain Profile” tab, turn “Firewall state” off</li>
          <li>On “Private Profile” tab, turn “Firewall state” off</li>
          <li>On “Public Profile” tab, turn “Firewall state” off</li>
          <li>Click ok</li>
        </ol>
      </li>
      <li>Open Windows PowerShell ISE, click “New”, copy in the custom PowerShell script & save</li>
      <li>Go to https://ipgeolocation.io/ & sign up, there will be an API key in the dashboard section</li>
      <li>In the custom script, change the “$API_KEY” value to the one in our newly created API account, save the file & run the script</li>
    </ol>
  </li>
  <br />
  <br />
  
  <li><b>Create Custom Log inside Log Analytics Workspace</b>
    <ol>
      <li>Go to Microsoft Azure home page</li>
      <li>On the search bar, type “log analytics workspace” & click on that option</li>
      <li>Click on workspace created earlier</li>
      <li>Under “Settings” tab, click on “Tables”</li>
      <li>Click on the “+ Create” button on that page & click option “New custom log (MMA-based)”</li>
      <li>We need to copy the log file over
        <ol>
          <li>Go to PowerShell script & go to “$LOGFILE_PATH” folder location</li>
          <li>Open “failed_rdp.log” file & copy all its contents (CTRL+A & CTRL+C)</li>
          <li>Open Notepad on PC & paste the logs in that file, save it as “failed_rdp.log”</li>
        </ol>
      </li>
      <li>On “Create a custom log” page, select the file we just created on our desktop & click next</li>
      <li>Make sure info is well formatted & click next</li>
      <li>In collection paths section, enter “Windows” as the “Type” and “$LOGFILE_PATH” folder location as the “Path”, click next</li>
      <li>In “Custom log name”, enter anything as its name, click next & click “Create”
        <ul><li>The custom log will be created immediately but it will take 30-45 minutes for the custom log analytics workspace and the virtual machine to synchronize (which is needed for the next step to work)</li></ul>
      </li>
    </ol>
  </li>
  <br />
  <br />
  
  <li><b>Setup Azure Sentinel</b>
    <ol>
      <li>Go to log analytics workspace, click on our workspace & click on “Logs” tab near the top</li>
      <li>Paste the custom log query below & click on the “Run” blue button located at the top of the page</li>
      <br />
      <p align="left">
        FAILED_RDP_WITH_GEO_CL <br/>
          |extend Username = extract(@"username:([^,]+)", 1, RawData), <br/>
          	Timestamp = extract(@"timestamp:([^,]+)", 1, RawData), <br/>
          	Latitude = extract(@"latitude:([^,]+)", 1, RawData), <br/>
          	Longitude = extract(@"longitude:([^,]+)", 1, RawData), <br/>
          	Sourcehost = extract(@"sourcehost:([^,]+)", 1, RawData), <br/>
          	State = extract(@"state:([^,]+)", 1, RawData), <br/>
          	Label = extract(@"label:([^,]+)", 1, RawData), <br/>
          	Destination = extract(@"destination:([^,]+)", 1, RawData), <br/>
          	Country = extract(@"country:([^,]+)", 1, RawData) <br/>
          |where Destination != "samplehost" <br/>
          |where Sourcehost != "" <br/>
          |summarize event_count=count() by Timestamp, Label, Country, State, Sourcehost, Username, Destination, Longitude, Latitude <br/>
      </p>
      <li>Go back to home page, type in search bar “Azure sentinel” & click option</li>
      <li>In overview, unselect “New overview” located at the top right of the page</li>
      <li>Under “Threat management” tab, click on “Workbooks” & click on “+ Add Workbook”</li>
      <li>Click “Edit” button located at the top left of the page, remove all the default workbooks</li>
      <li>Click “+ Add”, click “Add query” & paste the custom log query above & click “Run Query”</li>
      <li>Under “Visualization”, set it to “Map”</li>
      <li>Change the map settings to your liking & make sure you save the workbook</li>
    </ol>
  </li>
  <br />
  <br />

  <li><b>POST-SETUP (execute the lab)</b>
    <ol>
      <li>If you shut down the VM on your last use, start the VM on Azure (it's recommended to only disconnect from the VM so that the failed RDP logs actually appear)</li>
      <li>Go to RDP, paste the IP address of the VM in the “Computer” field & click “Connect”</li>
      <li>Once connected, open Windows PowerShell ISE, open the PowerShell script & run it</li>
      <li>Go to Microsoft Sentinel & go to “Workbooks” tab, select the workbook we created & click “View saved workbook” blue button located at the bottom right of the screen</li>
      <li>View this workbook to analyze the failed RDP login attempts on our honeypot VM</li>
    </ol>
    <p align="center">
      Azure Sentinel Map:  <br/>
      <img src="https://i.imgur.com/yUmCmGT.png" height="100%" width="100%" alt="Azure SIEM Lab Steps"/>
    <br />
    </p>
  </li>
</ol>


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
