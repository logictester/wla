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
^^^^^^^^
This document serves as a more thorough guide on the automation, configuration and installation tasks pertaining to the Windows Login Agent (WLA). Specifically, this document outlines the various command line switches, their properties, values and descriptions for usage with either interactive, silent, local as well as remote installation. The guide also documents how to create modified custom MSI based on the GA package and the GPO and registry settings that can be used to substitute or complement install-time parameters. 





Customizing the WLA logon experience
====================================

The default logon message(s) introduced by the Windows Logon Agent can be confusing. Luckily, messages can be replaced by modifying language files, either after installation or at installation (by modifying the MSI package itself). 

The following instructions changes the most often seen user dialogue:

.. tip::
   The below instructions details a "textbook" approach to modifying the U/X. For demo purposes you can simply edit and save the existing file using any test editor (no need to backup the file or use the registry etcetera).


#. Press the :kbd:`Windwows` key and :kbd:`e` simultaneously to bring up **File Explorer**
#. Navigate to: :code:`Program Files\SafeNet\Windows Logon\languages\en\`
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


.. figure:: /images/wla/loginWithPush.png
   :width: 100%

   Figure: Modified login text.
	  
Improving RDP User Experience (U/X) 
===================================

When using :abbr:`RDP (Remote Desktop Protocol)` to connect to a :abbr:`WLA (Windows Logon Agent)` enabled host the default login screen presents multiple fields accompanied by text that may be difficult to understand to end-users and that may not be applicable to the organization's implementation of WLA (use of enabled features). For instance the screen presents field labels that are non meaninful, such as: **"RDP User's IP"** and **"Please Enter your [PIN].."**.

.. figure:: /images/wla/loginDefaultRDP.png
   :width: 100%

   Figure: The default login screen over RDP is not user-friendly.
 
To address this issue there are two steps required:

* **Disable NLA on the target host**
* **Modify the RDP file**

Disable NLA on the target host
------------------------------

#. Press the :kbd:`Windwows` key and :kbd:`e` simultaneously to bring up **File Explorer**
#. Righ-click on **Computer** and select :guilabel:`&Properties`
#. Select :guilabel:`&Advanced system settings`
#. Click the :guilabel:`&Remote` tab and uncheck :abbr:`NLA (Network Level Authentication)`:

   .. figure:: /images/wla/disableNLA.png
      :width: 80%

      Figure: To disable NLA make sure it is unchecked.

#. Click :guilabel:`&OK` and exit

Modify the RDP file
-------------------

To disable this screen you can edit the RDP file to contain the following line:

::

    enablecredsspsupport:i:0 


.. warning::
   Make sure you understand any security implications of the above setting before implementing it.

Example RDP file 
----------------
The following is an example RDP file for a VM in Microsoft Azure. To use this as a template, save the content to a file with the extension :file:`.rdp` and modify line 23 (highlighed below) with the target IP address.

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


Deployment
==========


Traffic
-------

The following traffic must be allowed for the :abbr:`WLA (Windows Logon Agent)` to function.

.. note::
   Allow or whitelist hosts according to your STA hosting zone. If you are uncertain about what hosting zone to use, look at your operator (console) URL.

+-------+----+----------------------+----------------------+----------------------+
|Service|Port|EU Service Zone       |US Service Zone       |Classic Service Zone  |
+=======+====+======================+======================+======================+
|AuthN  |443 |cloud.eu.safenetid.com|cloud.us.safenetid.com|agent1.safenet-inc.com|
+-------+----+----------------------+----------------------+----------------------+
|Push   |443 |sps.eu.safenetid.com  |sps.us.safenetid.com  |sps.safenet-inc.com   |
+-------+----+----------------------+----------------------+----------------------+

TLS 1.2
-------
SafeNet Trusted Access currently implements a requirement on TLS **v1.2**. 
