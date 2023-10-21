# UnityTemplate
Unity GitHub Repo Template

*Remember to copy and paste `.gitattributes` & `.gitignore` into it project sub folder.    
**Remember to protect it new repository branchs

# XR Setup

1. Install XCode Beta that includes visionOS (https://developer.apple.com/download/applications/)
2. Download XCode simulators, including visionOS
3. Download compatible Unity version (2022.3.5f1 or newer)
4. (on Unity) File > Build Settings > Switch platform to visionOS
   1. check "Development Build"
   2. for "Run in XCode" select the location for the XCode Beta which supports visionOS
   3. for "Run in XCode as" select "Debug"
   4. click on "Player Settings"
      1. setup a Bundler Identifier
      2. change the version to your version strategy pattern
      3. API compatibility level: ".NET Standard 2.1"
      4. Target SDK: "Simulator SDK"
      5. Active input handling: "Input System Package (new)"
5. (on Unity) Window > Package Manager
   1. for the filter "Package" select: "Unity Registry"
   2. search by: "xr"
   3. install all the components:
      1. Apple ARKit XR Plugin
      2. AR Foundation
      3. OpenXR Plugin
      4. XR Hands
      5. XR Interaction Toolkit
      6. XR Plugin Management
6. (on Unity) Edit > Project Settings > XR Plug-in Management
   1. Apple ARKit
      1. Requirement: required
      2. Face Tracking: checked (check it)
   2. OpenXR:
      1. Render Mode: Single Pass Instanced
   3. XR Interaction Toolkit
      1. check: "Use XR Device Simulator in scenes"
      2. check: "Instantiate in Editor Only"
      3. XR Device Simulator must be a prefab pre-selected: "XR Device Simulator"
      4. **do not** check old layers
7. (on Unity) Building
   1. File > Build Settings
      1. Build > Clean Build = export it into "Builds/VisionOS" folder
      2. Open generated XCode file
         1. on top select vision simulator as target: "Unity-VisionOS > Apple Vision Pro"
         2. set app category
         3. set Display Name (it should be filled by Unity settings)
         4. Run the project to get simulator running and activated to be available for Unity
      3. (Unity Build Settings) To run on Emulator click: "Build and Run"

PS.: on Unity 2022.3.11f1 and XCode v15.1 BETA it is not filling version and DisplayName correctly, but for simulator it is enough. The name on Unity Player Settings is displayed on the app name on VisionOS Simulator


# Errors HotFix

## IF Unity incompatible version
1. IF on MacOS:
   1. install LFS command globally: `brew install git-lfs`
   2. install git lfs to repo: `git lfs install`
   3. pull LFS files: `git lfs pull`
1. If plastic error:
   1. Run: `plastic --configure` (or open plasitc)
   1. Sign In


# Rider <> Unity tips
1. Video for more information: https://www.youtube.com/watch?v=O1oOAM-AdbE
2. If Rider did not tagged the project as Unity Project, so it is necessary to open the root project folder on Rider, so Rider will set it up correctly

# Git Large Files

1. Duplicate the .gitignore from root dir to inside the Game Project dir
2. Install: 
   1. download and run the installer
   2. `git lfs install`
3. Add large files extension: 
   1. `git lfs track "*.psd" "*.png" "*.jpg" "*.jpg" "*.gif" "*.mp4" "*.mp3" "*.fbx"`
   2. `git lfs track "**.psd" "**.png" "**.jpg" "**.jpg" "**.gif" "**.mp4" "**.mp3" "**.fbx" "**.dll"`
4. Attributes tracker: `git add .gitattributes`
5. If git not tracking any extension run it: `git lfs migrate import --include="*.extension"`

# Unity Tips
1. Switch graphic manipulation: **QWERTY**
1. Lock Tab: lock Inspector tab to be able to select many Hierarchy elements without switching it Inspector visualization
