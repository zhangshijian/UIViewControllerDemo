# UIViewController详解


## 概述

视图控制器管理着构成应用程序用户界面中的一部分视图，其负责加载和处理这些视图，管理与这些视图的交互，并协调视图对其展示的数据内容的变更作出响应。视图控制器还能与其他视图控制器对象协调工作，帮助管理应用程序的整体界面。

视图控制器的主要职责包括以下内容：
- 更新视图的内容来响应底层数据的变化。
- 响应用户与视图的交互。
- 调整视图大小并管理整个界面的布局。

## 视图控制器的作用

视图控制器是应用程序内部结构的基础。每个应用程序至少含有一个视图控制器，大多数应用程序都含有多个视图视图控制器。每个视图控制器管理着应用程序的用户界面，以及该界面和底层数据之间的交互。视图控制器也便于在不同用户界面之间的转换。

`UIViewController`类定义了一些方法和属性来管理视图、处理事件、从一个视图控制器切换到另一个视图控制器和协调应用程序的其他部分，可以通过继承`UIViewController`（或其子类之一）来添加实现应用程序行为所需的自定义代码。

有两种类型的视图控制器：
- 内容视图控制器，其管理着应用程序内容的一部分。
- 容器视图控制器，其收集来自于其他视图控制器的信息并以便于导航的方式呈现或者以不同方式呈现这些视图控制器的内容。

### 视图管理

视图控制器最重要的作用是管理视图的层次结构。每个视图控制器都含有一个包含所有视图控制器内容的根视图，可以添加需要显示内容的视图到该根视图中。下图显示了视图控制器和视图之间的内置关系，视图控制器总是具有对其根视图的强引用，并且每个视图都具有对其子视图的强引用。

![图2-1](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG_ControllerHierarchy_fig_1-1_2x.png)

内容视图控制器自己管理其包含的所有视图，容器视图控制器管理其所包含的所有视图以及来自其一个或多个子视图控制器的**根视图**。容器视图控制器并不管理其子视图控制器的内容，只管理其子视图控制器的根视图。下图说明了`UISplitViewController`与其子视图控制器之间的关系。`UISplitViewController`管理其子视图的整体大小和位置，但子视图控制器管理这些视图的实际内容。

![图2-2](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG_ContainerViewController_fig_1-2_2x.png)

### 数据封送

视图控制器充当其管理的视图与应用程序数据之间的媒介。子类化`UIViewController`的时候，可以添加任何需要在子类中管理的数据变量。添加自定义变量会创建一个如下图所示的关系，其中视图控制器具有对数据的引用以及用于呈现该数据的视图。

![图2-3](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG_CustomSubclasses_fig_1-3_2x.png)

应该始终在视图控制器和数据对象中保持清晰的职责分离。大多数确保数据结构完整性的逻辑应属于数据对象本身。视图控制器可以验证来自视图的输入，然后以数据对象需要的格式打包输入，但是应该最小化视图控制器在管理实际数据中的角色。

`UIDocument`对象是一种独立于视图控制器管理数据的方式。文档对象是一个知道如何读写数据到持久存储的控制器对象。子类化`UIDocument`时，可以添加任何需要的逻辑和方法来提取数据，并将其传递给视图控制器或者应用程序的某部分。视图控制器可以存储其接收到的任何数据的副本，以便更新视图，但文档仍然拥有真实的数据。

### 用户交互

视图控制器是响应者对象，能够处理响应者链中传递的事件，但视图控制器很少直接处理触摸事件。相反，视图通常会处理自己的触摸事件，并将结果报告给其关联的委托或目标对象（通常是视图控制器）的方法。因此，视图控制器中的大多数事件都是使用委托方法或操作方法处理的。

### 资源管理

视图控制器对其视图和它创建的任何对象承担全部责任。`UIViewController`类自动处理视图管理的大多数方面，例如，UIKit自动释放不再需要的任何视图相关的资源。子类化`UIViewController`时，需要自己负责管理创建的任何对象。

当可用空闲内存不足时，UIKit会要求应用程序释放不再需要的资源。其中一种方式是通过调用视图控制器的`didReceiveMemoryWarning`方法来删除对不再需要的对象的引用或者稍后重新创建。例如，在该方法中删除缓存的数据。发生内存不足的情况时，释放尽可能多的内存非常重要。消耗太多内存的应用程序可能会被系统彻底终止以恢复内存。

### 适应性

视图控制器负责呈现其视图，并使该呈现适应底层环境。每个iOS应用程序都应该能够在iPad上运行，并且可以在几种不同大小的iPhone上运行。不是为每个设备提供不同的视图控制器和视图层，而是使用单个视图控制器来更简单地调整其视图以适应不断变化的空间需求。

在iOS中，视图控制器需要处理粗粒度的变化和细粒度的变化。当视图控制器的特性改变时，会发生粗粒度的变化。特征是描述整体环境的属性，例如显示比例。其中两个最重要的特性是视图控制器的水平和垂直尺寸类别，是它们表示视图控制器在给定维度中有多少空间。可以使用size class changes来改变布局视图的方式，如下图所示。当horizontal size class是规则的，视图控制器利用额外的水平空间来排列其内容。当horizontal size class是紧凑的，视图控制器垂直排列其内容。

![图2-4](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG_SizeClassChanges_fig_1-4_2x.png)

根据给定的size class，可以随时进行更细粒度的尺寸更改。当用户将iPhone从纵向旋转到横向时，size class可能不会改变，但屏幕尺寸通常会改变。在使用自动布局时，UIKt会自动调整视图的大小和位置以匹配新维度。视图控制器可以根据需要进行其他调整。

## 视图控制器层次结构

应用程序的视图控制器之间的关系定义了每个视图控制器所需的行为，维护正确的视图控制器关系可以确保自动行为在需要时传递给正确的视图控制器。如果违反了UIKit规定的规则和呈现关系，则应用程序的表现可能和预期不一致。

### 根视图控制器

根视图控制器是视图控制器层次结构的锚点。每个窗口只有一个根视图控制器，此根视图控制器的内容填充该窗口。根视图控制器定义了用户看到的初始内容。下图显示了根视图控制器和窗口之间的关系，因为窗口本身没有可见的内容，所以视图控制器的视图提供了所有的内容。

![图3-1](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG-root-view-controller_2-1_2x.png)

根视图控制器可以从`UIWindow`对象的`rootViewController`属性访问。当使用storyboard来配置视图控制器时，UIKit会在启动时自动设置该属性的值。对于以编程方式创建的窗口，必须自己设置根视图控制器。

### 容器视图控制器

容器视图控制器允许使用更易于管理和可重用的界面来组装复杂的界面。容器视图控制器将一个或多个子视图控制器的内容与可选的自定义视图混合在一起，来创建其最终界面。例如，`UINavigationController`对象显示来自其子视图控制器的内容以及由其管理的导航栏和可选工具栏。UIKit包含多个容器视图控制器，包括`UINavigationController`、`UISplitViewController` 和`UIPageViewController`。

容器视图控制器的视图总是会填充给定的空间，其通常被指定为窗口的根视图控制器。容器视图控制器也可以以模态的方式呈现，或者作为其他容器的子项安装。下图显示了在容器并排放置两个子视图。

![图3-2](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG-container-acting-as-root-view-controller_2-2_2x.png)

由于容器视图控制器管理其子项，UIKit定义了如何在自定义容器中设置这些子项的规则。

### 呈现视图控制器

呈现一个视图控制器时，通常会隐藏当前视图控制器的内容来将当前视图控制器的内容替换为新视图控制器的内容。**呈现最常用于模态地显示新内容。**在呈现一个视图控制器时，UIKit会在呈现视图控制器和其呈现的视图控制器之间创建如下图所示的关系。

![图3-3](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG-presented-view-controllers_2-3_2x.png)

当呈现涉及到容器视图控制器时，UIKit可能会修改呈现链来简化必须编写的代码。不同的呈现风格对应的视图在屏幕上的显示方式有不同的规则，例如全屏呈现总是覆盖整个屏幕。在呈现一个视图控制器时，UIKit会查找为呈现提供合适上下文的视图控制器。在许多情况下，UIKit会选择最近的容器视图控制器，但也可能选择窗口的根视图控制器。在某些情况下，也可以直接告诉UIKit哪个视图控制器定义了呈现上下文，并且应该处理呈现。

下图显示了容器视图控制器为呈现提供上下文的原因。在执行全屏呈现时，新视图控制器需要覆盖整个屏幕。容器视图控制器决定是否处理呈现，而不需要其子视图控制器知道容器视图的边界。

![图3-4](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG-container-and-presented-view-controller_2-4_2x.png)

## 设计技巧

视图控制器是在iOS上运行的应用程序的基本工具，并且UIKit的视图控制器基础结构可以很容易地创建复杂的界面，而无需编写大量的代码。在实现我们自己的视图控制器时，请使用一下提示和指导原则，以确保我们不会干涉可能会干扰系统预期的自然行为。

### 尽可能使用系统提供的视图控制器

许多iOS框架定义了视图控制器，使用这些系统提供的视图控制器可一节省时间并确保为用户提供一致的体验。大多数系统控制器都是为特定任务而设计的某些控制器提供对用户数据（如联系人）的访问，也可能提供访问硬件或提供专门调整的界面来管理媒体。例如，UIKit中的`UIImagePickerController`类显示用于获取图片和视频以及访问用户相机胶卷的标准界面。

在创建自己的自定义视图控制器前，请查看现有的框架是否存在能够执行需要的任务的视图控制器：
- UIKit框架提供视图控制器用于显示弹窗警告，拍照和录像，以及管理iCloud上的文件。UIKit还定义许多可用于组织内容的标准容器视图控制器。
- GameKit框架提供了视图控制器可用于匹配玩家以及管理排行榜、成就和其他游戏功能。
- Address Book UI框架提供了用于显示和选择联系人信息的视图控制器。
- MediaPlayer框架提供了用于播放和管理视频以及从用户库中选择媒体资源的视图控制器。
- EventKit UI框架提供了用于显示和编辑用户日历数据的视图控制器。
- GLKit框架提供了用于管理OpenGL渲染界面的视图控制器。
- Multipeer Connectivity框架提供了用于检测其他用户并邀请他们进行连接的视图控制器。
- Message UI框架提供了用于撰写电子邮件和SMS消息的视图控制器。
- PassKit框架提供了用于显示通行证并将其添加到Passbook的视图控制器。
- Social框架为Twitter、Facebook和其他社交媒体网站提供了编辑消息的视图控制器。
- AVFoundation框架提供了用于显示媒体资源的视图控制器。

> **重要**：切勿修改系统提供的视图控制器的视图层次结构。每个视图控制器都拥有其视图层次结构，并负责维护该层次结构的完整性。进行更改可能会在代码中引入错误，或阻止其所属视图控制器的正常运行。使用系统视图控制器时，总是依靠视图控制器公开的可用方法和属性进行所有修改的。

有关使用特定视图控制器的信息，可查看相应框架的参考文档。

### 保证每个视图控制器独立运行

视图控制器应始终是自给自足的对象，没有视图控制器应该有关于另一个视图控制器的内部工作或视图层次结构的知识。在两个视图控制器需要来回通信或传递数据的情况下，它们应该始终使用明确定义的公共接口来实现。

代理设计模式经常用于管理视图控制器之间的通信。通过委托，一个对象定义了一个相关的委托对象进行通信的协议，该委托对象是任何符合该协议的对象。委托对象的确切类型是不重要的，重要的是它实现了协议的方法。

### 仅将根视图用作其他视图的容器

仅将视图控制器的根视图用作其余内容的容器。使用根视图作为容器可以为所有视图提供一个共同的父视图，这使得许多布局操作变得更简单。许多自动布局约束需要共同的父视图来正确布置视图。

### 知道数据该存在于哪里

在模型 - 视图 - 控制器设计模式中，视图控制器的作用是促进模型对象和视图对象之间的数据移动。视图控制器可能会将一些数据存储在临时变量中并执行一些验证，但其主要职责是确保其视图包含准确的信息。而模型数据对象负责管理实际数据并确保数据的完整性。

`UIDocument`和`UIViewController`类之间的关系就是一个数据和接口分离的例子。具体而言，两者之间不存在默认关系。`UIDocument`对象负责协调数据的加载和保存，而`UIViewController`对象协调屏幕上的视图显示。如果需要在两个对象之间创建关系，请记住视图控制器应该只缓存文档中的信息以提高效率，实际的数据仍然属于文档对象。

### 适应变化

应用程序可以在各种iOS设备上运行，并且视图控制器被设计为适应这些设备上不同大小的屏幕。并不是使用单独的视图控制器来管理不同屏幕上的内容，而是使用内置的适配性支持响应视图控制器中的大小和大小等级的更改。UIKit发送的通知使我们有机会对用户界面进行大规模和小规模的更改，而无需更改视图控制器代码的其余部分。

## 子类化视图控制器

### 设计UI

可以使用Xcode中的storyboard文件直观地定义视图控制器的UI，也可以以编程方式来创建UI。但storyboard可以将视图的内容可视化，并根据需要自定义视图层次结构。以可视化方式构建UI界面，可以让我们快速进行更改并能看到结果，而无需构建和运行应用程序。

下图显示了一个storyboard的例子。每个矩形区域代表一个视图控制器及其相关视图，视图控制器之间的箭头是视图控制器的关系和节点。关系将容器视图控制器连接到其子视图控制器。Segues可用于在界面中的视图控制器之间导航。

![图4-1](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/storyboard_bird_sightings_2x.png)

### 处理用户交互

应用程序的响应者对象会处理传入的事件，虽然视图控制器是响应者对象，但它们很少直接处理触摸事件。相反，视图控制器通常以下列方式处理事件：
- 视图控制器定义了处理更高级别事件的操作方法。行动方法回应：
    - 具体行动。控件和一些视图调用行动方法来报告特定的交互。
    - 手势识别器。手势识别器调用行动方法来报告手势的当前状态。使用视图控制器处理状态变更或响应已完成的手势。
- 视图控制器观察由系统或其他对象发出的通知，通知报告更改。通知也是视图控制器更新其状态的一种方式。
- 视图控制器充当另一个对象的数据源或委托。例如，可以将它们用作`CLLocationManager`对象的代理，该对象将更新的位置发送给其委托对象。

### 在运行时显示视图

storyboard加载和显示视图控制器视图的过程非常简单。当需要时，UIKit会自动从stroyboard文件中加载视图。作为加载过程的一部分，UIKit执行以下任务序列：
- 使用storyboard文件中的信息实例化视图。
- 连接所有的outlets和actions。
- 将根视图分配给视图控制器的视图属性。
- 调用视图控制器的`awakeFromNib`方法。调用此方法时，视图控制器的特征集合为空，视图可能不在其最终位置。
- 调用视图控制器的`viewDidLoad`方法。在此方法中添加或者删除视图，修改布局约束，并未视图加载数据。

在屏幕上显示视图控制器的视图之前，UIKit提供了额外的机会在视图显示前后来执行需要的操作。具体来说，UIKit执行以下任务序列：
- 在视图即将出现在屏幕上前，调用视图控制器的`viewWillAppear:`方法。
- 更新视图的布局。
- 在屏幕上显示视图。
- 视图显示后，调用视图控制器的`viewDidAppear:`方法。

添加、删除或修改视图的大小或者位置时，也要添加和删除适用于这些视图的任何约束。对视图层次结构进行与布局相关的更改会导致UIKit将布局标记为脏。在下一个更新周期中，布局引擎使用当前布局约束条件计算视图的大小和位置，并将这些更改应用于视图层次结构。

### 管理视图布局

当视图的大小和位置发生变化时，UIKit将更新视图层次结构的布局信息。对于使用Auto Layout配置的视图，UIKit将使用Auto Layout引擎根据当前约束来更新布局。UIKit还会通知其他感兴趣的对象（如正在呈现的控制器）布局发生更改，以便它们可以做出相应的响应。

在布局过程中，UIKit会在几个时间点发出通知，以便我们可以执行其他与布局相关的任务。使用这些用纸来修改布局约束，或者在应用布局约束后对布局进行最终的调整。在布局过程中，UIKit为每个受影响的视图控制器执行以下操作：
- 根据需要更新视图控制器及其视图的特征集合。
- 调用视图控制器的`viewWillLayoutSubviews`方法。
- 调用当前`UIPresentationController`的`containerViewWillLayoutSubviews`方法。
- 调用视图控制器的根视图的`layoutSubviews`方法。此方法默认使用可用的约束来计算新的布局信息。然后该方法遍历视图层，并调用每个子视图的`layoutSubviews`方法。
- 将计算的布局信息应用于视图。
- 调用视图控制器的`viewDidLayoutSubviews`方法。
- 调用当前`UIPresentationController`对象的`containerViewDidLayoutSubviews`方法。

视图控制器可以使用`viewWillLayoutSubviews`和`viewDidLayoutSubviews`方法来执行可能影响布局过程的附加更新。在布局之前，可以添加或删除视图，更新视图的大小或位置，更新约束或更新其他视图相关的属性。布局之后，可以重新加载表格数据，更新其他视图的内容，或对视图的大小和位置进行最终调整。


