### Windows Authentication Process Overview

![Alt text](/OSCP/Methodology%20Notes/Attacks/Password/Assets/image.png)

### Local Security Authority Subsystem Service - LSASS

| **Authentication Packages**   | **Description**   |
| --------------|-------------------|
|   Lsasrv.dll    |	The LSA Server service both enforces security policies and acts as the security package manager for the LSA. The LSA contains the Negotiate function, which selects either the NTLM or Kerberos protocol after determining which protocol is to be successful. | 
|   Msv1_0.dll  |	Authentication package for local machine logons that don't require custom authentication.   |
|   Samsrv.dll	|   The Security Accounts Manager (SAM) stores local security accounts, enforces locally stored policies, and supports APIs.    |
|    Kerberos.dll   |	Security package loaded by the LSA for Kerberos-based authentication on a machine.  |
|   Netlogon.dll    |	Network-based logon service.    |
|    Ntdsa.dll  |  This library is used to create new records and folders in the Windows registry.  |

