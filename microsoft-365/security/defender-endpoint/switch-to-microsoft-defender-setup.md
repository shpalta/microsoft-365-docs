---
title: Switch to Microsoft Defender for Endpoint - Setup
description: Make the switch to Defender for Endpoint. Review the setup process, which includes installing Microsoft Defender Antivirus.
keywords: migration, Microsoft Defender for Endpoint, antivirus, passive mode, setup process
search.product: eADQiWindows 10XVcnh
search.appverid: met150
ms.prod: m365-security
ms.technology: mde
ms.mktglfcycl: deploy
ms.sitesec: library
ms.pagetype: security
ms.author: deniseb
author: denisebmsft
localization_priority: Normal
manager: dansimp
audience: ITPro
ms.collection:
  - M365-security-compliance
  - m365solution-migratetomdatp
  - m365solution-mcafeemigrate
  - m365solution-symantecmigrate
ms.topic: article
ms.custom: migrationguides
ms.date: 09/23/2021
ms.reviewer: jesquive, chventou, jonix, chriggs, owtho
---

# Switch to Microsoft Defender for Endpoint - Phase 2: Setup

**Applies to:**
- [Microsoft Defender for Endpoint](https://go.microsoft.com/fwlink/p/?linkid=2154037)
- [Microsoft 365 Defender](https://go.microsoft.com/fwlink/?linkid=2118804)

|[![Phase 1: Prepare.](images/phase-diagrams/prepare.png)](switch-to-microsoft-defender-prepare.md)<br/>[Phase 1: Prepare](switch-to-microsoft-defender-prepare.md)|![Phase 2: Set up.](images/phase-diagrams/setup.png)<br/>Phase 2: Set up|[![Phase 3: Onboard3.](images/phase-diagrams/onboard.png)](switch-to-microsoft-defender-onboard.md)<br/>[Phase 3: Onboard](switch-to-microsoft-defender-onboard.md)|
|---|---|---|
||*You are here!*||

**Welcome to the Setup phase of [switching to Defender for Endpoint](switch-to-microsoft-defender-migration.md#the-migration-process)**. This phase includes the following steps:

1. [Reinstall/enable Microsoft Defender Antivirus on your endpoints](#reinstallenable-microsoft-defender-antivirus-on-your-endpoints).
2. [Configure Defender for Endpoint](#configure-defender-for-endpoint).
3. [Add Defender for Endpoint to the exclusion list for your existing solution](#add-microsoft-defender-for-endpoint-to-the-exclusion-list-for-your-existing-solution).
4. [Add your existing solution to the exclusion list for Microsoft Defender Antivirus](#add-your-existing-solution-to-the-exclusion-list-for-microsoft-defender-antivirus).
5. [Set up your device groups, device collections, and organizational units](#set-up-your-device-groups-device-collections-and-organizational-units).

## Reinstall/enable Microsoft Defender Antivirus on your endpoints

On certain versions of Windows, Microsoft Defender Antivirus was likely uninstalled or disabled when your non-Microsoft antivirus/antimalware solution was installed. When endpoints running Windows are onboarded to Defender for Endpoint, Microsoft Defender Antivirus can run in passive mode alongside a non-Microsoft antivirus solution. To learn more, see [Antivirus protection with Defender for Endpoint](microsoft-defender-antivirus-compatibility.md#antivirus-protection-with-defender-for-endpoint).

As you're making the switch to Defender for Endpoint, you might need to take certain steps to reinstall or enable Microsoft Defender Antivirus. The following table describes what to do on your Windows clients and servers.

<br/><br/>

|Endpoint type|What to do|
|---|---|
|Windows clients (such as endpoints running Windows 10)|In general, you do not need to take any action for Windows clients (unless Microsoft Defender Antivirus has been uninstalled). Microsoft Defender Antivirus should still be installed, but is most likely disabled at this point of the migration process. When a non-Microsoft antivirus/antimalware solution is installed and the clients are not yet onboarded to Defender for Endpoint, Microsoft Defender Antivirus is disabled automatically. <br/><br/> Later, when the client endpoints are onboarded to Defender for Endpoint, if those endpoints are running a non-Microsoft antivirus solution, Microsoft Defender Antivirus goes into passive mode. <br/><br/> If the non-Microsoft antivirus solution is uninstalled, Microsoft Defender Antivirus goes into active mode automatically.|
|Windows servers|On Windows Server, you'll need to reinstall Microsoft Defender Antivirus, and set it to passive mode manually. On Windows servers, when a non-Microsoft antivirus/antimalware is installed, Microsoft Defender Antivirus cannot run alongside the non-Microsoft antivirus solution. In those cases, Microsoft Defender Antivirus is disabled or uninstalled manually. <br/><br/> To reinstall or enable Microsoft Defender Antivirus on Windows Server, perform the following tasks: <br/>- [Set DisableAntiSpyware to false on Windows Server](#set-disableantispyware-to-false-on-windows-server) (only if necessary)<br/>- [Reinstall Microsoft Defender Antivirus on Windows Server](#reinstall-microsoft-defender-antivirus-on-windows-server)<br/>- [Set Microsoft Defender Antivirus to passive mode on Windows Server](#set-microsoft-defender-antivirus-to-passive-mode-on-windows-server)|

> [!TIP]
> To learn more about Microsoft Defender Antivirus states with non-Microsoft antivirus protection, see [Microsoft Defender Antivirus compatibility](microsoft-defender-antivirus-compatibility.md).

### Set DisableAntiSpyware to false on Windows Server

The [DisableAntiSpyware](/windows-hardware/customize/desktop/unattend/security-malware-windows-defender-disableantispyware) registry key was used in the past to disable Microsoft Defender Antivirus, and deploy another antivirus product, such as McAfee, Symantec, or others. **In general, you should not have this registry key on your Windows devices and endpoints**; however, if you *do* have `DisableAntiSpyware` configured, here's how to set its value to false:

1. On your Windows Server device, open Registry Editor.

2. Navigate to `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Defender`.

3. In that folder, look for a DWORD entry called **DisableAntiSpyware**.
   - If you do not see that entry, you're all set.
   - If you do see **DisableAntiSpyware**, proceed to step 4.

4. Right-click the DisableAntiSpyware DWORD, and then choose **Modify**.

5. Set the value to `0`. (This action sets the registry key's value to *false*.)

> [!TIP]
> To learn more about this registry key, see [DisableAntiSpyware](/windows-hardware/customize/desktop/unattend/security-malware-windows-defender-disableantispyware).

### Reinstall Microsoft Defender Antivirus on Windows Server

> [!IMPORTANT]
> The following procedure applies only to endpoints or devices that are running the following versions of Windows:
>
> - Windows Server 2019
> - Windows Server 2022
> - Windows Server, version 1803 (core-only mode)
> - Windows Server 2016 (see the following section, [Are you using Windows Server 2016?](#are-you-using-windows-server-2016))

1. As a local administrator on the endpoint or device, open Windows PowerShell.

2. Run the following PowerShell cmdlets:

   ```powershell
   # For Windows Server 2016
   Dism /online /Enable-Feature /FeatureName:Windows-Defender-Features
   Dism /online /Enable-Feature /FeatureName:Windows-Defender
   Dism /online /Enable-Feature /FeatureName:Windows-Defender-Gui
   # For Windows Server 2019 and Windows Server 2022
   Dism /online /Enable-Feature /FeatureName:Windows-Defender
   ```
   Then restart the device. 
   
   When using the DISM command within a task sequence running PowerShell, the following path to cmd.exe is required.
   Example:

   ```powershell
   c:\windows\sysnative\cmd.exe /c Dism /online /Enable-Feature /FeatureName:Windows-Defender-Features
   c:\windows\sysnative\cmd.exe /c Dism /online /Enable-Feature /FeatureName:Windows-Defender
   ```

### Set Microsoft Defender Antivirus to passive mode on Windows Server

1. Open Registry Editor, and then navigate to

   ```text
   Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection
   ```

2. Edit (or create) a DWORD entry called **ForceDefenderPassiveMode**, and specify the following settings:

   - Set the DWORD's value to **1**.
   - Under **Base**, select **Hexadecimal**.

> [!NOTE]
> After onboarding to Defender for Endpoint, you might have to set Microsoft Defender Antivirus to passive mode on Windows Server. To validate that passive mode was set as expected, search for *event 5007* in the **Microsoft-Windows-Windows Defender Operational** log (located at `C:\Windows\System32\winevt\Logs`), and confirm that either the  **ForceDefenderPassiveMode** or **PassiveMode** registry keys were set by to **0x1**.

### Are you using Windows Server 2016?

Currently, you cannot run Microsoft Defender Antivirus in passive mode on Windows Server 2016. Uninstall the non-Microsoft antivirus/antimalware solution, and install/enable Microsoft Defender Antivirus. If you're having trouble enabling Microsoft Defender Antivirus on Windows Server 2016, follow these steps:

1. On the device, open PowerShell as an administrator.

2. Type the following PowerShell cmdlet: `mpcmdrun -wdenable`.

> [!TIP]
> For more information, see the following articles:
>
> - [Microsoft Defender Antivirus on Windows Server](microsoft-defender-antivirus-on-windows-server.md)
> - [Microsoft Defender Antivirus compatibility with other security products](microsoft-defender-antivirus-compatibility.md)

### Confirm that Microsoft Defender Antivirus is enabled

You can use one of several methods to confirm the state of Microsoft Defender Antivirus, as described in the following table:

<br/><br/>

|Method|Procedure|
|---|---|
|Windows Security app| 1. On a Windows device, open the Windows Security app.<br/><br/>2. Select **Virus & threat protection**.<br/><br/>3. Under **Who's protecting me?** select **Manage providers**. On the **Security providers** page, under **Antivirus**, you should see **Microsoft Defender Antivirus is turned on**.|
|Task Manager|1. On a Windows device, open the Task Manager app.<br/><br/>2. Select the **Details** tab.<br/><br/>3. Look for **MsMpEng.exe** in the list.|
|Windows PowerShell <br/><br/> (To confirm that Microsoft Defender Antivirus is running)|1. On a Windows device, open Windows PowerShell.<br/><br/>2. Run the following PowerShell cmdlet: `Get-Process`.<br/><br/>3. Review the results. You should see **MsMpEng.exe** if Microsoft Defender Antivirus is enabled.|
|Windows PowerShell <br/> (To confirm that antivirus protection is in place)|You can use the [Get-MpComputerStatus PowerShell cmdlet](/powershell/module/defender/get-mpcomputerstatus). <br/><br/>1. On a Windows device, open Windows PowerShell.<br/><br/>2. Run following PowerShell cmdlet: `Get-MpComputerStatus|select AMRunningMode`.<br/><br/>3. Review the results. You should see either **Normal** or **Passive** if Microsoft Defender Antivirus is enabled on the endpoint.|

> [!TIP]
> [Learn more about Microsoft Defender Antivirus states](microsoft-defender-antivirus-compatibility.md#more-details-about-microsoft-defender-antivirus-states).

## Configure Defender for Endpoint

This step of the migration process involves configuring Microsoft Defender Antivirus for your endpoints. We recommend using Intune; however, you can any of the methods that are listed in the following table:

<br/><br/>

|Method|What to do|
|---|---|
|[Intune](/mem/intune/fundamentals/tutorial-walkthrough-endpoint-manager) <br/><br/> **NOTE**: Intune is now part of Microsoft Endpoint Manager.|1. Go to the [Microsoft Endpoint Manager admin center](https://go.microsoft.com/fwlink/?linkid=2109431) and sign in.<br/><br/>2. Select **Devices** \> **Configuration profiles**, and then select the profile type you want to configure. If you haven't yet created a **Device restrictions** profile type, or if you want to create a new one, see [Configure device restriction settings in Microsoft Intune](/intune/device-restrictions-configure).<br/><br/>3. Select **Properties**, and then select **Configuration settings: Edit**<br/><br/>4. Expand **Microsoft Defender Antivirus**.<br/><br/>5. Enable **Cloud-delivered protection**.<br/><br/>6. In the **Prompt users before sample submission** dropdown, select **Send all samples automatically**.<br/><br/>7. In the **Detect potentially unwanted applications** dropdown, select **Enable** or **Audit**.<br/><br/>8. Select **Review + save**, and then choose **Save**. <br/><br/> **TIP**: For more information about Intune device profiles, including how to create and configure their settings, see [What are Microsoft Intune device profiles?](/intune/device-profiles).|
|Microsoft Endpoint Configuration Manager|See [Create and deploy antimalware policies for Endpoint Protection in Configuration Manager](/mem/configmgr/protect/deploy-use/endpoint-antimalware-policies). <br/><br/> When you create and configure your antimalware policies, make sure to review the [real-time protection settings](/mem/configmgr/protect/deploy-use/endpoint-antimalware-policies#real-time-protection-settings) and [enable block at first sight](configure-block-at-first-sight-microsoft-defender-antivirus.md).
|Control Panel in Windows|Follow the guidance here: [Turn on Microsoft Defender Antivirus](/mem/intune/user-help/turn-on-defender-windows). (You might see *Windows Defender Antivirus* instead of *Microsoft Defender Antivirus* in some versions of Windows.)|
|[Advanced Group Policy Management](/microsoft-desktop-optimization-pack/agpm/) <br/><br/> or <br/><br/> [Group Policy Management Console](/windows/security/threat-protection/microsoft-defender-antivirus/use-group-policy-microsoft-defender-antivirus)|1. Go to **Computer configuration** \> **Administrative templates** \> **Windows components** \> **Microsoft Defender Antivirus**.<br/><br/>2. Look for a policy called **Turn off Microsoft Defender Antivirus**.<br/><br/>3. Choose **Edit policy setting**, and make sure that policy is disabled. This action enables Microsoft Defender Antivirus. (You might see *Windows Defender Antivirus* instead of *Microsoft Defender Antivirus* in some versions of Windows.)|

> [!TIP]
> You can deploy the policies before your organization's devices are onboarded.

## Add Microsoft Defender for Endpoint to the exclusion list for your existing solution

This step of the setup process involves adding Defender for Endpoint to the exclusion list for your existing endpoint protection solution and any other security products your organization is using.

> [!TIP]
> To get help configuring exclusions, refer to your solution provider's documentation.

The specific exclusions to configure will depend on which version of Windows your endpoints or devices are running, and are listed in the following table:

<br><br/>

|OS|Exclusions|
|---|---|
|Windows 10, [version 1803](/windows/release-health/status-windows-10-1803) or later (See [Windows 10 release information](/windows/release-health/release-information)) <p> Windows 10, version 1703 or 1709 with [KB4493441](https://support.microsoft.com/help/4493441) installed <p> [Windows Server 2019](/windows/release-health/status-windows-10-1809-and-windows-server-2019), Windows Server 2022 <p> [Windows Server, version 1803](/windows-server/get-started/whats-new-in-windows-server-1803)|`C:\Program Files\Windows Defender Advanced Threat Protection\MsSense.exe` <p> `C:\Program Files\Windows Defender Advanced Threat Protection\SenseCncProxy.exe` <p> `C:\Program Files\Windows Defender Advanced Threat Protection\SenseSampleUploader.exe` <p> `C:\Program Files\Windows Defender Advanced Threat Protection\SenseIR.exe`|
|[Windows 8.1](/windows/release-health/status-windows-8.1-and-windows-server-2012-r2) <p> [Windows 7](/windows/release-health/status-windows-7-and-windows-server-2008-r2-sp1) <p> [Windows Server 2016](/windows/release-health/status-windows-10-1607-and-windows-server-2016) <p> [Windows Server 2012 R2](/windows/release-health/status-windows-8.1-and-windows-server-2012-r2) <p> [Windows Server 2008 R2 SP1](/windows/release-health/status-windows-7-and-windows-server-2008-r2-sp1)|`C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Monitoring Host Temporary Files 6\45\MsSenseS.exe` <p> **NOTE**: Monitoring Host Temporary Files 6\45 can be different numbered subfolders. <p> `C:\Program Files\Microsoft Monitoring Agent\Agent\AgentControlPanel.exe` <p> `C:\Program Files\Microsoft Monitoring Agent\Agent\HealthService.exe` <p> `C:\Program Files\Microsoft Monitoring Agent\Agent\HSLockdown.exe` <p> `C:\Program Files\Microsoft Monitoring Agent\Agent\MOMPerfSnapshotHelper.exe` <p> `C:\Program Files\Microsoft Monitoring Agent\Agent\MonitoringHost.exe` <p> `C:\Program Files\Microsoft Monitoring Agent\Agent\TestCloudConnection.exe`|


## Add your existing solution to the exclusion list for Microsoft Defender Antivirus

During this step of the setup process, you add your existing solution to the Microsoft Defender Antivirus exclusion list. You can choose from several methods to add your exclusions to Microsoft Defender Antivirus, as listed in the following table:

<br><br/>

|Method|What to do|
|---|---|
|[Intune](/mem/intune/fundamentals/tutorial-walkthrough-endpoint-manager) <br/><br/> **NOTE**: Intune is now part of Microsoft Endpoint Manager.|1. Go to the [Microsoft Endpoint Manager admin center](https://go.microsoft.com/fwlink/?linkid=2109431) and sign in.<br/><br/>2. Select **Devices** \> **Configuration profiles**, and then select the profile that you want to configure.<br/><br/>3. Under **Manage**, select **Properties**.<br/><br/>4. Select **Configuration settings: Edit**.<br/><br/>5. Expand **Microsoft Defender Antivirus**, and then expand **Microsoft Defender Antivirus Exclusions**.<br/><br/>6. Specify the files and folders, extensions, and processes to exclude from Microsoft Defender Antivirus scans. For reference, see [Microsoft Defender Antivirus exclusions](/mem/intune/configuration/device-restrictions-windows-10#microsoft-defender-antivirus-exclusions).<br/><br/>7. Choose **Review + save**, and then choose **Save**.|
|[Microsoft Endpoint Configuration Manager](/mem/configmgr/)|1. Using the [Configuration Manager console](/mem/configmgr/core/servers/manage/admin-console), go to **Assets and Compliance** \> **Endpoint Protection** \> **Antimalware Policies**, and then select the policy that you want to modify.<br/><br/>2. Specify exclusion settings for files and folders, extensions, and processes to exclude from Microsoft Defender Antivirus scans.|
|[Group Policy Object](/previous-versions/windows/desktop/Policy/group-policy-objects)|1. On your Group Policy management computer, open the [Group Policy Management Console](https://technet.microsoft.com/library/cc731212.aspx), right-click the Group Policy Object you want to configure and then select **Edit**.<br/><br/>2. In the **Group Policy Management Editor**, go to **Computer configuration** and select **Administrative templates**.<br/><br/>3. Expand the tree to **Windows components \> Microsoft Defender Antivirus \> Exclusions**. (You might see *Windows Defender Antivirus* instead of *Microsoft Defender Antivirus* in some versions of Windows.)<br/><br/>4. Double-click the **Path Exclusions** setting and add the exclusions.<br/><br/>5. Set the option to **Enabled**.<br/><br/>6. Under the **Options** section, select **Show...**.<br/><br/>7. Specify each folder on its own line under the **Value name** column. If you specify a file, make sure to enter a fully qualified path to the file, including the drive letter, folder path, filename, and extension. Enter **0** in the **Value** column.<br/><br/>8. Select **OK**.<br/><br/>9. Double-click the **Extension Exclusions** setting and add the exclusions.<br/><br/>10. Set the option to **Enabled**.<br/><br/>11. Under the **Options** section, select **Show...**.<br/><br/>12. Enter each file extension on its own line under the **Value name** column. Enter **0** in the **Value** column.<br/><br/>13. Select **OK**.|
|Local group policy object|1. On the endpoint or device, open the Local Group Policy Editor.<br/><br/>2. Go to **Computer Configuration** \> **Administrative Templates** \> **Windows Components** \> **Microsoft Defender Antivirus** \> **Exclusions**. (You might see *Windows Defender Antivirus* instead of *Microsoft Defender Antivirus* in some versions of Windows.)<br/><br/>3. Specify your path and process exclusions.|
|Registry key|1. Export the following registry key: `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Defender\exclusions`.<br/><br/>2. Import the registry key. Here are two examples:<br/>- Local path: `regedit.exe /s c:\temp\ MDAV_Exclusion.reg`<br/>- Network share: `regedit.exe /s \\FileServer\ShareName\MDAV_Exclusion.reg`|

### Keep the following points about exclusions in mind

When you add [exclusions to Microsoft Defender Antivirus scans](/windows/security/threat-protection/microsoft-defender-antivirus/configure-exclusions-microsoft-defender-antivirus), you should add path and process exclusions.

Keep the following points in mind:

- *Path exclusions* exclude specific files and whatever those files access.
- *Process exclusions* exclude whatever a process touches, but does not exclude the process itself.
- List your process exclusions using their full path and not by their name only. (The name-only method is less secure.)
- If you list each executable (.exe) as both a path exclusion and a process exclusion, the process and whatever it touches are excluded.

## Set up your device groups, device collections, and organizational units

Device groups, device collections, and organizational units enable your security team to manage and assign security policies efficiently and effectively. The following table describes each of these groups and how to configure them. Your organization might not use all three collection types.

<br/><br/>

|Collection type|What to do|
|---|---|
|[Device groups](/microsoft-365/security/defender-endpoint/machine-groups) (formerly called *machine groups*) enable your security operations team to configure security capabilities, such as automated investigation and remediation. <br/><br/> Device groups are also useful for assigning access to those devices so that your security operations team can take remediation actions if needed. <br/><br/> Device groups are created in the [Microsoft 365 Defender portal](microsoft-defender-security-center.md).|1. Go to the Microsoft 365 Defender portal (<https://security.microsoft.com>).<br/><br/>2. In the navigation pane on the left, choose **Settings** \> **Endpoints** \> **Permissions** \> **Device groups**.<br/><br/>3. Choose **+ Add device group**.<br/><br/>4. Specify a name and description for the device group.<br/><br/>5. In the **Automation level** list, select an option. (We recommend **Full - remediate threats automatically**.) To learn more about the various automation levels, see [How threats are remediated](/microsoft-365/security/defender-endpoint/automated-investigations#how-threats-are-remediated).<br/><br/>6. Specify conditions for a matching rule to determine which devices belong to the device group. For example, you can choose a domain, OS versions, or even use [device tags](/microsoft-365/security/defender-endpoint/machine-tags).<br/><br/>7. On the **User access** tab, specify roles that should have access to the devices that are included in the device group.<br/><br/>8. Choose **Done**.|
|[Device collections](/mem/configmgr/core/clients/manage/collections/introduction-to-collections) enable your security operations team to manage applications, deploy compliance settings, or install software updates on the devices in your organization. <br/><br/> Device collections are created by using [Configuration Manager](/mem/configmgr/).|Follow the steps in [Create a collection](/mem/configmgr/core/clients/manage/collections/create-collections#bkmk_create).|
|[Organizational units](/azure/active-directory-domain-services/create-ou) enable you to logically group objects such as user accounts, service accounts, or computer accounts. <br/><br/> You can then assign administrators to specific organizational units, and apply group policy to enforce targeted configuration settings. <br/><br/> Organizational units are defined in [Azure Active Directory Domain Services](/azure/active-directory-domain-services).|Follow the steps in [Create an Organizational Unit in an Azure Active Directory Domain Services managed domain](/azure/active-directory-domain-services/create-ou).|

## Next step

**Congratulations**! You have completed the Setup phase of [switching to Defender for Endpoint](switch-to-microsoft-defender-migration.md#the-migration-process)!

- [Proceed to Phase 3: Onboard to Defender for Endpoint](switch-to-microsoft-defender-onboard.md)
