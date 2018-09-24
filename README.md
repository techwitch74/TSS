# TSS
TSS Windows CMD based Troubleshshooting script toolset 

1.	Quick Overview of Troubleshooting script tss.cmd
Purpose: Multi-purpose Troubleshooting tool to simplify just-in-time rapid data collection for standard and sporadic issues in in complex environments - or is to be considered as a convenient method for submitting and following easy action plans.
Copy the tss_tools_*.zip file and expand it to local disk, i.e. into C:\tools folder; in some scenarios we need to find the additional tools either provided in zip or externally i.e. Sysinternals tools in the path, and the script adds PATH and searches in extracted C:\tools by default.
Please start the script in the C:\tools folder in elevated CMD window.
C:\tools>  tss [parameter list]
Please invoke the tss command with necessary/appropriate parameters from here.
If troubleshooting intermittent/long-term issues, please invoke the script and stop it later in elevated CMD with same Admin User account (because parameters are stored in user’s registry hive [HKCU\SOFTWARE\Microsoft\tss.cmd-state\*] )

•	Tss.cmd is built on t.cmd and fully down-level compatible (accepts same parameters as t.cmd), and provides a Persistent switch to capture debug ETL, network sniff and ProcMon  data at boot time. (Example: tss.cmd CliOn Trace Persistent)
•	For overview of possible command switches, run tss.cmd without any parameter; for full help, run ‘tss /help’
•	The tss*.zip file contains additional binaries (*.exe, *.dll) and helper.cmd scripts needed for correct usage of some parameters, like WPR tracing, Procmon tracing, or purging older trace files.
•	You can use On paramaters [clion][srvon][Ron] individually or in combination, but at least one of clion, srvon, Ron must be specified. 
•	In typical reproduction scenarios you would start tracing, reproduce the problem, then stop tracing, Example:
o	Start tracing: Tss Ron Trace Procmon PSR
o	..Now reproduce problem scenario
o	Stop tracing: Tss off
•	Most troubleshooting scenarios do not require any changes within the script itself, but you could adjust settings in the section 
::::::: Configuration parameters, you can modify for your needs :::::::::
Frequently used parameters are read in from configuration file tss_config.cfg, and editing this file is the preferred option when you need to change default parameters like _DirWork, which is the default location for resulting data set.
•	You can adjust additional scripts to run at start or stop time
I.	Start of repro: tss_extra_repro_steps_AtStart.cmd
II.	Before stopping repro:  tss_extra_repro_steps_AtStop.cmd
III.	As a condition to stop tracing: tss_stop_condition_script.cmd to be leveraged by TSS switch stop:Cmd
If you start the script without any parameters, you will see available options in the help menu:
C:\tools> tss
ERROR: operation mode must be specified

 Enabling Tracing:
  usage: tss [clion][srvon][Ron] + see below section [Ron] Options for predefined scenarios: - and [Ron] Additional options:

   At least one of CliOn, SrvOn, Ron (= ReproOn) must be specified.
 clion      - generate client component ETL-logs
 srvon      - generate server component ETL-logs
 Ron        - collecting Repro data and logs
     if [Ron]            you can choose any combination of available Ron options below, i.e [Trace:N:scenario] 
 Remove     - remove persistent network and ETL component tracing; cleanup Registry settings
 Query      - query active ms_* Data Collector Set (LOGMAN QUERY, LOGMAN QUERY -ets)

Collection options:
 capture    - [downlevel t.cmd] in combination with CliOn, SrvOn: enable packet capture (Windows 7 / 2008 R2 or newer)
 circ:N     - generate circular logs of size N megabytes (default circular buffer size is 250 MB per log)
 cluster    - collect cluster event logs
 csv        - generate cluster CSV component traces
 hyperv     - collect Hyper-V event logs
 persistent - capture ETL, NETSH and ProcMon traces across reboots


 Switches for predefined Ron scenarios:
 Auth         -+ scenario: Authentication logs (Kerberos, NTLM, SSL, negoexts, pku2u, Http), Procmon, trace, SDP
 Branchcache  -+ scenario: Branchcache+BITS logs, network trace, PSR, Perfmon:BC, SDP
 CSC          -+ scenario: OfflineFiles infos, CSC database dump, trace, PSR, Procmon, SDP
 DFScli       -+ scenario: DFS client logs, network trace, PSR, Procmon, SDP
 DNScli       -+ scenario: DNS client logs, network trace, PSR, SDP
 MsCluster          -+ scenario: MsCluster related logs: NetFt, LBFO, Storport, Trace, Perfmon:SQL, ClusterLog
 SQLtrace     -+ scenario: SQL server related logs and TraceChn, Perfmon:SQL
 UNChard      -+ scenario: UNChardening logs, network trace, Procmon:Boot, Auth, GPsvc, SDP
 WebClient[:Adv] -+ scenario: WebClient logs, WebIO ETL, network trace, PSR, Proxy, SDP, [def:Basic, Adv= incl. iDNA]
  - more options to control noSDP, noPSR, noProcmon, noGPresult in preconfigured scenarios, see tss_config.cfg

Additional Switches for Ron:
 802Dot1x[:LAN|WLAN] - collect 802.1x data for wired LAN or WiFi wireless WLAN, default=LAN
 AccessChk     - collect AccessChk logs
 AfdTcp                    - collect Afd and TcpIp ETL-log
 Bluetooth   	- collect Bluetooth logs
  CSVspace           	- collect CSV_space ETL-log
  DFSsrv             	- collect DFS server ETL-log and Eventlog

DHCPcli            - collect DHCP client ETL-log and DHCP Reg info
DHCPsrv            - collect DHCP server ETL-log and 'netsh dhcp server' info
DNSsrv      	- collect DNS server ETL-log
ETLmax[:N :NrKeep] - set upper limit of ETL log file size, Range:100-4096, Circ:N has precedence for CliOn/SrvOn, [def:N=1024 (MB), NrKeep=10] 
 Evt[:Sys|App|Sec] - collect  System, Application, Security Eventlogs, default is Sys+App Eventlogs
 Fiddler     	- collect Fiddler trace and Firewall REG settings
 Firewall    	- collect Firewall ETL-log
 GPresult    	- collect GPresult, Auditing and Security logs
 GPsvc        	- collect Group Policy GPsvc.log
 HypHost               	- collect HyperV-Host and HyperV-VMbus ETL-log
 HypVM             	 - collect HyperV-VirtualMachine ETL-log
  iDNA:PID|name[:maxF:Full^|ring^|onLaunch] - collect iDNA/TTT dump for process ID or service name or unique ProcessName (requires tss_tools_ttt_v1.*.zip) [defaults: maxF=2048 mode=Full]
  IPsec              	- collect IPsec ETL-log
  LBFO               	- collect LBFO teaming session
  LiveKd[:start^|stop] - Execute kd/windbg memory dump on a live system at stage Start or Stop [def:Stop]
  Mini               - collect only minimal data, no supporting information data like Sysinfo, Tasklist, Services, Registry hives
 NetIO           	- collect NetIO, TcpIP, WebIO, Winsock-AFD, WFP ETL-log
 NLA                     	- collect NLA and NCSI ETL-log
 NLB               	- collect NLB (Afd TcpIp WFP +) ETL-log
  PCI                	- collect PCI, setupapi and msinfo32 infos
 Perfmon[:spec:dur] - collect Perfmon logs, spec: choose CORE|DISK|SQL|BC [def:CORE], Duration: 1-59 sec [def:59]
  persistent        	 - choosen ETL logs, NETSH traces or ProcMon will be activated after next reboot
 ProcDump:PID|name[:N:Int:Start|Stop] - collect N user dumps with ProcDump.exe for process ID or service name or unique ProcessName [defaults: N=3, Int=10, Stop] 
 ProcMon[:Boot|Purge:N] - collect ProcMon [Bootlog] trace, [Purge:N]: purge older *.pml files, keep number N [def:5]
 ProcTrack[:module|thread] - collect process tracking ETL, [Module: with Module load activity | Thread: with Thread+Module load activity]
 Proxy             	 - collect Proxy settings and related Registry settings
 PSR[:maxsc]        - collect Problem Step Recorder (PSR) screenshots, [def: maxsc=99]
 REG[:spec]    - collect Registry hives, spec: choose all|Auth|Branchcache|CSC|Proxy|tcp|UNChard |Webclient [def:all]
  Rpc               	 - collect RPC, RpcSs and DCOM ETL-log
 SDP[:spec]         - collect psSDP report, default SDP category= NET, choose [Net|Dom|CTS|Print|HyperV|Setup|Perf|Mini|Nano]
 Stop:Evt:ID[:Sys|App|Sec|Other[:EventlogName[:StatusCode]]] - stop data collection on trigger Eventlog: EventID and optional App, Sys, Sec; Other for _EventlogName in tss_config.cfg
 Stop:Log[:pollInt] - stop data collection on trigger Logfile: optional PollIntervall-in-sec (def pollInt=10)
  Stop:Cmd[:pollInt] - stop data collection on trigger tss_stop_condition_script: optional PollIntervall-in-sec (def pollInt=10)
      Example: stop:Evt:999:App =Stop on Event in Application Event log with Event ID# 999
      stop:Evt:40962/40961:Other:Microsoft-Windows-PowerShell/Operational:3221226599 =Stop on Event ID# 40962 or 40961 in Microsoft-Windows-PowerShell/Operational Event log
                     stop:Log:5 =Stop on Search-string entry in specific Log file, PollInt: 5sec, all to be defined within tss_config.cfg
   stop:Cmd  =Stop based on condition given in tss_stop_condition_script.cmd
  Crash         - to be used with Stop trigger above, Caution: this switch will force a memory dump, open files won't save. Run 'tss off' after reboot, see KB969028
 StorPort      - collect StorPort ETL-log
 Trace[:N:scenario:fileMode] - capture circular NETSH tracebufferSize MB [def:bufferSize=300, Scenario=InternetClient, fileMode=circular] 
   for available tracing scenarios, type: 'netsh trace show scenarios', [for SrvCORE def:InternetServer], scenario 'Capture' will only sniff 
TraceChn[:N:scenario:NrKeep] - capture chained NETSH trace, chunk bufferSize MB [def:300, Scenario=InternetClient, NrKeep=10]
 TraceNM[:N] 	- capture requires Netmon NMcap utility, N: bufferSize MB [def:300]
 TraceNMchn[:N:NrKeep] - chained capture requires Netmon NMcap utility, N: bufferSize MB [def:300, NrKeep=10]
   Video              - collect ScreenRecorder Video ~6 MB/min (requires Feature 'Desktop Experience' on server edition; needs DeCoder for viewing)
 VPN                	- collect VPN ETL data (includes AfdTcp) and network trace
 VSS      	- collect Volume Shadow Copy Service (VSS) reports
 WebIO              	- collect WinHttp, WinInet, WebIO ETL-log, i.e. for WebClient or Outlook
 WFP                	- collect WFP Windows Filtering Platform, BFE (Base Filtering Engine), includes AfdTcp ETL-log
 WPR[:spec] 	- collect WPR trace, spec: choose Storage|CPU|Wait|General [def:Wait]
 Xperf[:spec]	- collect Xperf trace, spec: choose SMB2|tbd [def:SMB2] , alternatively: you may put your Xperf command into tss_extra_repro_steps_AtStart.cmd
     -> see 'tss /help' for detailed help info 
 DsR[:Drive:BlockSize:Sec:FS] - DriveLetter [D], BlockSize(K) [1024], Duration(Sec) [300], FileSize [10G] for DiskSpeed Repro
noSDP              	- turn off question regarding SDP collection at stage 'tss off', i.e. when using scheduled tasks
noPSR             	 - do not run PSR, used to override setting in preconfigured TS scenarios
 noProcMon         	 - do not run ProcMon, used to override setting in preconfigured TS scenarios
 noGPresult         	- do not run GPresult, used to override setting in preconfigured TS scenarios
 no_Cluster_GetLogs - don't collect cluster infos / validation reports
OpsMgr             	- collect OpsMgr ETL and Eventlogs
verbose            	- verbose mode tracing flags (defined for fskm/mup srv)

 Disabling Tracing:
  usage: tss off [nocab] [nobin]
 off     - turn off tracing
 nocab   - do not compress traces
 nobin   - do not gather system binaries matching the captured traces
  noSDP         - do not ask for SDP report, i.e. when using script in scheduled tasks

 Disabling and ReEnabling Tracing:
  usage: tss snapshot [nocab] [nobin]
  

Predefined parameters in tss_config.cfg 
@rem tss_config.cfg: CONFIGURE below variables for granular controlling TSS behaviour
@rem Disk and folder path of data collection results, i.e. D:\MS_DATA - only use local disk!
_DirWork=!SYSTEMDRIVE!\MS_DATA
@
@rem For using 'Stop:Log' trigger specify path of monitored Logfile, optional PollIntervall-in-sec (def pollInt=8)
_StopSearchString=tss_test123
_LogFilePath="C:\Tools\tss_StopTokenFile_.tmp"
_LogPollIntervalSec=8
@
@rem next 3 variables control the frequency of purging chained Network Traces or Procmon logs
_PurgeNrFilesToKeep=10
_PurgePollIntervalSec=10
_PurgeTraceChn=1
@
_ProcMonSpec=Purge
_EtlCircBuf=1024
_TraceBufferSize=300
_TracefileMode=circular
_Autorestart=0
@
@rem next 3 variables are for 'AccessChk', please verify if disk, folder and file names exist
_ShareNames=C$ D$ E$
_FolderPaths=C:\DFSroots D:\ E:\
_FilePaths=C:\Temp\test.txt
@
@ controlling Branchcache/BITS
_RunPS_BCstatus=1
_BC_RESTART=0
_BITSLOG_RESET=0
_BITSLOG_RESTART=1
@
@rem ex: _EventlogName=Microsoft-Windows-PowerShell/Operational and _Stop_EventID=40962/40961 are used by  'stop:Evt:ID:Other:EvtLogname:Code' to stop i.e. on multiple events
_EventlogName=
_Stop_EventID=
_Stop_WaitTimeInSec=0
_Stop_StatusCode=0
@
@rem write an EventID 999 into remote hosts eventlog to stop TSS at remote host, if started with 'stop:Evt:999'; comma separated list like =host1,host2
_WriteEventToHosts=
@
@rem TSS tasks/parameters that will be skipped on local PC at 'tss OFF' or using 'stop:Evt' when EventID created by source 'TSS' is found; comma separated list
_SkipExecutionList=LiveKd,_Cluster_GetLogs
@
@rem _ClusterLog=0 : don't collect cluster log; _Cluster_GetLogs=1 : collect cluster info via PS script tss_Cluster_GetLogs.ps1
_ClusterLog=0
_Cluster_GetLogs=1
_Cluster_WriteEventToHosts=1
_LiveDmp_analytic=0
@
@rem To skip data compression/zip at stop of TSS, set nocab=1 
nocab=0
@
@rem To turn off asking for SDP reports at end of TSS, set _noSDP=1
_noSDP=0
@
@rem To turn off Procmon logging in TS_Scenarios, set _noProcmon=1
_noProcmon=0
@
@rem To turn off PSR logging in TS_Scenarios, set _noPSR=1
_noPSR=0
@
@rem To turn off GPresult logs in TS_Scenarios, set _noGPresult=1
_noGPresult=0
2.	Examples of frequently helpful built-in TS (troubleshooting) scenarios
TSS scenarios are predefined data collection sets, which include all necessary data like PSR, ProcMon, Perfmon or ETL tracing logs.
All these predefined scenarios include network tracing/sniffing: 


#1 Collect logs for UNC hardening issues, log on as local Admin, open elevated CMD window and run:
(Scenario includes persistent Client SMB ETL-logs, Network Trace, Gpresult, GPsvc, Auth, Registry, Procmon, SDP)
C:\tools> tss Clion Ron UNChard 

#2 Collect Branchcache logs
(Scenario includes Network Trace, PSR, Gpresult, Registry, Perfmon, SDP)
C:\tools> tss Ron Branchcache

#3 Collect DFS client logs
(Scenario includes Network Trace, PSR, Gpresult, Procmon, SDP)
C:\tools> tss CliOn Ron DFScli

#4 Collect DNS client logs
(Scenario includes Network Trace, PSR, SDP)
C:\tools> tss Ron DNScli

#5 Collect logs for SQLtracing
(Scenario includes Network Trace, Perfmon)
C:\tools> tss Ron SQLtrace

#6 Collect logs for CSC Offline Files
(Scenario includes Client SMB ETL-logs, Network Trace, Gpresult, Registry, PSR, Procmon, SDP)
C:\tools> tss CliOn Ron CSC

#7 Collect logs for Authentication provider
(Scenario includes Client SMB, SSL,HTTPsys ETL-logs, Network Trace, Gpresult, Registry, PSR, Procmon, SDP)
C:\tools> tss CliOn Ron Auth 

#8 Collect logs for MS-cluster 
(Scenario includes Network Trace, NetFT+LBFO+Cluster ETL, Storport, Perfmon, SDP)
C:\tools> tss Ron MsCluster 

#9 Collect logs for WebClient (similar to interactive WebClient SDP)
 (Scenario includes Network Trace, WebIO ETL, Proxy, PSR, Procmon, SDP, Advanced includes TTT/iDNA)
C:\tools> tss CliOn Ron Webclient[:Adv] 

#10 DiskSpeed tests, for more info see 'tss /help'
C:\tools> tss Ron DsR:D:1024:300:10G 

Above commands will start the data collection and most will stop on predefined scenarios after hitting ANY-key.
To stop data collection logging for non-predefined scenarios, run: 
C:\tools> tss off
( if you use ‘tss off nocab’ the data in C:\MS_DATA\<date-time folder> will not be compressed; using noSDP will not ask for SDP report.)

Some of the scenarios will ask for the SDP report to start run at the end of the data collection. 
Note: up to TSS version 1.68, the default included SDP report Portable_Diagnostic.exe is the Network Diagnostic from https://home.diagnostics.support.microsoft.com/selfhelp 
For the included default SDP, at the end of SDP data collection please uncheck ‘[ ] Send results to Microsoft’ click [Save a copy], then click [Next].
Please upload the saved report manually onto MS workspace.
 

TSS version 1.69 and later will invoke the already included PowerShell script based psSDP report, which runs on all OS versions, including Server core 2016. 
More Examples: 
A)	You want to stop tracing based on a trigger Eventlog ID or some specific error entry in a log file for issues, which occur sporadically: use the Stop feature:
Example: C:\tools> tss Ron traceChn:1 stop:Log 
stop:Evt:ID:Eventlog - stop data collection on trigger Eventlog: choose either App or Sys, and EventID
stop:Log[:pollInt] - stop data collection on trigger Logfile: optional PollIntervall-in-sec (def pollInt=10)
      Examples: stop:Evt:999:App =Stop on Event in Application Event log with Event ID# 999  (def: App:999 )
                         stop:Log:5 =Stop on Search-string entry in specific Log file, both to be defined within tss_TestLogFile.cfg
regarding second example, if you want to stop tracing based on a specific Windows Log entry, you can adjust the first two parameters in the file tss_TestLogFile.cfg, and optionally adjust 3rd parameter LogPollIntervalSec:
	set StopSearchString=tss_test123
	set LogFilePath=!_MS_ScriptDir!\tss_StopTokenFile._tmp
	set LogPollIntervalSec=8
Read the file tss_StopTokenFile._tmp for testing your config.

B)	You want to run extra commands right after the script begins to start running?
No problem, just edit the included batch file tss_extra_repro_steps_AtStart.cmd and modify it for your own needs.
This batch file will be executed at each start of 'tss Ron' =ReproOn.

C)	You want to run extra commands before the script ends repro?
No problem, just edit the included batch file tss_extra_repro_steps_AtStop.cmd and modify it for your own needs.
This batch file will be executed at end of repro and before 'tss OFF' =ReproOFF.

D)	You want to stop tracing based on specific conditions?
No problem, just edit the included batch file tss_stop_condition_script.cmd and modify it for your own needs (i.e. by default: check if File share is available).


When using the Persistent switch, the settings will be active after each reboot, unless you decide to remove it by running following command, when you are finished with all of your troubleshooting:
C:\tools> Tss remove

Notes/hints: 
-	Some parameters are mutually exclusive: don’t combine [capture], [trace] or [traceChn]
-	If you want to use the SDP switch with a specialty switch, just supply your SDP sceciality: 
default SDP category= NET, choose [Net|Dom|CTS|Print|HyperV|Setup|Perf|Mini|Nano]
-	In case of unforeseen errors, please be sure to stop tracing “tss off” before starting a new trace session. Also try “tss remove” if you can’t recover (new start of tss .. fails, stop command  tss off also fails)
