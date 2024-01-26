### Footprinting IMAP/POP3

```bash
nmap 10.129.14.128 -sV -p110,142,993,995 -sC
```

### cURL Interaction

```bash
## Can observe how connection is being made
curl -k 'imaps://10.129.14.128' --user user:p4ssword -v
```

### OpenSSL Interaction

```bash
## TLS encrypted interaction POP3
openssl s_client -connect 10.129.14.128:pop3s

## TLS encrypted interaction IMAP
openssl s_client -connect 10.129.14.128:imaps
```

### IMAP3 Syntax

| Command    | Description                                                                 |
|------------|-----------------------------------------------------------------------------|
| 1 LOGIN username password	| User's login. |
| 1 LIST "" *	  | Lists all directories. |
| 1 CREATE "INBOX"	| Creates a mailbox with a specified name. |
| 1 DELETE "INBOX"	| Deletes a mailbox. |
| 1 RENAME "ToRead" "Important"	| Renames a mailbox. |
| 1 LSUB "" *	| Returns a subset of names from the set of names that the User has declared as being active or subscribed. |
| 1 SELECT INBOX	| Selects a mailbox so that messages in the mailbox can be accessed. |
| 1 UNSELECT INBOX	| Exits the selected mailbox. |
| 1 FETCH <ID> all	| Retrieves data associated with a message in the mailbox. |
| 1 CLOSE	| Removes all messages with the Deleted flag set. |
| 1 LOGOUT	| Closes the connection with the IMAP server. |


### POP3 Syntax

| Command       | Description                                                                 |
|---------------|-----------------------------------------------------------------------------|
| USER username	| Identifies the user.                                                        |
| PASS password	| Authentication of the user using its password.                              |
| STAT	        | Requests the number of saved emails from the server.                        |
| LIST	        | Requests from the server the number and size of all emails.                 |
| RETR id	    | Requests the server to deliver the requested email by ID.                   |
| DELE id	    | Requests the server to delete the requested email by ID.                    |
| CAPA	        | Requests the server to display the server capabilities.                     |
| RSET	        | Requests the server to reset the transmitted information.                   |
| QUIT	        | Closes the connection with the POP3 server.                                 |