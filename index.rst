.. Documentation master file, created by
   sphinx-quickstart on Sat Mar  6 02:48:40 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.
=========================
Windows Logon Agent (WLA)
=========================

.. toctree::
   :hidden:
   :maxdepth: 3


   index




Overview
========

WIP




Deployment
==========

WIP

Group Policy Object (GPO)
=========================

Installing WLA and Configuring Settings via Group Policy Object (GPO)
*********************************************************************

Group Policy enables policy-based administration that allows to centrally manage and configure many policy settings. Group Policy uses directory services and security group membership to provide flexibility and support extensive configuration information. Policy settings are specified by an administrator
For more information about GPO, please refer to `Group Policy Overview <https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831791(v=ws.11)>`_

Installation of SafeNet Windows Logon Agent (WLA) via GPO
*********************************************************

To install the SafeNet Windows Logon Agent via GPO, follow these steps:

Creating a Distribution Point
-----------------------------

To deploy an MSI through GPO, you need to perform these steps to create a distribution point on the **Publishing Server**

#. Log in to the server as an administrator
#. Create a shared network folder

.. note:: The shared network folder will contain the MSI package and the Agent Configuration file

3. Set permissions on this folder to allow access to the distribution package
4. Copy the MSI and Agent file to the previously created shared network folder

Creating a Group Policy Object
------------------------------

An MSI package is deployed (distributed) through GPO. To create an object, you need to perform the below steps:

#. To open **Group Policy Management**, in the **Run** menu enter *gpmc.msc* and click :guilabel:`OK`
#. Expand **Forest** (your forest) --> **Domains** (your domain)
#. Right-click the **Group Policy Objects** and select :guilabel:`New`
#. Enter a name for your policy and leave **Source Starter GPO** as *none*
#. Right-click the domain name and select :guilabel:`Link an Existing GPO…`
#. In **Select GPO** pop up, select newly created GPO and click :guilabel:`OK`
#. Click the newly created GPO. In the right pane, right-click the linked domain name and select :guilabel:`enforce`

.. note:: Performing the above steps will create and enforce a new GPO, and will link it with the domain

Configuring ADMX and ADML Settings
----------------------------------

The SafeNet Windows Logon Agent policy settings are stored in a **Windows Administrative Template (ADMX)** file. The settings can be edited using Windows tools. The settings can be propagated to the entire domain, or be applied to the local computer and domain controllers only.

To configure settings:

#. Add ADMX and ADML file to Group Policy Object (GPO) Editor

#. Configure ADMX and ADML settings using GPO Editor

Adding ADMX and ADML Files to Group Policy Object Editor
--------------------------------------------------------

#. Copy the Local Group Policy definition (C:\\Windows\\PolicyDefinitions) to the Domain Group Policy location (C:\\Windows\\SYSVOL\\sysvol\\<domain_name>\\Policies).

#. Copy the ADMX file (:code:`<Application_name>_AgentConfig_<Date>.admx`) from Agent Installation Package, to the following location on your domain controller/server:

::

  C:\Windows\SYSVOL\sysvol\<domain_name>\Policies\PolicyDefinitions

3. Copy the appropriate ADML language file (:code:`<Application_name>_AgentConfig_<Date>.adml`) to the language folder in the :code:`\PolicyDefinitions` folders.

For example:

  • In Windows 8/10, the English language file provided should be copied to: :code:`C:\Windows\SYSVOL\sysvol\<domain_name>\Policies\PolicyDefinitions\en-US`

Configuring ADMX and ADML Settings
----------------------------------

After the Administrative Template has been added, you can open the template to configure the settings

**To open the SafeNet Windows Logon Agent settings:**

1. Open the Windows **Run** menu and type in :code:`gpmc.msc`
2. Click :guilabel:`OK`
3. The **Group Policy Management** windows opens

.. thumbnail:: /images/wla/gpmc.png

4. Complete one of the following actions:

- To propagate the settings to all clients in the domain, right-click **Default Domain Policy** or **newly created GPO** under the domain node.

- To apply the settings to the local machine and any other domain controllers in this domain, under the **Domain Controllers** node, right-click **Default Domain Controllers Policy**

5. From the dropdown menu, select :guilabel:`Edit...`. The **Group Policy Management Editor** window opens
6. In the left pane, navigate to :menuselection:`Computer Configuration --> Policies --> Administrative Templates --> WLA Policies --> AuthGINA`. The **SafeNet Windows Logon Agent** settings are displayed in the right pane

.. thumbnail:: /images/wla/wla_policies.png

7. Enable all the required settings (except **PrimaryServiceURL** and **OptionalSecondaryServiceURL**), if not already enabled, with default value or user-defined value

.. important:: The **PrimaryServiceURL** and **OptionalSecondaryServiceURL** should be set to **[Not Configured]**. The value of both the settings gets configured via the **.agent** file

.. note:: For **LocalUserOrGroup_Ex** and **LocalUserOrGroup_In** settings, in the Value field, you can enter **%COMPUTERNAME%\groupname**. In this case, when the GPO settings are pushed to the client machines, the variable **(%COMPUTERNAME%)** will be automatically set to the computer name of the respective client machine

ADMX and ADML Settings
----------------------

The table below provides description of the GPO Settings available with the **SafeNet Windows Logon Agent**:

.. table::
  :class: tight-table

  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Setting                                                                 | Description and Usage                                                                                                                                                                     |
  +=========================================================================+===========================================================================================================================================================================================+
  | Use Proxy                                                               | | This policy setting configures the use of a proxy server for                                                                                                                            |
  |                                                                         | | connecting with the token validation service e.g. STA or a Token Validation Proxy.                                                                                                      |
  |                                                                         | | The setting corresponds to the UI option (Communications tab) called                                                                                                                    |
  |                                                                         | | Use Proxy.                                                                                                                                                                              |
  |                                                                         | |                                                                                                                                                                                         |
  |                                                                         | | [1] Enables the use of a proxy server                                                                                                                                                   |
  |                                                                         | | [0] Proxy server is not used (**default value**)                                                                                                                                        |
  |                                                                         | |                                                                                                                                                                                         |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | If you enable this setting you must also enable ‘**Proxy Server**’                                                                                                                      |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | DoNotFilter                                                             | | This policy setting allows a view where other, third-party credential providers                                                                                                         |
  |                                                                         | | can also be displayed                                                                                                                                                                   |
  |                                                                         | | By default, WLA filters out (does not display) any other credential providers.                                                                                                          |
  |                                                                         | |                                                                                                                                                                                         |
  |                                                                         | | The correct syntax is:                                                                                                                                                                  |
  |                                                                         | | **{GUID},{GUID},{GUID}**                                                                                                                                                                |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | | Strip NETBIOS prefix                                                  | | This policy setting determines if a NETBIOS name (DOMAIN\\USERNAME) is                                                                                                                  |
  | | (**domain\\username** will be sent as **username**)                   | | sent to the authentication server as-is, or if the portion prefixing                                                                                                                    |
  |                                                                         | | the username is removed (stripped).                                                                                                                                                     |
  |                                                                         | | The setting corresponds to the UI option (Communications tab) called                                                                                                                    |
  |                                                                         | | Strip NETBIOS prefix (domain\\username will be sent as username)                                                                                                                        |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] Strips the DOMAIN\ portion from the username when authenticating with SAS                                                                                                           |
  |                                                                         | | [0] WLA will not sanitize the username (**default value**)                                                                                                                              |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | This is useful when users log in with NETBIOS sometimes and SAS                                                                                                                         |
  |                                                                         | | username at other times and you need to sanitize what is being sent from                                                                                                                |
  |                                                                         | | different protected resources so that SAS can still authenticate the                                                                                                                    |
  |                                                                         | | user(s) consistently.                                                                                                                                                                   |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | WLAasV1Provider                                                         | | WLAasV1Provider is used to select the Credential Provider Type                                                                                                                          |
  |                                                                         | | and specify whether Credential Provider Type is set to V1 or not.                                                                                                                       |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | - Windows Credential Provider Type selected as V2: **WLAasV1Provider=0**                                                                                                                |
  |                                                                         | | - Windows Credential Provider Type selected as V1: **WLAasV1Provider=1**                                                                                                                |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | In Windows 7 and Windows Server 2008 R2,                                                                                                                                                |
  |                                                                         | | it is recommended to keep the value 1 i.e. using V1 credential provider.                                                                                                                |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | In Windows 8, 10, 2012, 2016, 2019,                                                                                                                                                     |
  |                                                                         | | it is recommended to keep the value 0 i.e. using V2 credential provider.                                                                                                                |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | IPAddressAPIUrl                                                         | | This specifies the URL to get the public IP of the machine,                                                                                                                             |
  |                                                                         | | to support network/IP based logon policies as configured in STA                                                                                                                         |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | IPAddressFallbackAPIUrl                                                 | | This specifies a secondary URL, in case the URL mentioned in                                                                                                                            |
  |                                                                         | | IPAddressAPIUrl fails, to get the public IP of the machine to support                                                                                                                   |
  |                                                                         | | network/IP based logon policies as configured in STA                                                                                                                                    |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Enable SSL Certificate Check                                            | | This policy setting enables or disables the validation of the SAS or Token Validation Proxy (if used) server certificate.                                                               |
  |                                                                         | | The setting corresponds with the UI option called Enable SSL Certificate Check.                                                                                                         |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] WLA will validate the server certificate (**default value**)                                                                                                                        |
  |                                                                         | | [0] Does not validate server certificate                                                                                                                                                |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | Disabling the certificate check can be useful in troubleshooting or when testing using a self-signed untrusted certificate.                                                             |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Proxy Server                                                            | | This policy setting configures the proxy server IP address or FQDN                                                                                                                      |
  |                                                                         | | and its port number for use when connecting with the token validation                                                                                                                   |
  |                                                                         | | service (e.g. SAS or a Token Validation Proxy).                                                                                                                                         |
  |                                                                         | | The values set correspond to the UI options (Communications tab) called                                                                                                                 |
  |                                                                         | | ‘Proxy Server:’ and ‘Port:’. Correct syntax is: '1.2.3.4:567' or                                                                                                                        |
  |                                                                         | | 'host.domain.name:port'.                                                                                                                                                                |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | Must be used with setting ‘Use Proxy’ and/or 'Use Proxy for SPS'.                                                                                                                       |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Exempt Local/Domain Administrator strong authentication                 | | This policy setting configures the exclusion of local and domain                                                                                                                        |
  |                                                                         | | administrators from using strong authentication (OTP). When set,                                                                                                                        |
  |                                                                         | | administrators can authenticate with fixed passwords instead of dynamic                                                                                                                 |
  |                                                                         | | passwords. When disabled [0] all users must use OTP for Windows Logon                                                                                                                   |
  |                                                                         | | (assuming no other exemptions are configured). The setting corresponds                                                                                                                  |
  |                                                                         | | to UI option: 'Exempt Local/Domain Administrator strong authentication' (Policy tab)                                                                                                    |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] Local & Domain Administrators are exempt from strong authentication (**default value**)                                                                                             |
  |                                                                         | | [0] All users musts use strong authentication.                                                                                                                                          |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | ProxyPassword                                                           | | This policy setting configures the proxy server password as used when                                                                                                                   |
  |                                                                         | | authenticating to the defined proxy server (if required), in order to                                                                                                                   |
  |                                                                         | | connect with the token validation service (e.g. SAS or a Token Validator                                                                                                                |
  |                                                                         | | Proxy host). The setting corresponds to the UI option called:                                                                                                                           |
  |                                                                         | | 'Password'. Note that setting 'Password' also assumes setting ‘Proxy                                                                                                                    |
  |                                                                         | | Server’ and ‘Username’, and may also require setting 'Use Proxy for SPS'                                                                                                                |
  |                                                                         | | (if applicable)                                                                                                                                                                         |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **WARNING:**                                                                                                                                                                            |
  |                                                                         | | Windows Logon Agent (WLA) uses the SAS key file to encrypt and decrypt                                                                                                                  |
  |                                                                         | | the proxy password during operation and thus assumes the password is                                                                                                                    |
  |                                                                         | | propagated from the GPO in encrypted form (!) To set the password with                                                                                                                  |
  |                                                                         | | the GPO it is therefore best to retrieve it from the registry of a                                                                                                                      |
  |                                                                         | | client already installed                                                                                                                                                                |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | LocalUserOrGroup_Ex                                                     | | Local Groups which are excluded from SafeNet Authentication.                                                                                                                            |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | When any group is added to this setting through GPO,                                                                                                                                    |
  |                                                                         | | **DomainUSerOrGroup_In** has to be set to **'*'**                                                                                                                                       |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Primary Server (IP:Port)                                                | | This policy setting configures the primary SAS authentication server (or                                                                                                                |
  |                                                                         | | the Token Validation Proxy). Syntax is either protocol followed by IP                                                                                                                   |
  |                                                                         | | address and port (if non-standard) or protocol followed by FQDN and port                                                                                                                |
  |                                                                         | | (if non-standard), i.e.: 'http://1.2.3.4:8080' or                                                                                                                                       |
  |                                                                         | | 'https://server.domain.com'. The setting corresponds to UI options                                                                                                                      |
  |                                                                         | | (Communications tab): 'Primary Server (IP:Port)' and when the default                                                                                                                   |
  |                                                                         | | value is used (or when a custom value is used with https): 'Use SSL'                                                                                                                    |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Enable Microsoft Password Caching                                       | | When enabled (set to 1), the WLA will cache the user's domain password on first                                                                                                         |
  |                                                                         | | successful authentication and then cache it until password expiration,                                                                                                                  |
  |                                                                         | | requiring only the use of OTP for Windows logon                                                                                                                                         |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | The setting corresponds to the UI option called: ‘Enable Microsoft Password Caching’                                                                                                    |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] WLA will cache the users Windows domain password and only require OTP                                                                                                               |
  |                                                                         | | [0] Users are required to enter both Windows domain password and OTP (**default value**)                                                                                                |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | Enabling this setting will improve usability (user convenience) of the solution                                                                                                         |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Agent Encryption Key File                                               | | This policy setting sets the key file location. Refer to default value below for example syntax                                                                                         |
  |                                                                         | | The setting corresponds to the UI option called: 'Agent Encryption Key File:' (Communications tab)                                                                                      |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | Default value: [C:\Program Files\SafeNet\Windows Logon\KeyFile\Agent.bsidkey]                                                                                                           |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | A customer may use the default (bundled with the agent) key file or set their own virtual server specific key file                                                                      |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Enable GrIDsure tokens                                                  | | This policy setting configures the appearance and use of GrIDsure authentication in WLA during Windows logon.                                                                           |
  |                                                                         | | The setting corresponds to the UI option (Policy tab) called:  'Enable GrIDsure tokens'.                                                                                                |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] Enables GrIDsure authentication in WLA (**default value**)                                                                                                                          |
  |                                                                         | | [0] Disables the use of GrIDsure authentication                                                                                                                                         |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | Only leave this setting enabled if you need to support GrIDsure.                                                                                                                        |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | WrapCredentialProvider                                                  | | This entry specifies the GUID of the credential provider the agent is wrapping to provide two-factor authentication.                                                                    |
  |                                                                         | | This setting corresponds to the UI option under Credential Provider (Policy Tab)                                                                                                        |
  |                                                                         | | It could be set to either                                                                                                                                                               |
  |                                                                         | | Windows v1 Password Credential Provider - {6f45dc1e-5384-457a-bc13-2cd81b0d28ed}                                                                                                        |
  |                                                                         | | or                                                                                                                                                                                      |
  |                                                                         | | Windows v2 Password Credential Provider - {60b78e88-ead8-445c-9cfd-0b87f74ea6cd}                                                                                                        |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | It can be set to wrap some other 3rd party credential provider by                                                                                                                       |
  |                                                                         | | selecting **"Other credential Provider"** and specifying its GUID                                                                                                                       |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | Correct syntax is:                                                                                                                                                                      |
  |                                                                         | | **{GUID}**                                                                                                                                                                              |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Logging Level                                                           | | This policy setting configures client side log level. The setting                                                                                                                       |
  |                                                                         | | corresponds to the UI option called: 'Logging Level' (Logging tab).                                                                                                                     |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] Critical                                                                                                                                                                            |
  |                                                                         | | [2] Error                                                                                                                                                                               |
  |                                                                         | | [3] Warning (**default value**)                                                                                                                                                         |
  |                                                                         | | [4] Info                                                                                                                                                                                |
  |                                                                         | | [5] Debug                                                                                                                                                                               |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Attempt to return to primary Authentication Server every: [ ] minute(s) | | This policy setting configures the time, in minutes, after which the                                                                                                                    |
  |                                                                         | | WLA will attempt to return to its primary authentication server                                                                                                                         |
  |                                                                         | | following a failover to a defined secondary server.                                                                                                                                     |
  |                                                                         | | The setting corresponds to the UI option called: 'Attempt to return to                                                                                                                  |
  |                                                                         | | primary Authentication Server every [ ] minute(s)' (Communications tab).                                                                                                                |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | The **default value** for this setting is 10 minutes.                                                                                                                                   |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | This setting is only applicable if a failover server has been defined using the setting **'Failover Server (optional)'**                                                                |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Allow outgoing RDP connection without OTP                               | | This policy setting configures if WLA should be used for outgoing RDP                                                                                                                   |
  |                                                                         | | (remote desktop). The setting corresponds to the UI option called:                                                                                                                      |
  |                                                                         | | 'Allow outgoing RDP connection without OTP'.                                                                                                                                            |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] WLA is not enforced for outgoing RDP (**default value**)                                                                                                                            |
  |                                                                         | | [0] Outgoing RDP is subject to the use of OTP                                                                                                                                           |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | DomainUserOrGroup_In                                                    | | This policy setting configures what users are subject to using strong                                                                                                                   |
  |                                                                         | | authentication (OTP).                                                                                                                                                                   |
  |                                                                         | | When a group is added to **DomainUseOrGroup_In** (Only selected group must                                                                                                              |
  |                                                                         | | use SafeNet), then the **LocalUserOrGroup_Ex** is set to **'*'**. If pushing                                                                                                            |
  |                                                                         | | through GPO, the user needs to set the registry entry to **'*'**                                                                                                                        |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [ ] Not configured                                                                                                                                                                      |
  |                                                                         | | [DomainName.com\\Group Name] Only the provided group must use strong authentication                                                                                                     |
  |                                                                         | | [*] All users must use strong authentication (sets UI option: ‘Everyone must use SafeNet’)                                                                                              |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | If you define a group or multiple groups in this setting you must also                                                                                                                  |
  |                                                                         | | set **DomainUserOrGroup_Ex** with a value of **'*'**, meaning all but the                                                                                                               |
  |                                                                         | | defined groups of users are excluded from strong authentication                                                                                                                         |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Allow windows explorer without OTP                                      | | This policy setting configures if WLA should be used for accessing network resources using Windows Explorer                                                                             |
  |                                                                         | | The setting corresponds to the UI option called: 'Allow windows explorer without OTP' (Policy tab)                                                                                      |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] WLA is not enforced for outgoing Windows Explorer                                                                                                                                   |
  |                                                                         | | [0] Outgoing Windows Explorer is subject to OTP (**default value**)                                                                                                                     |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Credential Tile Filter                                                  | | This policy setting configures the appearance of credential provider tiles during Windows Logon                                                                                         |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [0] All credential tiles presented to the user will enforce SafeNet                                                                                                                     |
  |                                                                         | | authentication. Corresponds to the UI setting (Policy tab) called: ‘Only                                                                                                                |
  |                                                                         | | display SafeNet credential tile’ This is the **default value**                                                                                                                          |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] Authentication can be performed using SafeNet or third-party                                                                                                                        |
  |                                                                         | | credentials, but the Microsoft credential tile is hidden. Corresponds to                                                                                                                |
  |                                                                         | | the UI setting (Policy tab) called: ‘Hide Microsoft credential tile’                                                                                                                    |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [2] Authentication can be performed with third-party or Microsoft                                                                                                                       |
  |                                                                         | | credentials and ONLY third-party or Microsoft credential tiles are                                                                                                                      |
  |                                                                         | | displayed. Corresponds to the UI setting (Policy tab) called: ‘Hide                                                                                                                     |
  |                                                                         | | SafeNet credential tile and show all available’                                                                                                                                         |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | LocalUserOrGroup_In                                                     | | This policy setting configures what local users are subject to using strong authentication (OTP)                                                                                        |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [ ] Not configured                                                                                                                                                                      |
  |                                                                         | | [ComputerName\\Group Name] Only the provided group must use strong authentication                                                                                                       |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | if you define a group or multiple groups in this setting you must also                                                                                                                  |
  |                                                                         | | set **DomainUserOrGroup_Ex** with a value of **'*'**, meaning all but the                                                                                                               |
  |                                                                         | | defined local groups of users are excluded from strong authentication.                                                                                                                  |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | ThirdPartyFilter                                                        | | This can be set to **0** to “Allow all applications” or **1** to “Allow                                                                                                                 |
  |                                                                         | | SafeNet compliant applications”.                                                                                                                                                        |
  |                                                                         | | Some third-party credential provider software may conflict with the                                                                                                                     |
  |                                                                         | | working of the SafeNet Agent for Windows Logon. So, you can restrict                                                                                                                    |
  |                                                                         | | their access with this registry key and only allow certain supported                                                                                                                    |
  |                                                                         | | software to work with the agent                                                                                                                                                         |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Communication Timeout: [ ] seconds                                      | | This policy setting specifies the maximum timeout value for authentication requests sent to SAS                                                                                         |
  |                                                                         | | The setting corresponds to UI option: 'Communication Timeout: [ ] seconds' (Communications tab)                                                                                         |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | The **default value** is **120 seconds**                                                                                                                                                |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Use Proxy for SPS                                                       | | Select to connect to the Service Provider Server via proxy server                                                                                                                       |
  |                                                                         | | This setting is used to connect to the Service Provider Server via proxy                                                                                                                |
  |                                                                         | | server                                                                                                                                                                                  |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Domain groups are not nested in Local group                             | | This policy setting can be enabled to improve logon performance if                                                                                                                      |
  |                                                                         | | (and only if) domain groups are not nested inside local groups                                                                                                                          |
  |                                                                         | | The setting corresponds to UI option called: ‘Domain groups are not nested in Local group’ (Policy tab)                                                                                 |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] Improves WLA client performance when domain groups are not nested in local groups                                                                                                   |
  |                                                                         | | [0] Used when domain groups are not nested in local groups (**default value**)                                                                                                          |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Failover Server (optional)                                              | | This policy setting configures the secondary (failover) SAS                                                                                                                             |
  |                                                                         | | authentication server (or the Token Validation Proxy). Syntax is either                                                                                                                 |
  |                                                                         | | protocol followed by IP address and port (if non-standard) or protocol                                                                                                                  |
  |                                                                         | | followed by FQDN and port (if non-standard) , i.e.:                                                                                                                                     |
  |                                                                         | | 'http://1.2.3.4:8080' or 'https://server.domain.com'. The setting                                                                                                                       |
  |                                                                         | | corresponds to UI options (Communications tab): 'Failover Server                                                                                                                        |
  |                                                                         | | (optional)' and when the default value is used (or when a custom value                                                                                                                  |
  |                                                                         | | is used with https): 'Use SSL (requires a valid certificate)'                                                                                                                           |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Log File Location                                                       | | This policy settings configures the client log file path and its                                                                                                                        |
  |                                                                         | | naming. The setting corresponds to the UI option called: 'Log File                                                                                                                      |
  |                                                                         | | Location' (Logging tab)                                                                                                                                                                 |
  |                                                                         | | Refer to default value below for example syntax                                                                                                                                         |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **Default value:**  [C:\Program Files\SafeNet\Windows Logon\Log\AuthGINA-{date}.log]                                                                                                    |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | DomainUserOrGroup_Ex                                                    | | When any group is added to this setting, then the **DomainUserOrGroup_In**                                                                                                              |
  |                                                                         | | entry remains empty. You need to set **LocalUserOrGroup_In to** **'*'**                                                                                                                 |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Username                                                                | | This policy setting configures the proxy server username as used when                                                                                                                   |
  |                                                                         | | authenticating to the defined proxy server (if required), in order to                                                                                                                   |
  |                                                                         | | connect with the token validation service (e.g. SAS or a Token                                                                                                                          |
  |                                                                         | | Validation Proxy). The setting corresponds to the UI option called:                                                                                                                     |
  |                                                                         | | 'Username'.                                                                                                                                                                             |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | Correct syntax is: **'username'**                                                                                                                                                       |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | Setting 'Username' also assumes setting ‘Proxy Server’ and ‘Password’,                                                                                                                  |
  |                                                                         | | and may also require setting 'Use Proxy for SPS' (if applicable)                                                                                                                        |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Strip realm from UPN (username@domain.com will be sent as username)     | | This policy setting determines if a UPN (username@domain.com) is sent                                                                                                                   |
  |                                                                         | | to the authentication server as-is, or if the portion following the                                                                                                                     |
  |                                                                         | | username is removed (stripped).                                                                                                                                                         |
  |                                                                         | | The setting corresponds to the UI option (Communications tab) called:                                                                                                                   |
  |                                                                         | | ‘Strip realm from UPN (username@domain.com will be sent as username)’                                                                                                                   |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] Strips the @domain.com portion from the UPN when authenticating with SAS                                                                                                            |
  |                                                                         | | [0] WLA will not sanitize the username (**default value**)                                                                                                                              |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | This is useful when users log in with UPN sometimes and SAS username                                                                                                                    |
  |                                                                         | | at other times and you need to sanitize what is being sent from                                                                                                                         |
  |                                                                         | | different protected resources so that SAS can still authenticate the                                                                                                                    |
  |                                                                         | | user(s) consistently                                                                                                                                                                    |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Custom Logo                                                             | | This policy settings allows you to set a custom image in the logon                                                                                                                      |
  |                                                                         | | screen for compatible credential providers (customization is not                                                                                                                        |
  |                                                                         | | compatible with the Windows V2 provider)                                                                                                                                                |
  |                                                                         | | The setting corresponds to the UI option: 'Custom Logo' (Appearance tab)                                                                                                                |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **Example syntax**: 'C:\Program Files\SafeNet\Windows Logon\customLogo.bmp'.                                                                                                            |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | The custom logo must be a **bitmap** (.bmp) of **110 x 110** pixels and must be available locally on the client                                                                         |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | Enable Agent                                                            | | This policy setting configures the Windows Logon Agent (WLA) as                                                                                                                         |
  |                                                                         | | enabled or disabled. The setting corresponds to the UI option called:                                                                                                                   |
  |                                                                         | | ‘Enable Agent’ (Policy tab).                                                                                                                                                            |
  |                                                                         | | When enabled, WLA will be displayed at logon                                                                                                                                            |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | [1] WLA will be enabled and displayed at logon (**default value**)                                                                                                                      |
  |                                                                         | | [0] WLA will be disabled (remains installed and configured but is not used)                                                                                                             |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
  | FilterProcess                                                           | | This policy setting can be configured to exclude applications from                                                                                                                      |
  |                                                                         | | SAS authentication. For example, Outlook may prompt for authentication                                                                                                                  |
  |                                                                         | | using OTP when WLA is installed with default options. To exclude Outlook                                                                                                                |
  |                                                                         | | from using OTP, simply add the executable name (outlook.exe) to the                                                                                                                     |
  |                                                                         | | FilterProcess list of values. To exclude multiple applications from using                                                                                                               |
  |                                                                         | | OTP, use comma for separation, e.g. “outlook.exe,consent.exe”.                                                                                                                          |
  |                                                                         |                                                                                                                                                                                           |
  |                                                                         | | **NOTE:**                                                                                                                                                                               |
  |                                                                         | | The FilterProcess setting does not have an equivalent UI setting in                                                                                                                     |
  |                                                                         | | WinLogonManager, and can only be set directly in client registry or                                                                                                                     |
  |                                                                         | | using Group Policy.                                                                                                                                                                     |
  +-------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Deploying the WLA MSI
---------------------

**Follow these steps to deploy the WLA MSI to client machines:

- Create an MST file
- Copy Agent configuration file to client machines
- Deploy MST and MSI files
- Set the GPO order

.. note:: To deploy the SafeNet Windows Logon Agent MSI, you need to set **AGENTMODE** and **JSONFILEPATH** values. For this purpose, we use parameterized MSI Installation with the help of a **transform (.mst)** file

Create an MST file
------------------

To create an MST file, you need to install the **ORCA** tool. It is a free utility from Microsoft, available with the Windows SDK package (Developer Tool).

.. note:: You can download the **ORCA** tool `here <https://docs.microsoft.com/en-us/windows/win32/msi/orca-exe>`_

1. After the successful installation of the tool, **right-click** the SafeNet Windows Logon Agent MSI file (SafeNet Authentication Service Agent for Win 8-10-2012-2016 x64.msi) in the previously created shared folder

2. Click :guilabel:`Edit with Orca`

3. Click :guilabel:`Transform` > :guilabel:`New Transform`

4. In Orca Editor, click :guilabel:`Property` from **Tables** in the left pane

.. thumbnail:: /images/wla/orca_property.png

5. Double-click the value of property **AGENTMODE** and set it as **1**

.. thumbnail:: /images/wla/agentmode.png

6. Double-click the value of property **JSONFILEPATH** and set it to the path on the client machine where the Agent configuration file will be copied to

7.Click File > Generate Transform….

8.Save your Transform (.mst) file with a desired name.

Silent installation
===================

One approach to controlling the choices made to the installer during installation is to run the installation silently with parameters. This allows the customer to set key configuration items such as authentication server FQDN and logon mode.

The following section provides an example of silent installation :code:`/quiet` switch followed by product settings. A list of key product settings as well as useful generic MSI switches are then provided in the following tables.

Example of silent install for STA EU cloud that caches the Windows password and excludes administrators from the use of MFA:

.. Code-block:: powershell

   msiexec /i <installerName>.msi /quiet
   COMPANYNAME=swedemo TOKENVALIDATORLOCATION=cloud.eu.safenetid.com
   USESSL=s USEFAILOVER=0 LOGONMODE=1 EXEMPTADMINS=1

Example of silent installation *without* forced reboot:

::

    msiexec /i <installerName>.msi /quiet REBOOT=ReallySupress


.. note::
   In the above examples the installation will run without the user noticing. If the :code:`/quiet` switch is removed the installer will run in interactive mode, but with the applicable settings pre-populated.


WLA command line switches
-------------------------

The following table outlines :abbr:`WLA (Windows Logon Agent)` specific properties with possible values as well as their explanation (relating mostly to the GUI based options). The wording used here does not match the actual GUI options.

+--------------------------+--------+----------------------------------------------------------+
|MSI switch/key            |Value(s)|Explanation                                               |
+==========================+========+==========================================================+
|COMPANYNAME               |string  | Company name (any).                                      |
+--------------------------+--------+----------------------------------------------------------+
|TOKENVALIDATORLOCATION    |FQDN    | Defines primary authentication server                    |
+--------------------------+--------+----------------------------------------------------------+
|TOKENVALIDATORLOCATION2   |FQDN    || Defines secondary authentication server                 |
|                          |        ||                                                         |
|                          |        || Note: set the value to *primary* for STA                |
+--------------------------+--------+----------------------------------------------------------+
|USEFAILOVER               |1 (0)   || **1** = failover enabled                                |
|                          |        || **0** = no failover                                     |
|                          |        ||                                                         |
|                          |        || Note: this option **must** be used with key             |
|                          |        || *TOKENVALIDATORLOCATION2*                               |
+--------------------------+--------+----------------------------------------------------------+
|USESSL                    |s       || **s** = toggles use of SSL (requires certificates)      |
|                          |        || for primary server                                      |
+--------------------------+--------+----------------------------------------------------------+
|USESSL2                   |s       || **s** = toggles use of SSL (requires certificates)      |
|                          |        || for secondary server                                    |
+--------------------------+--------+----------------------------------------------------------+
|LOGONMODE                 |1 (0)   || **1** = Windows password is hidden (cached)             |
|                          |        || **0** = Windows password and MFA is required            |
+--------------------------+--------+----------------------------------------------------------+
|EXEMPTADMINS              |1 (0)   || **1** = Exempts administrators from using MFA           |
|                          |        || **0** = Everybody must use MFA                          |
+--------------------------+--------+----------------------------------------------------------+
|USEGRID                   |1 (0)   || **1** = GRIDSure is enabled                             |
|                          |        || **0** = GRIDSure is disabled                            |
+--------------------------+--------+----------------------------------------------------------+
|RDPWITHOUTOTP             |1 (0)   || **1** = Allows outgoing RDP connection without OTP      |
|                          |        || **0** = Outgoing RDP is subject to OTP                  |
+--------------------------+--------+----------------------------------------------------------+

.. attention::
   Note that examining the MSI package with logging you will find additional keys/switches that non-functional, possibly deprecated by Engineering. These include, but are not limited to :code:`EXEMPTADMINSCHECK`, :code:`EXEMPTADMINSCHECK1`, :code:`KEYFILEPATH`, :code:`KEYFILE` and :code:`USEGRIDCHECK`.

Generic MSI command line switches
---------------------------------

The following table outlines :abbr:`MSI (Microsoft Installer)` switches that can be useful when installing WLA.

+----------------+--------------+------------------------------------------------------------+
|MSI switch/key  |Value(s)      |Explanation                                                 |
+================+==============+============================================================+
| /i             |              | Basic install switch when run from command line            |
+----------------+--------------+------------------------------------------------------------+
| /quiet         |              | Installs package in silent mode (e.g. in background)       |
+----------------+--------------+------------------------------------------------------------+
| /passive       |              || Installer displays a progress bar to the user indicating  |
|                |              || an ongoing (silent) installation                          |
+----------------+--------------+------------------------------------------------------------+
| /log           | /L*V         | Creates an installation log file                           |
+----------------+--------------+------------------------------------------------------------+
| REBOOT         |ReallySupress || By default when running in silent mode the computer will  |
|                |              || automatically reboot on installation completion. To avoid |
|                |              || this behavior you can use REBOOT=ReallySupress instead.   |
|                |              || In this case the installation will complete on the next   |
|                |              || user initiated reboot                                     |
+----------------+--------------+------------------------------------------------------------+

Interactive installation
========================
For user controlled interactive installation, please refer to official product documentation.


Traffic
=======

The following traffic must be allowed for the :abbr:`WLA (Windows Logon Agent)` to function.

.. note::
   Allow or white-list hosts according to your STA hosting zone. If you are uncertain about what hosting zone to use, look at your operator (console) URL.

+-------+----+----------------------+----------------------+----------------------+
|Service|Port|EU Service Zone       |US Service Zone       |Classic Service Zone  |
+=======+====+======================+======================+======================+
|AuthN  |443 |cloud.eu.safenetid.com|cloud.us.safenetid.com|agent1.safenet-inc.com|
+-------+----+----------------------+----------------------+----------------------+
|Push   |443 |sps.eu.safenetid.com  |sps.us.safenetid.com  |sps.safenet-inc.com   |
+-------+----+----------------------+----------------------+----------------------+

TLS 1.2
-------
.. attention::
   SafeNet Trusted Access currently implements a requirement on TLS **v1.2**.

Some Operating Systems such as **Windows 7** does not support TLS 1.2 natively and the use of Windows Logon Agent will fail unless protocol support is modified.

To learn more, please refer to  `Microsoft documentation <https://support.microsoft.com/en-ie/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi>`_


Customizing the WLA logon experience
************************************

The default logon message(s) introduced by the Windows Logon Agent can be tailored to customer needs with messages replaced by modifying language files. This can be achieved either post installation (as seen below) or prior installation (by modifying the MSI package itself).


.. tip::
   The below instructions details a "textbook" approach to modifying the U/X. For demo purposes you can simply edit and save the existing file using any test editor (no need to backup the file or use the registry etcetera).

The following instructions changes the most often seen user dialog:

#. Press the :kbd:`Windwows` key and :kbd:`e` simultaneously to bring up **File Explorer**
#. Navigate to: :code:`"Program Files\SafeNet\Windows Logon\languages\en\"`
#. Copy and paste the file :file:`LogonClient.ccl` giving it a new name (e.g. :file:`LogonClient_v2.ccl`)
#. Open the new file in your text editor of choice and search and find: ";122"
#. Modify the current text with your own text, e.g.:

   .. Code-block::

	  Press ENTER for Push OTP!

#. Exit saving the file
#. Press the :kbd:`Windwows` key + :kbd:`r` simultaneously to bring up the Run command
#. Type **regedit** and press :kbd:`Enter` to launch the Registry Editor
#. Navigate to: :code:`HKEY_LOCAL_MACHINE\SOFTWARE\CRYPTOCard\AuthGINA`
#. Double-click to edit the key :code:`LocalizedMessages`
#. Change the key value to point to the new file (step 3 above)
#. Exit the Registry Editor
#. Log off or restart to review the implementation of your modified user dialogue.

.. thumbnail:: /images/wla/loginWithPush.png
   :title: Figure: Modified login text.
   :show_caption: true
|


Improving RDP User Experience (U/X)
===================================

As with the interactive login, when using :abbr:`RDP (Remote Desktop Protocol)` to connect to a :abbr:`WLA (Windows Logon Agent)` enabled host the default login screen presents multiple fields accompanied by text that may not be applicable to the customers use of the agent. Such field labels includes **"RDP User's IP"** and **"Please Enter your [PIN].."** as seen below.

.. thumbnail:: /images/wla/loginDefaultRDP.png
   :title: Figure: The default login screen over RDP.
   :show_caption: true
|
To address this issue there are two steps required:

* :ref:`Disable NLA on the target host <disable-nla>`

* :ref:`Modify the RDP file <modify-rdp-file>`

.. _disable-nla:

Disable NLA on the target host
------------------------------

#. Press the :kbd:`Windwows` key and :kbd:`e` simultaneously to bring up **File Explorer**
#. Righ-click on **Computer** and select :guilabel:`&Properties`
#. Select :guilabel:`&Advanced system settings`
#. Click the :guilabel:`&Remote` tab and uncheck :abbr:`NLA (Network Level Authentication)`:

   .. thumbnail:: /images/wla/disableNLA.png
      :width: 80%
      :title: Figure: To disable NLA make sure it is unchecked.
      :show_caption: true

#. Click :guilabel:`&OK` and exit

.. _modify-rdp-file:

Modify the RDP file
-------------------

#. Edit the RDP file to contain the following line:

   ::

       enablecredsspsupport:i:0

#. Save and close the file

.. warning::
   Make sure you understand any security implications of the above setting before implementing it.

Example RDP file
^^^^^^^^^^^^^^^^

The following is an example RDP file for a VM in Microsoft Azure. To use this as a template, save the content to a file with the extension :file:`.rdp` and modify line 23 (highlighted below) with the target IP address.

.. code-block:: text
   :emphasize-lines: 23, 47
   :linenos:

   use multimon:i:0
   desktopwidth:i:1920
   desktopheight:i:1080
   session bpp:i:32
   winposstr:s:0,3,0,0,800,600
   compression:i:1
   keyboardhook:i:2
   audiocapturemode:i:0
   videoplaybackmode:i:1
   connection type:i:7
   networkautodetect:i:1
   bandwidthautodetect:i:1
   displayconnectionbar:i:1
   enableworkspacereconnect:i:0
   disable wallpaper:i:0
   allow font smoothing:i:0
   allow desktop composition:i:0
   disable full window drag:i:1
   disable menu anims:i:1
   disable themes:i:0
   disable cursor setting:i:0
   bitmapcachepersistenable:i:1
   full address:s:40.69.70.188:3389
   audiomode:i:0
   redirectprinters:i:1
   redirectcomports:i:0
   redirectsmartcards:i:1
   redirectclipboard:i:1
   redirectposdevices:i:0
   autoreconnection enabled:i:1
   authentication level:i:2
   prompt for credentials:i:0
   negotiate security layer:i:1
   remoteapplicationmode:i:0
   alternate shell:s:
   shell working directory:s:
   gatewayhostname:s:
   gatewayusagemethod:i:4
   gatewaycredentialssource:i:4
   gatewayprofileusagemethod:i:0
   promptcredentialonce:i:0
   gatewaybrokeringtype:i:0
   use redirection server name:i:0
   rdgiskdcproxy:i:0
   kdcproxyname:s:
   drivestoredirect:s:
   enablecredsspsupport:i:0


Uninstalling
============

The :abbr:`WLA (Windows Logon Agent)` can be uninstalled either from **Control Panel**, by running the MSI again -or silently using the following command:

::

    msiexec /x <installerName>.msi

.. warning::
   If the Windows Logon Agent was installed using the provided :file:`.exe` then you cannot uninstall it using the :file:`.msi` and vice versa. Doing so may lead to a situation where the agent cannot be fully uninstalled.

Upgrading
=========


//TODO: Get clarification from R&D on upgrade procedure and supported *from* versions: upgrade from 2.x appears not to work.


::

    msiexec /i <installerName>.msi /quiet REINSTALLMODE=vomus REINSTALL=ALL

With regards to the use deployment tools it's important to note that :abbr:`WLA (Windows Logon Agent)` currently does not increment the :code:`ProductCode` value. Tools such as Microsoft SCCM uses this property to see if the software is already installed; The comparison of :code:`UpgradeCode` determines product family (e.g. WLA) and :code:`ProductCode` determines what version *is* installed versus what version is *about* to be installed.

.. note::
   With ProductCode being the same, this approach to upgrade will not work *unless* the MSI is modified and re-signed using a trusted certificate.


Registry keys
=============

Registry entries for :abbr:`WLA (Windows Logon Agent)` largely reflects GUI options in the Logon Manager app and GPO (ADMX), however some discrepancies exist where more control is available through registry than GUI or Group Policy. This includes but is not limited to the ability to set language file location.

The WLA registry keys are available under:

::

    HKEY_LOCAL_MACHINE\SOFTWARE\CRYPTOCard\AuthGINA
