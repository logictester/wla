.. Documentation master file, created by
   sphinx-quickstart on Sat Mar  6 02:48:40 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.
===========================================
Windows Logon Agent (WLA)
===========================================

.. toctree::
   :hidden:
   :maxdepth: 3
   

   index
      



Overview
********

WIP




Deployment
**********

WIP 

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
   Note that examining the MSI package with logging you will find additional keys/switches that are possibly deprecated by Engineering, but nonetheless not functional. These include, but are not limited to :code:`EXEMPTADMINSCHECK`, :code:`EXEMPTADMINSCHECK1`, :code:`KEYFILEPATH`, :code:`KEYFILE` and :code:`USEGRIDCHECK`.

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

The Windows Logon Agent (WLA) can be uninstalled either from **Control Panel**, by running the MSI again -or silently using the following command:

::

    msiexec /x <installerName>.msi

.. warning::
   If the Windows Logon Agent was installed using the provided :file:`.exe` then you cannot uninstall it using the :file:`.msi` and vice versa. Doing so may lead to a situation where the agent cannot be fully uninstalled. 

Upgrading
=========
WIP


::

    msiexec /i <installerName>.msi /quiet REINSTALLMODE=vomus REINSTALL=ALL

.. note::
   With regards to deployment tools it's important to note that WLA currently does not increment the **ProductCode** value. Tools such as **Microsoft SCCM** (formerly known as SMS) uses this property to see if the software is already installed; The presence of **UpgradeCode** determines product family (e.g. WLA) and **ProductCode** determines what version *is* installed versus what version is *about* to be installed. 
   With ProductCode being the same, :code:`{523727B0-D5D5-4392-935B-BFEAA70F29A6}`, this approach to upgrade will not work *unless* the MSI is modified and re-signed using a trusted certificate.


Registry keys
=============

Registry entries for WLA largely reflects GUI options in the Logon Manager app and GPO (ADMX), however some discrepancies exist where more control is available through registry than GUI or Group Policy. This includes but is not limited to the ability to set language file location.

The WLA registry keys are available under:

::

    HKEY_LOCAL_MACHINE\SOFTWARE\CRYPTOCard\AuthGINA

