## Active Directory

Active Directory (AD) is a directory service for Windows enterprise environments. It is based on the protocols x.500 and LDAP that came before it and still utilizes these protocols in some form today. 

It is a distributed, hierarchical structure that allows for centralized management of an organization’s resources, including users, computers, groups, network devices and file shares, group policies, devices, and trusts. AD provides **authentication, accounting, and authorization functions** within a Windows enterprise environment. 

## Overarching Enumeration Principles

Key concept: Looking for every possible avenue that will provide us a potential route inside.

When starting enumeration, we would first use passive resources, starting wide in scope and narrowing down. Once we exhaust our initial run of passive enumeration, we will need to examine the results and then move into active enumeration.

### Key Data Points

|   Data Point  |	Description |
|---------------|---------------|
|   AD Users    |	We are trying to enumerate valid user accounts we can target for password spraying. |
|   AD Joined Computers | 	Key Computers include Domain Controllers, file servers, SQL servers, web servers, Exchange mail servers, database servers, etc. |
|   Key Services    |	Kerberos, NetBIOS, LDAP, DNS    |
|   Vulnerable Hosts and Services	|   Anything that can be a quick win. ( a.k.a an easy host to exploit and gain a foothold)  |

### Scanning

Generally, it is good practice to save whatever network traffic we capture for future analysis when needed.

### Identifying Users

We will need to find a way to establish a foothold in the domain by either obtaining clear text credentials or an NTLM password hash for a user, a SYSTEM shell on a domain-joined host, or a shell in the context of a domain user account. Obtaining a valid user with credentials is critical int he early stage.

## Gaining Foothold

At this point, we have completed our initial enumeration of the domain. We obtained some basic user and group information, enumerated hosts while looking for critical services and roles like a Domain Controller, and figured out some specifics such as the naming scheme used for the domain. 

## Credentialed Enumeration

Now that we have acquired a foothold in the domain, it is time to dig deeper using our low privilege domain user credentials. Since we have a general idea about the domain's userbase and machines, it's time to enumerate the domain in depth. 

We are interested in information about domain user and computer attributes, group membership, Group Policy Objects, permissions, ACLs, trusts, and more. We have various options available, but the most important thing to remember is that most of these tools will not work without valid domain user credentials at any permission level. So at a minimum, we will have to have acquired a user's cleartext password, NTLM password hash, or SYSTEM access on a domain-joined host.