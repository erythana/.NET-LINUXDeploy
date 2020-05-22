### Deploy your Visual-Studio application to Linux and run it with .NET Core automatically
You can check the Output-Window in VisualStudio for additional information on whats happening in the BuildEvent

- Requirements
 - A Visual Studio project where you set up -an PostBuild Event (Application - Propertieies - Buildevents - Postbuild Events)
 - Putty-Tools: **plink**, **pscp**  and **puttygen**
  - .NET Core on your Remote-Linux Machine (Details on how to set everything up below)
 - A SSH Private Key (so the password isnt in your Build-Event! - Details below)
------------
### How to set everything up
 - Visual Studio Project
 Create a VisualStudio Project, open the projects settings, go to the Build-Tab and copy the contents of the Build-Script into the Post-Build Event Window - change the variables from the CHANGE VARIABLES section according to your settings/paths.
 - Download PuttyLink and PuttySCP from https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
 - On your Linux-Machine: Download .NET Core SDK - in the following example i used .NET Core 3.1 ARM32 for my Raspberry Pi 4:
```bash
wget https://download.visualstudio.microsoft.com/download/pr/349f13f0-400e-476c-ba10-fe284b35b932/44a5863469051c5cf103129f1423ddb8/dotnet-sdk-3.1.102-linux-arm.tar.gz
```
Create tmp Directory and unpack the file to the new directory:
```bash
mkdir dotnet-tmp
tar zxf dotnet-sdk-3.1.102-linux-arm.tar.gz $HOME/dotnet-tmp
```
Move everything to /usr/share/dotnet so it is available for all users and set permissions accordingly:
```bash
sudo mv dotnet-tmp/ /usr/share/dotnet
sudo chmod 755 /usr/share/dotnet
```
Optional (to make dotnet available for you in your ssh session after each reboot): Add these lines
```bash
export DOTNET_ROOT=/usr/share/dotnet
export PATH=$PATH:/usr/share/dotnet
```
to the end of your bashrc file:
```bash
sudo nano ~/.bashrc
```
 - Create a private key to connect to your SSH Session without using a password (in this case, using a password would be unsafe because you possibly could deploy it to Source-Control):
Open Puttygen on your Windows-Machine, check "RSA" and make sure you create a key with at least 2048 bits. Click on "Generate" and move your cursor in the window to generate the key.
Then click on "Save private key", choose yes to save without password and save it to "sshprivatekey.ppk". 
Now copy the public key (from within the generator window) and connect to your linux machine with ssh (manually). Open/Create the file authorized_keys like this:
```bash
sudo nano ~/.ssh/authorized_keys
```
and copy the public key into this file. Exit your running ssh connection.
- Now everything should be set up and you can try to debug your solution!

### Known Problems
- When Debugging the solution, VisualStudio (the BuildEvent) freezes because it waits for the Application (on Linux) to close. Close the App there and everything continues.
