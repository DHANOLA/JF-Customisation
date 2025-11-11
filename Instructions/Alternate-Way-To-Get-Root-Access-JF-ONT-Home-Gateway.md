# Alternate way to get root access in JF ONT for newer firmwares with router specific encryption keys

*Disclaimer: - This is Only for educational purposes, No one is responsible for any type of damage. Any wrong step might brick your router. So be aware.*

1. First of all, follow [this guide](https://github.com/JFC-Group/JF-Customisation/blob/master/Instructions/Get-Any-File-From-JF-ONT-Home-Gateway.md) to get the file at `/flash/secure/key.txt` which is the encryption key of your backup config.
2. Go to your router admin page and download a backup of your router settings configuration from Administrator > Maintenance.
   
   **Note:** In newer firmware version the option is hidden in the webpage. Find the Backup/Restore HTML Form and uncomment the section. It should look like this:-

    ```html  
    <form name="tf1_frmBackupSaveCurrentSettings" method="post" action="?action=backup">
    <input type="hidden" name="token" value="sometoken">
    ```
   
4. Decrypt the config (`.enc` file) using the command `openssl aes-128-cbc -d -pass file:key.txt -in input.enc -out output.txt` (Change `input.enc` and `output.txt` respectively to your requirements).

   **Note:** In newer firmware like R2.49 for JCOW414 this method not working, please try using the [keyguesser.py](https://github.com/JFC-Group/JF-Customisation/blob/main/keyguesser.py). This script guess and identify the encryption key used to encrypt the JioFiber config file downloaded from the router's admin page
   
5. Open the decrypted config file.
6. Change the first line of the decrypted config file like this:

   ```
   config.userdb = {} os.execute("/usr/sbin/telnetd"); os.execute("/pfrm2.0/bin/iptables -I fwInBypass -p tcp --dport 23 -m ifgroup --ifgroup-in 0x1/0x1 -j ACCEPT"); os.execute("echo -e \"password\npassword\" | passwd root");
   ```
   
7. Ensure there is no line break in the line you just pasted. The whole content should be in a single line and the line should start with `config` otherwise this isn't gonna work.
8. Encrypt the config using the command `openssl aes-128-cbc -pass file:key.txt -in input.txt -out output.enc` (Change `input.txt` and `output.enc` respectively).
9. The output file should have the same name as your actual backed up config file in step 2.
10. Restore the new backup file in Router Admin page at Administrator > Maintenance.
11. Done! Now Telnet into the Router and use username as `root` and password as `password`.
