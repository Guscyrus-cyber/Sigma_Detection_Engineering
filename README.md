# Project Lab— Sigma Detection Engineering

## Windows Brute-Force Detection Using Sigma Rules and Splunk Enterprise

### Introduction
This Hands-in-practice lab has two parts:
1. Tools need for this project lab
2. The actual practical lab

This hands-on cybersecurity lab focuses on Sigma Detection Engineering, a fundamental skill used by SOC Analysts and Detection Engineers to identify suspicious activities, develop detection rules, and investigate potential security threats.

The primary objective is to develop, validate, and test Sigma detection rules for identifying Windows brute-force authentication attempts using Windows Security Event ID 4625 (Failed Logon). The investigation uses Windows 11 security-event logs previously generated during a controlled brute-force simulation and ingested into Splunk Enterprise.

### Lab Environment and Tool Preparation

Before beginning the practical detection-engineering exercises, the required tools were downloaded, installed, and configured through the macOS Terminal on a MacBook Pro.

The preparation included:

- **Python 3.12 and pipx:** Python environment and isolated package management.

- **Sigma CLI and pySigma:** Sigma rule validation and conversion.

- **Sigma Splunk Backend:** Conversion of Sigma YAML detection rules into Splunk Search Processing Language (SPL).

- **SigmaHQ Repository:** Official open-source detection rules downloaded through Git.

- **Visual Studio Code:** YAML rule development and editing.

- **Splunk Enterprise 10.2.1:** Previously installed and configured on the MacBook Pro for security-log ingestion, searching, and investigation.

- **Windows 11 Virtual Machine (UTM):** Existing Windows environment used to generate authentication-failure events.

Following installation, Sigma CLI functionality, Splunk backend compatibility, Sigma-to-SPL conversion, and access to Windows security-event data were successfully verified.

### Lab Objectives

The project focuses on developing practical SOC detection-engineering capabilities, including:

1.  Creating custom Sigma YAML detection rules for Windows authentication failures.

2.  Converting Sigma rules into executable Splunk SPL queries.

3.  Investigating Windows Event ID 4625 authentication activity.

4.  Identifying brute-force attack patterns and mapping relevant techniques to MITRE ATT&CK.

5.  Validating detection accuracy and investigating potential false positives.

6.  Documenting technical findings, detection evidence, and incident response procedures.

### Step 1 — Tools needed for Project 

<table style="width:99%;">
<colgroup>
<col style="width: 24%" />
<col style="width: 43%" />
<col style="width: 31%" />
</colgroup>
<thead>
<tr>
<th><strong>Tool</strong></th>
<th><strong>Purpose</strong></th>
<th><strong>Status</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td>macOS Terminal</td>
<td>Install and execute tools</td>
<td>Already available</td>
</tr>
<tr>
<td>Python 3</td>
<td>Run Sigma validation and testing utilities</td>
<td>Verify version</td>
</tr>
<tr>
<td>pip / pipx</td>
<td>Install Python applications</td>
<td>Verify</td>
</tr>
<tr>
<td>pySigma / Sigma CLI</td>
<td>Validate and convert Sigma rules</td>
<td>Install</td>
</tr>
<tr>
<td>Sigma Splunk backend</td>
<td>Convert Sigma rules to Splunk SPL</td>
<td>Install</td>
</tr>
<tr>
<td>Sigma rule repository</td>
<td>Reference community detection rules</td>
<td>Download</td>
</tr>
<tr>
<td>Git</td>
<td>Version control and rule management</td>
<td>Verify</td>
</tr>
<tr>
<td>Visual Studio Code</td>
<td>Write and edit YAML detection rules</td>
<td>Verify</td>
</tr>
<tr>
<td>Splunk Enterprise</td>
<td>Execute detection searches</td>
<td>Previously installed; verify</td>
</tr>
<tr>
<td>Windows 11 VM / <br />
Sysmon</td>
<td>Generate Windows security telemetry</td>
<td>Previously configured; verify</td>
</tr>
</tbody>
</table>

No additional Windows VM is required for this project. The existing Windows environment can be reused.

### Step 2 - Pre-installation check 

Before downloading anything, I check the tools already installed. This avoids duplicate installations and unnecessary storage usage.

I open Terminal on the MacBook Pro, copy the following commands, and I press Enter:\

(Images 1, 2, and 3)

### Step 3 —What happens after 
----------------------------------

After reviewing the Terminal output, the installation will proceed in this order:

1.  I install any missing Python tooling.

2.  I install Sigma CLI and its Splunk backend using the official SigmaHQ instructions. 

3.  I download the official [<u>SigmaHQ detection rule repository</u>](https://github.com/SigmaHQ/sigma).

4.  I verify Sigma YAML validation and SPL conversion.

5.  I confirm Splunk and Windows security-log availability.

Microsoft Sentinel/KQL support can be added later if needed. There is no reason to install \
additional SIEM platforms before verifying the existing environment.

## System verification results

| **Tool**          | **Result**    |     |
|-------------------|---------------|-----|
| macOS             | 26.6.1        |     |
| Python            | 3.12.0        |     |
| pip               | 23.2.1        |     |
| Homebrew          | 7.0.6         |     |
| Git               | 2.48.1        |     |
| VS Code CLI       | Installed     |     |
| Sigma CLI         | Not installed |     |
| pipx              | Not installed |     |
| Available storage | 14 GiB        |     |

## Install pipx

pipx installs Python command-line applications in isolated environments, helping avoid conflicts with existing Python packages.

In the macOS Terminal, I execute:

brew install pipx

After installation finishes, I execute:

pipx ensurepath

Then I verify:

pipx --version

The first command downloads and installs pipx. The second configures the executable search \
path. A new Terminal session may be necessary before the command becomes available.

After verifying pipx, the next step will be installing Sigma CLI and the Splunk backend. 

(Images 4 and 5)

The Terminal output confirms:

| **Component**         | **Result**    |
|-----------------------|---------------|
| Homebrew installation | Successful    |
| pipx version          | 1.17.12       |
| PATH configuration    | Successful    |
| Python dependencies   | Installed     |
| Installation errors   | None reported |


Install Sigma CLI
-----------------

Sigma CLI will be used to validate Sigma detection rules and convert supported rules into \
Splunk searches.

First, I close Terminal completely and I open a new Terminal window so the updated PATH takes effect.

Then I execute this command:

pipx install sigma-cli

This installs Sigma CLI in an isolated Python environment without modifying the existing \
Python 3.12 installation.

After installation, I verify the command:

sigma --version

Important: I do not install the Splunk backend yet. The next step will be configuring the Sigma CLI plugins \
needed for Splunk. (Images 6, 7, and 8)

Step 3A — Reinstall Sigma CLI using Python 3.12
-----------------------------------------------

There is no need to reinstall macOS, Homebrew, or Python.

First, I uninstall only the nonworking Sigma CLI environment:

pipx uninstall sigma-cli

Then I reinstall Sigma CLI using the existing Python 3.12 interpreter:

pipx install sigma-cli --python /Library/Frameworks/Python.framework/Versions/3.12/bin/python3

After installation, I verify:

sigma --version

This approach keeps Sigma CLI isolated from other Python packages and avoids changing the \
system's default Python installation. (Image 9 and 10)

Step 3B — Verify Sigma CLI
--------------------------

In Terminal, I execute:

sigma --help

This should display the available Sigma CLI commands, such as rule conversion, validation, and plugin management.

Then I execute:

pipx list

This should confirm that sigma-cli 3.1.0 is installed under Python 3.12.

### Current installation status

| **Component**           | **Status**            |
|-------------------------|-----------------------|
| Python 3.12             | Available             |
| pipx 1.17.12            | Installed             |
| Sigma CLI 3.1.0         | Installed             |
| Sigma CLI functionality | Awaiting verification |
| Splunk backend          | Not installed yet     |
| SigmaHQ rule repository | Not downloaded yet    |

After confirmation, the next step will be installing the Sigma Splunk backend so that Sigma \
YAML detection rules can be converted into Splunk SPL searches. (Image 11)

Step 4 — Install the Sigma Splunk Backend
-----------------------------------------

The Splunk backend will allow Sigma detection rules written in YAML to be converted into \
Splunk Search Processing Language (SPL).

In Terminal, I execute:

sigma plugin install splunk

After installation, I verify the backend:

sigma list targets

The output should include splunk as an available conversion target.

This is important because this Project lab will involve writing Sigma rules and executing the resulting SPL queries against Windows \
security events in the existing Splunk environment.

After verification, the next step will be downloading the official SigmaHQ detection rule \
repository. 

Top of Form

The output confirms:

| **Component**         | **Status** |
|-----------------------|------------|
| Sigma Splunk plugin   | Installed  |
| pySigma compatibility | Confirmed  |
| Splunk SPL target     | Available  |
| Splunk SPL2 target    | Available  |

The Sigma environment can now convert supported detection rules into Splunk searches. (Image 12)

Step 5 — Download the official SigmaHQ repository
-------------------------------------------------

The SigmaHQ repository contains community-maintained detection rules for Windows, Linux, cloud environments, and other security \
platforms.

These rules will serve as references when developing original detection logic for the project.

I execute the following commands in Terminal

First, I create a dedicated directory for Sigma tools:

mkdir -p ~/SOC-Tools

Next, I download the official repository:

git clone --depth 1 https://github.com/SigmaHQ/sigma.git ~/SOC-Tools/sigma

The --depth 1 option downloads only the latest repository snapshot, reducing storage requirements.

Finally, I verify the download:

ls ~/SOC-Tools/sigma

The expected output should include directories such as rules and other repository files.

Afterward, I will verify the downloaded rules, and I check the remaining available storage, and I confirm the existing Splunk environment.

The output confirms:

| **Component**             | **Status**        |
|---------------------------|-------------------|
| SigmaHQ GitHub repository | Downloaded        |
| Git clone                 | Successful        |
| 6,635 Git objects         | Received          |
| Detection rules directory | Available         |
| Threat-hunting rules      | Available         |
| Repository location       | ~/SOC-Tools/sigma |

(Image 13)

Step 6 — Verify Sigma rules and available storage
-------------------------------------------------

Before proceeding to Splunk integration, I should confirm that Sigma CLI can read and convert an existing Windows detection rule.

First, I locate Windows security rules. And I execute:

find ~/SOC-Tools/sigma/rules/windows -iname '\*failed\*logon\*.yml' \| head -10

Then I check the available storage:

df -h /

These commands do not modify anything.

### Why these matters

The first detection scenario will involve Windows Event ID 4625 — Failed Logon / Brute-Force Detection.

The downloaded SigmaHQ repository provides reference detection rules. I will develop and test original rules using the existing Windows and Splunk lab environment.

Afterward, I will verify Sigma rule conversion and Splunk connectivity before beginning \
This project lab

The output confirms:

| **Component**                 | **Result**                            |
|-------------------------------|---------------------------------------|
| Windows Sigma detection rules | Found                                 |
| Failed-logon detection rules  | Found                                 |
| SigmaHQ repository            | Accessible                            |
| Available storage             | 14 GiB                                |
| Storage condition             | Sufficient for Sigma rule development |

The repository contains relevant Windows authentication detection rules, including:

- win_security_susp_failed_logon_source.yml

- win_security_susp_failed_logon_reasons.yml

These can serve as references for the first detection-engineering scenario.

(Image 14)

Step 7 — Verify Sigma-to-Splunk conversion
------------------------------------------

Before beginning the actual lab, I need to verify that Sigma CLI can convert an existing YAML detection rule into Splunk SPL.

I execute this command in Terminal:

sigma convert -t splunk ~/SOC-Tools/sigma/rules/windows/builtin/security/win_security_susp_failed_logon_reasons.yml

Sigma CLI should generate a Splunk SPL search query.

This will confirm that:

1.  Sigma CLI can read YAML detection rules.

2.  The Splunk backend is operational.

3.  Sigma rules can be translated into Splunk search syntax.

Important: Successful conversion does not yet confirm that the query will work against the field names and indexes in the existing Splunk environment. That validation will follow separately.

After successful conversion, I will verify Splunk connectivity and Windows \
event-log availability. Once preparation is complete, the first hands-on Sigma Detection Engineering lab can begin.

The output shows that Sigma CLI and the Splunk backend are working, but the conversion requires an \
additional configuration called a processing pipeline.

This is not an installation failure.

### What is a processing pipeline?

A Sigma processing pipeline translates generic detection fields into the field names and data \
formats used by a specific SIEM platform.

For example, a Windows authentication rule may reference EventID, while a particular Splunk \
data source may require different field mappings. (Image 15)

Step 7A — Identify available Splunk pipelines
---------------------------------------------

In Terminal, I execute:

sigma list pipelines splunk

This command will display the processing pipelines available for the installed Splunk backend.

I will select the appropriate pipeline based on the Windows security-event format used in the existing Splunk environment.

Once selected, the next step will be converting the Sigma rule into SPL and verifying the\
generated query.

No additional software installation is necessary at this point.

The output confirms three available pipelines:

| **Pipeline**               | **Purpose**                           |
|----------------------------|---------------------------------------|
| splunk_windows             | Windows Security Event Logs           |
| splunk_sysmon_acceleration | Windows Sysmon events                 |
| splunk_cim                 | Splunk Common Information Model (CIM) |

For the first detection scenario, Windows Event ID 4625 — Failed Logon, I will start with 
splunk_windows. (Image 16)

Step 7B — Convert a Sigma rule into Splunk SPL
----------------------------------------------

I open Terminal and I execute:

sigma convert -t splunk -p splunk_windows ~/SOC-Tools/sigma/rules/windows/builtin/security/win_security_susp_failed_logon_reasons.yml

Sigma CLI should produce a Splunk SPL query containing Windows authentication detection \
conditions.

This will verify that:

1.  Sigma CLI can read an existing detection rule.

2.  The Windows processing pipeline is available.

3.  The Splunk backend can generate SPL.

4.  The detection-engineering environment is ready for further integration testing.

Important: The generated SPL must still be checked against the actual field names and \
sourcetypes in Splunk before being considered a working detection.

After successful conversion, I will verify the existing Splunk environment and Windows security-event data.

The output confirms that Sigma CLI parsed the YAML rule and generated a Splunk SPL query.

| **Component**               | **Status** |
|-----------------------------|------------|
| Sigma rule parsing          | 100%       |
| Splunk backend              | Working    |
| Windows processing pipeline | Working    |
| SPL query generation        | Successful |

The generated query includes:

- EventCode IN (4625, 4776) — Windows authentication failure events.

- Status and SubStatus — Authentication failure codes.

- SubjectUserSid — Account security identifier.

Important: This particular SigmaHQ rule detects selected suspicious authentication failure \
reasons. It is not yet a complete brute-force correlation rule. (Image 17)

Step 8 — Verify Splunk Enterprise
---------------------------------

Before beginning the actual detection-engineering lab, I need to verify that the existing Splunk installation is accessible.

On the MacBook Pro, I open Safari and enter:

http://localhost:8000

Then:

1.  I check whether the Splunk login page opens.

2.  I sign into my Splunk Enterprise account.

3.  I confirm that the Search & Reporting application is available.

### Preparation progress

Sigma tooling preparation Complete

Splunk verification Pending

Windows security telemetry verification Pending

Time to confirm whether Splunk opens successfully at http://localhost:8000.

Once Splunk is verified, I will check the existing Windows Event ID 4625 data before starting Project lab.

The output confirms:

| **Component**                      | **Status**            |
|------------------------------------|-----------------------|
| Splunk Enterprise 10.2.1           | Running               |
| Web interface (localhost:8000)     | Accessible            |
| Search & Reporting                 | Working               |
| Search execution                   | Successful            |
| Existing Linux authentication logs | 9,755 events returned |
| Windows Event ID 4625 data         | Not yet verified      |

The output shows that Splunk is operational. The displayed events are Linux authentication logs, not \
Windows security events.

## Step 9 — Verify Windows Event ID 4625 data

Before starting Project, I need to confirm that the Windows authentication events from the previous \
brute-force lab are still available.

In Splunk Search & Reporting, I replace the existing search with:

index=\* (EventCode=4625 OR EventID=4625)

\| stats count by index sourcetype host

\| sort - count

This will identify any indexed Windows \
failed-logon events and show the associated index, sourcetype, and host.

The screenshot confirms that Splunk returned 26 matching events across two datasets.

| **Index** | **Sourcetype**         | **Host**   | **Events** |
|-----------|------------------------|------------|------------|
| window    | disabled_account_usage | MacBookPro | 25         |
| main      | tier1_lab              | user       | 1          |
| Total     |                        |            | 26         |

This confirms that Windows \
failed-logon event data is present. However, the original Windows \
brute-force dataset has not yet been positively identified. (Image 18)

## Step 10 — Verify the actual Windows event fields

Before starting Sigma detection engineering, I need to inspect the available fields. This is important because Sigma-generated SPL must match the fields stored in Splunk.

In Splunk Search & Reporting, I replace the existing query with:

index=window (EventCode=4625 OR EventID=4625)

\| table \_time host EventCode EventID TargetUserName SubjectUserName IpAddress LogonType Status SubStatus \_raw

\| head 10

### What this step will verify

- Whether the events contain actual Windows authentication records.

- Whether TargetUserName, IpAddress, and LogonType are extracted.

- Whether the data is suitable for testing Sigma rules.

- Whether additional field mapping or data preparation is necessary.

The 25 events under disabled_account_usage are belong to a different investigation lab previously I worked on it, so it should not assumed that these are the original brute-force events.

Once the Windows event structure is verified, I can finish the preparation phase and begin the actual Sigma Detection Engineering project.

## Findings

The search returned 25 events from the window index.

| **Field**          | **Result**                  |
|--------------------|-----------------------------|
| EventCode          | 4625 — correctly extracted  |
| host               | MacBookPro — ingestion host |
| TargetUserName     | Empty                       |
| SubjectUserName    | Empty                       |
| IpAddress          | Empty                       |
| LogonType          | Empty                       |
| Status / SubStatus | Empty                       |
| Account_Name       | Present in raw events       |
| Failure_Reason     | Account Disabled            |
| src_ip             | Present in raw events       |

Important discovery

The dataset contains \
Windows-style authentication events, but the field names differ from those expected by the Sigma Splunk \
processing pipeline.

For example:

- Sigma may reference TargetUserName, while the dataset contains Account_Name.

- Sigma may reference IpAddress, while the dataset contains src_ip.

This is a realistic detection-engineering challenge: a detection rule can be syntactically correct but fail to identify threats \
because the SIEM field mappings do not match the ingested telemetry. (Image 19 and 20)

Step 11 — Verify extracted fields in Splunk
-------------------------------------------

In Splunk Search & Reporting, I replace the previous search with:

index=window sourcetype=disabled_account_usage EventCode=4625

\| table \_time host Account_Name Failure_Reason src_ip EventCode

\| head 10

This will determine whether Splunk has extracted the alternative field names or whether those \
values exist only inside \_raw.

If the fields are extracted correctly, I can plan the appropriate \
Sigma-to-Splunk field mapping. If not, I will configure or apply field extraction.

One additional consideration: these events concern disabled-account authentication attempts, not necessarily the original brute-force exercise. I will keep that distinction clear when selecting the dataset for This Project lab.

The output confirms that Splunk correctly extracts the following fields:

| **Field**      | **Result**                   |
|----------------|------------------------------|
| EventCode      | 4625                         |
| Account_Name   | bob, alice, admin, john      |
| Failure_Reason | Account Disabled             |
| src_ip         | Multiple source IP addresses |
| \_time         | Available                    |
| host           | MacBookPro                   |

All 25 events are searchable, and the first 10 are displayed correctly.

Important finding: The existing dataset is suitable for investigating disabled-account authentication attempts. However, field mapping will be necessary before testing Sigma rules designed for standard Windows authentication logs. (Image 21)

Step 12 — Check for the original brute-force dataset
----------------------------------------------------

Before completing preparation, I should locate the Windows Event ID 4625 dataset from the earlier brute-force lab.

In Splunk Search & Reporting, I execute:

index=\* "4625"

\| stats count by index sourcetype host

\| sort - count

This broader search looks for the event identifier anywhere in the searchable event text, \
including records where EventCodeor EventID was not extracted.

The objective is to determine whether the original Windows brute-force events are still indexed.

The output confirms 39 matching events across three datasets.

| **Index** | **Sourcetype**         | **Host**     | **Events** |
|-----------|------------------------|--------------|------------|
| window    | disabled_account_usage | MacBookPro   | 25         |
| main      | windows_security_xml   | Windows11-VM | 13         |
| main      | tier1_lab              | user         | 1          |
| Total     |                        |              | 39         |

The most important discovery is:

index=main sourcetype=windows_security_xml host=Windows11-VM

This appears to be the original Windows 11 security-event dataset from the previous \
brute-force investigation.

I have located the likely data source, but still need to confirm the event structure before beginning detection engineering. (Image 22)

Step 13 — Inspect the original Windows 11 security logs
-------------------------------------------------------

In Splunk Search & Reporting, I replace the existing query with:

index=main sourcetype=windows_security_xml "4625"

\| table \_time host \_raw

\| head 5

### What this will establish

I need to determine whether the original Windows 11 logs contain:

- Event ID 4625 — Failed Logon

- Target account information

- Logon type

- Source IP address or workstation

- Authentication failure status

- Original Windows Event XML structure

This information is necessary to develop a Sigma rule that accurately matches the real telemetry.

After verification, I can finish the preparation phase and begin the Project — Sigma Detection Engineering, using the original Windows brute-force events rather than unrelated sample data.

Final preparation step completed successfully.\
The five output confirm that the original Windows 11 authentication-failure events are stored in Splunk and available for investigation.

## Verified Windows security-event data

| **Field** | **Observed value** | **Meaning** |
|----|----|----|
| Event ID | 4625 from the search context | Failed Windows logon |
| Computer | WIN-AFBL2J2NDKS | Original Windows VM |
| Splunk host | Windows11-VM | Indexed data source |
| TargetUserName | Gus­cyrus | Account targeted |
| LogonType | 2 | Interactive logon |
| Status | 0xC000006D | Logon failure |
| SubStatus | 0xC000006A | Incorrect password |
| AuthenticationPackageName | Negotiate | Authentication package |
| EventRecordID | 1840–1844 | Consecutive Windows event records |

The five displayed events occurred on September 23, 2026, between approximately 9:11 PM and 9:14 PM Eastern Time. (Images 23 through 27)

These records are consistent with the earlier controlled brute-force simulation involving repeated incorrect passwords.

Important: Five failed logons establish suspicious-pattern test data, but do not independently prove a real-world attack.

Data Sources and Collection

This Sigma Detection Engineering project utilizes security-event data collected from previous hands-on SOC investigations, supplemented by open-source Sigma detection rules.

**1. Windows 11 Virtual Machine — Primary Data Source**

Windows Security Event Logs were collected from a Windows 11 virtual machine installed and configured through UTM on a MacBook Pro. During a previous brute-force detection lab, multiple unsuccessful authentication attempts were intentionally generated to produce Windows Security Event ID 4625 (Failed Logon).

The security events were exported from Windows Event Viewer, converted into XML format, and ingested into Splunk Enterprise for analysis. The existing dataset provides authentic Windows-generated security telemetry for developing and validating Sigma detection rules.

**2. SigmaHQ — Detection Rule Reference**

The official SigmaHQ GitHub repository was cloned through the macOS Terminal. The repository provides community-maintained YAML detection rules covering suspicious authentication activity, credential attacks, and other adversary techniques.

Existing Sigma rules serve as references for developing, modifying, and validating detection logic. Sigma CLI and the Splunk backend enable conversion of Sigma YAML rules into Splunk Search Processing Language (SPL).

**3. Splunk Enterprise — Additional Security Datasets**

Splunk Enterprise, previously installed on the MacBook Pro, contains additional security-event datasets from earlier SOC investigations.

Available datasets include:

- main — Windows Security XML events

- window — Disabled-account authentication activity

- main — Tier 1 SOC laboratory events

These datasets support detection testing, event correlation, investigation, and false-positive analysis. The provenance of supplemental datasets must be verified before classification as authentic or simulated security activity.

**Data Collection Objective**

The objective is to develop an original Sigma detection rule, convert the rule into executable Splunk SPL, validate detection results against existing Windows security events, investigate suspicious authentication behavior, and document findings using SOC detection-engineering practices.

Project — Preparation checklist
-------------------------------

| **Component**                     | **Status** |
|-----------------------------------|------------|
| Python and pipx                  |      √       |
| Sigma CLI                       |       √      |
| Sigma Splunk backend             |      √      |
| SigmaHQ repository               |      √      |
| Sigma-to-SPL conversion          |      √     |
| Splunk Enterprise                |      √      |
| Windows Event ID 4625 telemetry  |      √      |
| Original brute-force events      |      √      |

Environment preparation. Completed\
\
The XML fields are present in the raw records. Whether Splunk automatically extracts all \
necessary fields remain to be tested during the actual lab.

### Project Lab— Sigma Detection Engineering

Sigma Detection Engineering — Windows Brute-Force Detection (Event ID 4625)

Lab objective: Develop, validate, and test a custom Sigma detection rule for identifying repeated Windows authentication failures associated with brute-force activity.

Tools: Sigma CLI, pySigma, Splunk Enterprise, Windows 11 Security Event Logs, macOS Terminal, and Visual Studio Code.

MITRE ATT&CK: T1110 — Brute Force, including T1110.001 — Password Guessing.

**Lab workflow**

1.  Inspect Windows Event ID 4625 telemetry in Splunk.

2.  Identify authentication fields required for detection.

3.  Develop a custom Sigma YAML rule.

4.  Validate Sigma rule syntax and structure.

5.  Convert Sigma detection logic into Splunk SPL.

6.  Execute and validate the detection against Windows security logs.

7.  Develop event-correlation logic for repeated failed logons.

8.  Investigate false positives and evaluate detection accuracy.

9.  Document MITRE ATT&CK mapping, findings, and limitations.

10. Prepare the final GitHub repository, technical evidence, and incident response report.

## Step 1 — Windows Authentication Telemetry Baseline

Objective: Establish a baseline of Windows failed-logon events before developing detection logic.

Data source: Windows 11 VM security logs, previously exported in XML format and ingested into Splunk Enterprise.

### Procedure

I open Splunk Enterprise → Search & Reporting.

I execute the following SPL query: (Image 28)

File name: 01_Windows_4625_Telemetry_Baseline.png\


| **Field**         | **Result**           |
|-------------------|----------------------|
| Splunk index      | main                 |
| Sourcetype        | windows_security_xml |
| Host              | Windows11-VM         |
| Matching events   | 13                   |
| First event       | September 22, 2026   |
| Last event        | September 24, 2026   |
| Search time range | All time             |

Finding: Splunk successfully retrieved 13 Windows Security XML events containing 4625. Exact Event ID verification remains necessary before classifying every match as a failed-logon event. (Image 29)

Step 2 — Extract Windows Authentication Fields
----------------------------------------------

Objective: Identify the authentication fields required for developing an accurate Sigma detection rule.

Windows Event ID 4625 commonly contains the following information:

- TargetUserName — Account associated with the failed authentication

- Status — Authentication failure status

- SubStatus — Additional failure information

- LogonType — Authentication method

- IpAddress — Source IP address, when available

### Procedure

In Splunk Enterprise, I replace the previous search with the following SPL query.\
Then I immediately run it.\
\
After using the SPL query, and I run it:\
\
The Splunk output confirm successful extraction of Windows authentication-failure fields from the original Windows 11 VM security logs.



| **Field**          | **Observed result**      |
|--------------------|--------------------------|
| Windows Event ID   | 4625                     |
| Total events       | 13                       |
| Host               | Windows11-VM             |
| Account            | Guscyrus                 |
| Logon Type         | 2 — Interactive          |
| Status             | 0xC000006D               |
| SubStatus          | 0xC000006A               |
| Source IP          | 127.0.0.1 or unavailable |
| Observation period | September 22–24, 2026    |


Security analysis

- Status 0xC000006D: Authentication failure.

- SubStatus 0xC000006A: Incorrect password.

- Logon Type 2: Interactive logon attempt.

- Source IP 127.0.0.1: Loopback address; not evidence of a remote attacker.

- 13 events: Multiple authentication failures across the observation period, including clusters of closely spaced attempts.

Preliminary classification: Lab-generated repeated authentication failures consistent with password-guessing behavior. A confirmed brute-force detection requires a defined threshold and time window.

The final event has missing extracted fields. Additional validation may be necessary before including the event in account-based correlation. (Image 30 and 31)

Step 3 — Custom Sigma Detection Rule Development
------------------------------------------------

Objective: Create an original Sigma YAML detection rule targeting Windows failed authentication attempts associated with incorrect passwords.

The initial Sigma rule will detect individual failed-logon events. A subsequent correlation stage will identify repeated failures within a defined time window.

### Procedure — macOS Terminal

I open Terminal and execute the command.\
Visual Studio Code open a new YAML file.

### I copy the following detection rule into the file: windows_failed_password_logon.yml\
Then I save it. (Image 32 through 35)\

### Step 4 — Sigma YAML Rule Validation

Objective: Validate the custom Sigma detection rule for YAML syntax, required Sigma fields, and compatibility with the installed Sigma CLI.

Tool: Sigma CLI 3.1.0 — macOS Terminal

### 4.1— Verify the Sigma rule file

- I open macOS Terminal and execute: ls -l ~/SOC-Tools/Sigma-Detection-Lab/rules/\

  The Terminal screenshot confirms:\

  Sigma rule: windows_failed_password_logon.yml

- Location: ~/SOC-Tools/Sigma-Detection-Lab/rules/

- File size: 1,480 bytes

- Status: File exists and is ready for validation. (Image 36)

### 4.2 — Validate the Sigma rule

I execute:\
sigma convert -t splunk -p splunk_windows ~/SOC-Tools/Sigma-Detection-Lab/rules/windows_failed_password_logon.yml\
\
This command performs two operations:

1.  I parses the YAML file and I checks Sigma rule structure.

2.  I converts the detection logic into Splunk Search Processing Language (SPL).\
    \
    The output differs according to the installed Splunk processing pipeline.

(source="WinEventLog:Security" EventCode=4625 Status="0xC000006D" SubStatus="0xC000006A")\
\
Successful conversion confirms rule parsing and query generation, not actual detection accuracy. Detection testing against the Windows 11 dataset follows in Step 5.

filename: 02_Sigma_Rule_Validation.png

Step 4.2 — Successfully completed!\
The Terminal output confirms that the custom Sigma detection rule was successfully converted into Splunk SPL.

Generated SPL:

source="WinEventLog:Security" EventCode=4625 Status="0xc000006D" SubStatus="0xc000006A"

\| table TargetUserName,IpAddress,LogonType,Status,SubStatus

(Image 37)

### Step 5 — Test the Sigma Detection Rule in Splunk Enterprise

The existing Windows 11 VM dataset contains 13 failed-logon events. However, the imported XML events use different field extraction and source naming from the generated Sigma query.

I open Splunk Enterprise → Search & Reporting.

After running the SPL query,\
\
The Sigma Detection Engineering Lab — Windows Failed Password Logon Detection has 8 main steps.
\
The Splunk Enterprise output confirm successful execution of the adapted Sigma detection logic against the Windows 11 VM authentication logs.

### Detection Results

| **Detection field**   | **Result**                    |
|-----------------------|-------------------------------|
| SIEM Platform         | Splunk Enterprise 10.2.1      |
| Detection Rule        | Windows Failed Password Logon |
| Windows Event ID      | 4625                          |
| Total matching events | 12                            |
| Host                  | Windows11-VM                  |
| Account               | Guscyrus                      |
| Logon Type            | 2 — Interactive               |
| Status                | 0xc000006d                    |
| SubStatus             | 0xc000006a                    |
| Source IP             | 127.0.0.1 or unavailable      |
| Detection validation  | Successful                    |

### **SOC Analysis**

The detection identified 12 failed authentication events associated with the Window 11 VM.

Event ID 4625: Failed Windows authentication.

- Status 0xC000006D: Logon failure.

- SubStatus 0xC000006A: Incorrect password.

- Logon Type 2: Interactive logon.

- 127.0.0.1: Loopback address, not evidence of an external attacker.

The results demonstrate successful identification of incorrect-password authentication failures. Repeated failures can warrant investigation for brute-force activity, but the events alone do not establish malicious activity.

The detection was validated using an adapted SPL query because the imported XML dataset requires field extraction. Direct execution of the original Sigma-generated SPL remains a separate validation task. (Images 38. 39, and 40)

### Step 6 — Authentication Failure Investigation
\
The next objective is to determine the frequency and distribution of failed authentication attempts.

In Splunk Search & Reporting, I execute the SPL query.
\
After I am running the query:
\
index=main sourcetype=windows_security_xml "4625"\
\| rex field=\_raw "\<Data Name=\\TargetUserName\\\>(?\<Account_Name\>\[^\<\]\*)\</Data\>"\
\| rex field=\_raw "\<Data Name=\\Status\\\>(?\<Failure_Status\>\[^\<\]\*)\</Data\>"\
\| rex field=\_raw "\<Data Name=\\SubStatus\\\>(?\<Failure_SubStatus\>\[^\<\]\*)\</Data\>"\
\| rex field=\_raw "\<Data Name=\\IpAddress\\\>(?\<Source_IP\>\[^\<\]\*)\</Data\>"\
\| where lower(Failure_Status)="0xc000006d" AND lower(Failure_SubStatus)="0xc000006a"\
\| stats count AS Failed_Attempts earliest(\_time) AS First_Attempt latest(\_time) AS Last_Attempt by host Account_Name Source_IP\
\| convert ctime(First_Attempt) ctime(Last_Attempt)\
\| sort - Failed_Attempts

The Splunk Enterprise output confirms successful execution of the authentication failure investigation query.

### Investigation Results

| **Field**                           | **Result**         |
|-------------------------------------|--------------------|
| Host                                | Windows11-VM       |
| Account                             | Guscyrus           |
| Total failed attempts               | 12                 |
| Failures from 127.0.0.1             | 10                 |
| Failures with unavailable source IP | 2                  |
| First recorded failure              | September 22, 2026 |
| Last recorded failure               | September 23, 2026 |
| Authentication failure              | Incorrect password |
| Windows Event ID                    | 4625               |

SOC Investigation Findings

The investigation identified 12 failed authentication attempts associated with the Windows 11 virtual machine.

Ten authentication failures originated from the loopback address 127.0.0.1, while two events contained no usable source IP address.

The authentication failures occurred across two days. The available evidence is consistent with the controlled failed-password testing conducted during the Windows authentication lab.

Detection assessment: Successful identification of repeated incorrect-password attempts. No evidence of an external brute-force attack has been established. (Image 41)

### Step 7 — False-Positive Analysis and Detection Tuning

The next objective is to demonstrate an important detection engineering skill: distinguishing suspicious authentication activity from expected laboratory activity.

The existing Sigma rule identifies incorrect-password failures but does not establish a brute-force attack. Detection tuning will therefore focus on frequency, time windows, and expected local authentication activity.

Step 7.1 — Identify repeated authentication failures within a five-minute window.

I execute the following SPL in Splunk Enterprise: (Image 42)

Detection threshold: Three or more incorrect-password failures within the same five-minute time bucket, grouped by host, account, and source IP.

This threshold is for laboratory evaluation, not a production-ready brute-force detection. The five-minute buckets are fixed rather than rolling windows.

The Splunk output confirms successful execution of the five-minute authentication failure detection query.

### Detection Results

| **Detection field**             | **Result**                      |
|---------------------------------|---------------------------------|
| Host                            | Windows11-VM                    |
| Account                         | Guscyrus                        |
| Source IP                       | 127.0.0.1                       |
| Detection threshold             | 3 or more failures in 5 minutes |
| September 22, 2026, 22:20       | 5 failures                      |
| September 23, 2026, 21:10       | 4 failures                      |
| Total threshold-matching events | 9                               |
| Threshold-matching time buckets | 2                               |

**SOC Analysis**

The detection identified two clusters of repeated failed-password attempts.

Both clusters exceeded the configured threshold. However, the source address 127.0.0.1 represents local loopback activity, and the authentication failures originated from controlled Windows 11 laboratory testing.

**Finding:** The detection successfully identified repeated authentication failures, but the threshold matches do not establish a genuine brute-force attack. (Image 43)

Step 7.2 — False-Positive Tuning
--------------------------------

The next objective is to reduce alerts associated with known laboratory activity while preserving visibility into other authentication failures.

For this controlled lab, I exclude the loopback address from the threshold-based detection.

In Splunk Enterprise, I replace the previous query with: (Image 44)

After I run the query:

The output shows “No results found,” but that is the expected outcome.\

The purpose is to test whether false-positive tuning could eliminate alerts associated with known laboratory activity.

### Why Splunk returned no results

1.  Before tuning: 12 failed authentication events were identified.

2.  Threshold detection: Two groups exceeded the threshold of three failures within five minutes.

3.  After tuning: The query excluded the loopback IP address 127.0.0.1.

4.  Remaining events: Two events had unavailable source IP addresses.

5.  Final result: Neither remaining event group exceeded the threshold.

Therefore, Splunk correctly displayed “No results found.”

### What does this mean?

The result demonstrates that the tuning filter eliminated the two previously detected laboratory alert candidates.

However, zero results do not mean the detection is universally accurate or safe for production. The broad loopback exclusion would need further refinement.

So it demonstrates detection behavior before and after tuning.

Therefore, The Splunk Enterprise output confirms successful execution of the tuned detection query.

### Before and After Detection Tuning

| **Detection metric**             | **Before tuning**   | **After tuning**    |
|----------------------------------|---------------------|---------------------|
| Authentication events considered | 12                  | 2                   |
| Threshold-matching groups        | 2                   | 0                   |
| Threshold-matching failures      | 9                   | 0                   |
| Loopback exclusion               | No                  | Yes                 |
| Detection result                 | 2 alerts candidates | No alert candidates |

### **SOC Analysis**

The original detection identified two clusters of repeated failed authentication attempts originating from 127.0.0.1.

The tuned query excluded loopback activity and retained two authentication events with unavailable source IP addresses. Neither remaining event group met the threshold of three failed attempts within five minutes.\
\
**Result:** A 100% reduction in threshold-matching groups for the tested dataset.

This demonstrates successful laboratory false-positive tuning. However, the exclusion is appropriate only for the controlled test scenario. Production environments require further validation because malicious authentication attempts can also originate locally. (Image 45)

# Step 8 — Final SOC Investigation Report and 

Project: Sigma Detection Engineering — Windows Failed Password Logon Detection

Status: Detection testing and false-positive tuning completed. Final documentation in progress.

This final step documents the complete detection engineering workflow, including Sigma rule development, Splunk integration, Windows Event ID 4625 investigation, detection validation, and false-positive tuning.

The project addresses three important SOC and detection engineering competencies:

- Writing a custom Sigma detection rule for a real Windows authentication technique.

- Investigating detection results and tuning false-positive alerts.

- Converting Sigma YAML detection logic into Splunk SPL and validating the logic against Windows Security logs.


Final SOC Investigation Report
------------------------------

CASE SUMMARY

| Incident type             | Repeated failed authentication |
|---------------------------|--------------------------------|
| Event ID                  | 4625                           |
| Affected system           | Windows11-VM                   |
| Investigation period      | September 22–23, 2026          |
| Events investigated       | 12                             |
| Detection threshold       | 3 failures in 5 minutes        |
| Initial threshold matches | 2 groups                       |
| After tuning              | 0 groups                       |
| Final classification      | Authorized laboratory activity |

Executive Summary

A custom Sigma detection rule was developed to identify Windows failed authentication events associated with incorrect passwords. The Sigma CLI successfully converted the YAML rule into Splunk Search Processing Language (SPL).

Windows 11 Security logs collected during previous authentication testing were ingested into Splunk Enterprise on a MacBook Pro. An adapted SPL query identified 12 matching failed authentication events.

Subsequent analysis identified two five-minute periods containing five and four failed authentication attempts, respectively. Both groups exceeded the laboratory detection threshold.

False-positive analysis associated the matching activity with controlled local authentication testing. A laboratory-specific exclusion for 127.0.0.1 reduced the threshold-matching groups from two to zero.

The investigation demonstrated detection engineering, log analysis, threshold evaluation, and false-positive tuning. No unauthorized access or confirmed malicious activity was established.

### MITRE ATT&CK Mapping

MITRE ATT&CK T1110 — Brute Force

T1110.001 — Password Guessing

Repeated incorrect-password attempts can indicate password guessing. The observed events provide relevant authentication-failure telemetry, but the authorized laboratory activity does not establish an actual adversary attack.

### Final SOC Disposition

Classification: Benign positive — authorized laboratory authentication testing.

Detection effectiveness: The rule logic successfully identified incorrect-password failures. The threshold-based SPL search identified two groups requiring review.

Tuning limitation: Excluding all loopback traffic is not suitable as a general production control because local malicious activity could be missed.

Recommended production improvement: Apply approved host- and account-specific exceptions, validate against known malicious and benign samples, and correlate authentication failures with successful logons and endpoint telemetry.
