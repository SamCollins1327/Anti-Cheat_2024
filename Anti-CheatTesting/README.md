# Test Descriptions
I1 - File Integrity.
This assesses weather or not the anti-cheat runs file integrity checks. This should be one of the most basic defenses run. We test this by patching the game binary in the Interactive Disassembler (IDA), then running the game. 

I3 - Code injection.
This tests the code injection countermeasures for each anti-cheat solution, this is subdivided into three tests. 
1) We try standard code injection, by allocating memory within the process then calling LoadLibraryA on our DLL using ```loadlibraryinjector.exe``` and ```Dll1.dll```. 
2) We try manually mapping our code into the game process using ```Extreme Injector v3.exe``` and ```Dll2.dll```. 
3) We try injection via thread hijacking using ```Extreme Injector v3.exe``` and ```Dll3.dll```.
In all cases the code injected in harmless 'hello world' code. 


H1 - Obfuscation.
We manually check if important game files are obfuscated in any way. To do this we use binwalk to measure and record the entropy of the main PE file for each game. 

H2 - Debugging.
To check simple anti debug we attach GDB to the running game process. If we can attach Cheat Engine to the game process, we then also attach the Cheat Engine debugger (In all cases the reaction to both debuggers was identical). We note a clear direction for future work in this test case would be to measure reactions to more advanced debuggers, such as scyllahide and titanhide for x64dbg, as well as hypervisor based debuggers like hyperdbg. 

H3 - Process scanning.
Here we are checking weather the anticheat is enumerating running processes and open windows, this is further divided into two sub-cases for two common reverse engineering tools: (1) IDA, and (2) Cheat Engine. 
1) Open the IDA static analysis tool, load an executable not associated with the game, then run the game. We see if the game opens or not and if any warnings are given. This is to test if the anti-cheat is checking open processes, and flags the use of IDA as a possible threat.  
2) We start Cheat Engine memory scanning tool and then open the game. If the game opens then we attach Cheat Engine to the game process. Cheat Engine is a dynamic analysis tool for which the game must be running to use, unlike IDA. 


P1 - Account Bans.
Check that each service bans players who they detect cheating. This is quite a baseline test but is important for completeness. In some cases where despite 'best efforts' we aren't banned, we take second hand information from the provider as results here. 

P2 - Hardware ID.
Collect a list of the Hardware ID (HWID) information gathered by each anti-cheat to use in it's HWID based bans using a Windows Management Instrumentation (WMI) trace. We rank this based which is the most thorough, and which rely purely on WMI vs those which gather the information more directly. In some cases where one account was banned, a second was banned via HWID, such was the case for Valorant and Fortnite. A HWID 'spoofer' could be used to bypass these bans, however to avoid the presence of such software possible poisoning other results, we simply used multiple devices in such cases.  


K1 - Test Mode.
Here we see if each game will run with Windows test signing enabled (```Bcdedit.exe -set TESTSIGNING ON```). This would allow us to load our own unsigned kernel drivers arbitrarily to cheat in game. 

K2 - Vulnerable Drivers.
Load the vulnerable ```iqvw64e.sys``` driver on the target machine, open the game, play two multiplayer games. We did this with an edited version of KDMapper  (```kdmapper_no_unload.exe```) which doesn't unload the vulnerable driver post injection, using the ```HelloWorld_EntryOnly.sys``` driver which enters then runs no threads, the same test could be carried out with an application like OSR driver loader. This is to evaluate whether the anti-cheat will check loaded drivers for known vulnerable software. 

K3 - Kernel Injection.
Open the game, then using KDMapper (```kdmapper.exe```) and ```iqvw64e.sys```, inject the basic entry only ```HelloWorld_EntryOnly.sys``` driver into the kernel.  This checks if the anti-cheat can detect an injection at the kernel level, and if it takes any action regardless the content of the driver. 

K4 - Module Detection.
Using KDMapper  (```kdmapper.exe```) and ```iqvw64e.sys```, inject the ```HelloWorld_WithThread.sys``` driver into the kernel and start a single thread, close the command line and unload the driver, then open the game and play two games. This evaluates weather anti-cheat can locate the manually mapped driver without the vulnerable kernel driver present or having observed the injection.   
