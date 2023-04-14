# CARLA-Simulator-CompileSrcCode-Instruction

UE4 & CARLA Built from Source Code (Windows)

## Required Software:
 
Ensure that you have all these. Otherwise, the VS2017 x64 terminal will give error when compiling UE4…  
-	CMake generates standard build files from simple configuration files.
-	Git is a version control system to manage CARLA repositories.
-	Make generates the executables.
-	Python3 x64 is the main script language in CARLA. Having a x32 version installed may cause conflict, so it is highly advisable to have it uninstalled.
	Ensure that all the above stated are added into environmental path.
-	Visual Studio 2017 is also required. You will need the following installed. 
	•	Windows 8.1 SDK. Select it in the Installation details section on the right.
	•	x64 Visual C++ Toolset. In the Workloads section, choose Desktop development with C++. This will enable a x64 command prompt that will be used for the build. Check it up by pressing the Win button and searching for x64. Be careful to not open a x86_x64 prompt.

Other Visual Studio versions may cause conflict. Even if these have been uninstalled, some registers may persist. To completely clean Visual Studio from the computer, go to Program Files (x86)\Microsoft Visual Studio\Installer\resources\app\layout and run .\InstallCleanup.exe -full

## Getting UE4:

You will need to have your GitHub account linked to UE account so that you can have access their source code. You will need UE 4.24. Since CARLA 0.9.11, it is no longer possible for you to just download the UE4 provided by Unreal. You must compile from the source code. 

Clone the Repo: git clone --depth=1 -b 4.24 https://github.com/EpicGames/UnrealEngine.git 
Then CD into the Repo and run the patch:     
cd UnrealEngine 
powershell -Command "(New-Object System.Net.WebClient).DownloadFile('https://carla-releases.s3.eu-west-3.amazonaws.com/Backup/UE4_patch_wheels.patch', 'UE4_patch_wheels.patch')"
git apply UE4_patch_wheels.patch
Run the configuration scripts: 
Setup.bat
GenerateProjectFiles.bat

Now, we need to compile the engine. Open the UE4.sln file inside the source folder with Visual Studio (Epic recommends 2017 or 2019 versions). Make sure that in the build bar, it is selected the 'Development Editor', 'Win64' and, 'UnrealBuildTool' options. Check this guide if you need any help. Then, in the solution explorer, right-click in UE4 and select Build.

Once the solution is compiled you can open the engine to check that everything was correct launching the executable Engine\Binaries\Win64\UE4Editor.exe (Any of the exe should do fine since when I did it, UE4ditor.exe is not present…)

## Getting CARLA:

This section is the most troublesome one, because CARLA source code has missing files and various things needs to be done before hand. 
You need x64 VS2017 terminal for all process down below. 

Clone the Repo: git clone https://github.com/carla-simulator/carla

Only the assets package is yet to be downloaded. \Util\ContentVersions.txt contains the links to the assets for CARLA releases. These must be extracted in Unreal\CarlaUE4\Content\Carla. If the path does not exist, create it. Download the latest assets to work with the current version of CARLA.

Now we need to set the environment variable for CARLA. This is necessary for CARLA to find the Unreal Engine installation folder. By doing so, users can choose which specific version of Unreal Engine is to be used. If no environment variable is specified, the CARLA will look up in the directories and use the last version in search order. 

1. Open the Windows Control Panel and go to Advanced System Settings.
2. On the Advanced panel open Environment Variables....
3. Click New... to create the variable.
4. Name the variable as UE4_ROOT and choose the path to the installation folder of the desire UE4 installation.

There is a missing file called Version.h at the directory carla-0.9.11\LibCarla\source\carla. You can make a copy of Version.h.in and rename it as Version.h and put it in the same directory.

The last step is to finally build CARLA. There are different make commands to build the different modules. All of them run in the root CARLA folder.

make PythonAPI compiles the API client, necessary to grant control over the simulation. It is only needed the first time. Remember to run it again when updating CARLA. Scripts will be able to run after this command is executed. Command: make PythonAPI

make launch compiles the server simulator and launches Unreal Engine. Press Play to start the spectator view and close the editor window to exit. Camera can be moved with WASD keys and rotated by clicking the scene while moving the mouse around. Command: make launch 

The project may ask to build other instances such as UE4Editor-Carla.dll the first time. Agree in order to open the project. During the first launch, the editor may show warnings regarding shaders and mesh distance fields. These take some time to be loaded and the city will not show properly until then.

## Test Compiled CARLA Simulator

Finally, let's test the simulator. Inside PythonAPI/examples and PythonAPI/util there are some example scripts that may be especially useful for starters. The following commands will spawn some life into the town, and create a weather cycle. Each script should be run in one terminal.

Run the following to test… (You can initialize CARLA’s environment by pressing the Play button in the UE4 editor…)

### Terminal A 

cd PythonAPI/examples
python3 spawn_npc.py  

### Terminal B

cd PythonAPI/examples
python3 dynamic_weather.py 

The PythonAPI will be built based on the installed Python version, but the docs will use Python3 by default.

## Command Summary

| Command | Description |
| ------------- | ------------- |
| make help | Prints all available commands. |
| make launch | Launches CARLA server in Editor window. |
| make PythonAPI | Builds the CARLA client. |
| make package | Builds CARLA and creates a packaged version for distribution. |
| make clean | Deletes all the binaries and temporals generated by the build system. |
| make rebuild | make clean and make launch both in one command. |
