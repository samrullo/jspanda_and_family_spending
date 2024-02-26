First you install FileZilla client.

Then You create new connection as below as described by ChatGPT.

You don't have to convert to ppk format. FileZilla supports .pem format as well.


To connect to a server using SSH keys with FileZilla, you need to follow these steps:

1. **Generate SSH Keys**: If you haven't already, you'll need to generate an SSH key pair. On Unix-like systems, you can do this with the `ssh-keygen` command. On Windows, you can use PuTTYgen or the Git Bash terminal.

2. **Install FileZilla**: Ensure that FileZilla is installed on your computer. You can download it from [the official FileZilla website](https://filezilla-project.org/).

3. **Export the Private Key (if necessary)**: If your private key is not in a format that FileZilla understands (for example, if it's a PuTTY `.ppk` file), you'll need to convert it to the OpenSSH format. You can use PuTTYgen to export your private key to an OpenSSH format if necessary.

4. **Configure FileZilla to Use the SSH Key**:
   - Open FileZilla.
   - Go to "Edit" > "Settings" (or "FileZilla" > "Preferences" on a Mac).
   - In the settings window, go to "Connection" > "SFTP".
   - Click on "Add key file...".
   - Navigate to your private key file, select it, and click "Open". If the key is not in the correct format, FileZilla will offer to convert it for you.
   - If you had to convert the key, you'll be prompted to save the converted key. Save it with a different name to preserve the original.
   - Click "OK" to close the settings window.

5. **Set Up a New Connection**:
   - Go to "File" > "Site Manager".
   - Click "New Site" and name it appropriately.
   - Set the following:
     - "Host": The IP address or domain name of the server you wish to connect to.
     - "Port": The port your SFTP server is listening on (default is 22 for SFTP).
     - "Protocol": Select "SFTP - SSH File Transfer Protocol".
     - "Logon Type": Select "Key file".
     - "User": Enter your SSH username.
     - "Key file": If the path to your private key isn't already filled in, browse to the location of your key file (the one you added in the SFTP settings).
   - Click "Connect" to save and connect.

If everything is set up correctly, FileZilla will use the provided SSH key to authenticate with the server, and you'll be able to transfer files securely. If you encounter any issues, check the error messages provided by FileZilla for clues as to what went wrong.
```