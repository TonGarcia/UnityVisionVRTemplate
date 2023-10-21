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


## Fully immersive space

1. Unity uses "Metal rendering" + "Apple ARKit" (so even for VR it is necessary AR kit which track body and objects on real world)
2. Immersive types:
   1. [Fully immersive (cover the entire vision field)](https://developer.apple.com/videos/play/wwdc2023/10093)
   2. [Immersive (your content passthrough mixed to the reality)](https://developer.apple.com/videos/play/wwdc2023/10088/)
      1. Ps.: RecRoom from Against Gravity (check cross ref for AutismyVR)

### Workflow (from Unity to the device)

1. **XR Setup** step
2. PS.: Recompile native plug-ins, if any. If the native is already raw source code or .mm file it is good to go
3. Run on XCode like on **XR Setup** step

### Prepare Graphics

1. Set **Universal Render Pipeline** 
2. "**Static foveated rendering**", available on URP, (concentrates more pixel density in the center of eye focused area)
   1. "**Static foveated rendering**" works with all URP features:
      1. HDR
      2. Camera Stacking
      3. Post-Processing
      4. and so on
   2. as visionOS renders happens in a nonlinear space, so there are **Shader macros handle remapping** to address it
   3. Enhances the visual experience
3. **Single-pass instanced rendering**: supports Metal graphics API (enabled by default) = 1 drawing call for both eyes
4. [**Depth compositing**](https://www.youtube.com/shorts/oh12OXjxALo): make sure app is writing to the depth buffer for every pixel correctly
   1. examples (shaders):
      1. custom SkyBox = it is very far away to user to create infinite feeling. It writes a depth of zero with reverse Z 
      2. water effect
      3. transparency effects

### Controllers Inputs

1. **Controllers**: people will use **Hands** and **Eyes** to interact with the content
2. Interactions approaches:
   1. **XR Interaction Toolkit**: abstract input which adds hand tracking
      1. Features:
         1. high level interaction system
         2. works for 3D and UI (2D Canvas) objects
         3. author once for hands and controllers (XRI abstracts the type of input)
            1. **XRI translate hand tracking into actions that the app can respond to**
         4. --> best for cross platform inputs management
         5. supports 3D **interactions** (hover, grab and select in 3D space and UI for 3D spatial worlds)
         6. includes **locomotion** for comfortable navigation on a fully immersive space 
         7. **visual feedback for input**: XRI enables us to define the visual reactions for each input constraint
            1. as people interact with the virtual world, visual feedback is important for immersion
      2. Architecture:
         1. **Interactable**: objects in our scene that can receive input
         2. **Interactor**: how people can interact with our interactables
         3. **Interaction Manager**: integrate the 2 components together
         4. USING IT:
            1. **Interactable Component**:
               1. add **Interactable Component** to the Object. Types:
                  1. **Simple** = marks object as receiving interactions = subscribe to events like SelectEntered or SelectExited
                  2. **XR Grab Interactable** = the object will follow the **interactor** (person/character) around inheriting its velocity
                  3. **Teleport** = teleport to TeleportArea and TeleportAnchor --> enables to define areas or points for the player teleport to
                  4. **Custom interactable**
            2. **Interactor**: list of interactables that could potentially hover over or select each frame. Types:
               1. **Direct**: interactables that you are touching it. When a person's hands touch an or close to interactable object
               2. **Ray**: interacting from far away interactables = creates an configurable angle (like throw signal)
                  1. if the object is Grab Interactable so it move the objects to user hands
                  2. it is also used to limit the degrees of freedom for the Grab
               3. **Socket**: shows an area that can accept an object --> interactors not attached to hands (live somewhere in the world like invisible colliders)
               4. **Poke**: similar to direct interactor, but beyond touch it requires a specific/correct motion to works
               5. **Gaze**: interacting by looking = Ray Interactor extension to make gaze easier
            3. **Interaction Manager**:
               1. middleman between **Interactors** and **Interactables** for exchange interactions
               2. primary role: initiate changes in interaction state within a designated group of registered **Interactors** and **Interactables**
               3. usually it is a singleton like a GameManager
                  1. it is possible to create many **Interaction Managers** and each of them know it own context (**Interactors** and **Interactables**)
                  2. multiple **Interaction Managers** are used to enable or disable a set of interactions
            4. **XR Controller Component**: it is used to make sense of the input data you'll receive
               1. it takes input actions from the hands or a tracked device and passes it to **Interactors** so **Interactor** can decide to select or activate something based on that input
               2. you need to **bind Input Action References** for each **XR Interaction States** such as Select.
               3. **XR Controller** can handle each hand or act both as same
   2. **Unity Input System**: map to system gestures: 
      1. Bind to the tap system gesture => Binding Properties:
         1. <XRController>{LeftHand}/pinchValue
         2. <XRController>{LeftHand}/pinchPosition
         3. <XRController>{LeftHand}/pinchRotation
      2. Bind to the look position:
         1. <XRController>{LeftHand}/pointerPosition
         2. <XRController>{LeftHand}/pointerRotation
   3. **Unity Hands Package**: Low-level access to hand joint data
      1. consistent data across platforms
      2. usage:
         1. identify gestures (like thumbs up)
         2. translate that gesture into a gameplay action
         3. Ps.: it is powerful but very challenge due variety of range of motions and hands sizes
      3. sample:
         1. ``` isIndexExtended(XRHand hand) { hand.GetJoint(XRHandJointID.Wrist).TryGetPose(out var wristPose) &&  hand.GetJoint(XRHandJointID.IndexTip).TryGetPose(out var tipPose) } ```
         2. it can be called on "**OnHandUpdate**" event
      4. goal: it is used to display a virtual hand that reflects the real hands moves 

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
