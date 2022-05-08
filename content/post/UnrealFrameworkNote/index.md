+++
author = "NekoRAM7"
title = "UE Framework-How to start the game"
date = "2022-05-08"
description = "An article to show the procedure to start a game powered by Unreal Engine."
tags = [
    "GameDev",
    "UnrealEngine"
]
categories = [
    "GameDev",
    "UnrealEngine"
]
series = ["Unreal"]
aliases = ["migrate-from-jekyl"]
image = "Image.jpg"
+++

This article offers an insight into the framework of UE, by analyizng the steps to take when an UE game is launched.
<!--more-->


## Game Loop
```cpp
// 游戏循环的大致过程
int main()
{
    init();
    while(!g_exit_requested)
    {
        poll_input();
        update();
        render();
    }
    shutdown();
}
```

## Launch
游戏通过各个平台的launch文件启动。
```cpp
// 简化后的launch.cpp
#include "LaunchEngineLoop.h"

FEngineLoop GEngineLoop;
bool GIsRequestingExit = false;

int32 GuardedMain(const TCHAR* CmdLine)
{
    // Run early Initialization, load engine modules
    int32 ErrorLevel = GEngineLoop.PreInit(CmdLine); // PreInit加载了大多数模块
    if(ErrorLevel != 0 || GisRequestingExit)
    {
        return ErrorLevel;
    }

    // Create and initialize a UEngine, run late initialization, start the game
    ErrorLevel = GEngineLoop.Init();

    // Every frame: kick off rendering, tick the engine, update RHI
    while(!GIsRequestingExit)
    {
        GEngineLoop.Tick();
    }

    // Run cleanup and exit when requested
    GEngineLoop.Exit();
    return ErrorLevel;
}
```
## GEngineLoop.PreInit（）
引擎的源代码分为多个模块，模块系统有助于确保源代码的不同部分之间的依赖关系是可管理的，并确保只加载所需的模块。
![G1](Photos/1.JPG "Modules")
在Game Loop的`PreInit`阶段会先加载一些低级模块，为后续的初始化做准备，如定义基本类型。

### 加载顺序：
#### 1. LoadCoreModules
CoreUObject
#### 2. LoadPreInitModules
Engine
Renderer
Rendering APIs
AnimGraphRuntime
SlateRHIRenderer
Landscape
RenderCore
#### 3. Project & Plugin (Early Loading)
#### 4. LoadStartupCoreModules
   Core
   Networking
   XAudio2
   Messaging
   Slate
   UMG
   Overlay
   ClothingSystemRuntimeNv
   PacketHandler
   ...
#### 5. Project & Plugin (Default Point)
   GamePlay Codes
### 模块加载流程
1.首先，引擎注册该模块中定义的所有`UObject`类，从而被反射系统获知，并且为每个类构造一个CDO(Class Default Object)。CDO是对类的默认状态的记录，并且充当之后的继承的原型。引擎循环分配CDO，运行其构造函数，并传递父类的CDO作为模板。因此构造函数中不应该包含Gameplay代码。
2.第二，引擎调用了模块的`StartupModule`函数，其与`ShutdownModule`相对应，从而在模块的生命周期中执行所需的初始化。

综上，`GEngineLoop.PreInit()`加载了后续初始化所需的底层系统、引擎块、工程以及插件模块。

## GEngineLoop.Init()

### 创建UGameEngine实例
```cpp
GEngine = NewObject<UEngine>(GetTransientPackage(), EngineClass);
```
`UEngine`派生出`UEditorEngine`与`UGameEngine`。
 引擎首先检查配置文件来决定使用哪个类，然后创建该类的一个实例`GEngine`，并将其作为全局的UGameEngine实例。
`GEngine`在Engine/Engine.h声明。
### 初始化GEngine
```cpp
// Initialize the engine: this creates UGameInstance and UGameViewPortClient
GEngine->ParseCommandline();
GEngine->Init(this);
FCoreDelegates::OnPostEngineInit.Broadcas();
```
初始化后引擎会触发一个全局委托`OnPostEngineInit`来指示引擎现已初始化。
### 初始化所有后加载的模块
```cpp
// Initialize any late-loaded modules
IProjectManager::Get().LoadModulesForProject(EloadingPhase::PostEngineInit);
IPluginManager::Get().LoadMoudlesForEnabledPlugings(EloadingPhase::PostEngineInit);
```

至此，引擎启动，前期初始化完成。

### 进入Map
UEngine类能够使玩家在引擎启动后进入地图中。
引擎能够浏览到一个URL， 其表示要作为客户端连接的服务器地址（本地为127.0.0.1），也可以表示要在本地加载的地图的名称。

## 引擎启动后的初始化
```cpp
// In GameEngine.cpp； UGameEngine::Init()

// Load and apply user game settings
GetGameUserSettings()->LoadSettings();
GetGameUserSettings()->ApplyNonResolutionSettings();

// Create an UGameInstance using the class specified by project config
FSoftClassPath GameInstanceClassName = GetDefault<UGameMapsSettings>()->GameInstanceClass;
UClass* GameInstanceClass = LoadObject<UClass>(nullptr, *GameInstanceClassName.ToString());
GameInstance = NewObject<UGameInstance>(this, GameInstanceClass);

// Create FWorldContext and dummy UWorld; initialize subsystems
GameInstance->InitializeStandalone();

// Create an UGameViewportClient and initialize it
UGameViewportCLient* ViewportClient = NewObject<UGameViewportClient>(this, GameViewportClientClass);
ViewportClient->Init(*GameInstance->GetWorldContext(), GameInstance);
GameViewport = ViewportClient;
GameInstance->GetWorldContext()->GameViewport = ViewportClient;
CreateGameViewport(ViewportClient);

// Create a ULocalPlayer and associate it with the view client
FString Error;
ViewportClient->SetupInitalLocalPlayer(Error);
UGameViewportClient::OnviewportCreated().Broadcast();

```
加载地图前,引擎通过创建`UGameInstance`, `UGameViewportClient`和`ULocalPlayer`来进行初始化
`ULocalPlayer`可以被视为代表屏幕前的用户。
`UGameViewportClient`本质上是渲染、音频和输入系统的高级接口，即用户和引擎之间的接口。
`UGameInstance`处理一些特定于项目的功能，GameEngine中只能创建唯一一个。