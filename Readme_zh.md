
# GASDocumentation
这是我自己对于虚幻引擎4 GameplayAbilitySystem(GAS) 插件的一些见解,以及一个多人示例项目.这不是官方文档,示例项目与我都不隶属于Epic Games.我不确保此文档及示例代码的准确性.

此文档主要介绍了GAS插件的基本概念与类,并根据我自己的经验提供一些补充说明.已经有很多人在社区中分享过GAS的知识了,这里是我的分享^_^

示例项目与此文档基于虚幻引擎4.24版本.

GAS插件的源代码才是最好的文档.

<a name ="table-of-contents"></a>
## 目录

> 1. [GameplayAbilitySystem插件简介](#intro)
> 1. [Sample Project](#sp)
> 1. [Concepts](#concepts)  
>     3.1 [Ability System Component](#concepts-asc)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.1.1 [Replication Mode](#concepts-asc)  
>     3.2 [Gameplay Tags](#concepts-gt)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.2.1 [Responding to Changes in Gameplay Tags](#concepts-gt-change)  
>     3.3 [Attributes](#concepts-a)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.3.1 [Attribute Definition](#concepts-a-definition)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.3.3 [Meta Attributes](#concepts-a-meta)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.3.4 [ Responding to Attribute Changes](#concepts-a-changes)  
>     3.4 [Attributes Set](#concepts-as)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.4.1 [ Attribute Set Definition](#concepts-as-definition)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.4.2 [ Attribute Set Design](#concepts-as-design)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.4.3 [ Defining Attributes](#concepts-as-attributes)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.4.4 [ Initializing Attributes](#concepts-as-init)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>     3.5 [Gameplay Effects](#concepts-ge) 
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.1 [Gameplay Effect Definition](#concepts-ge-definition)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.2 [Applying Gameplay Effects](#concepts-ge-applying)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.3 [Removing Gameplay Effects](#concepts-ge-removing)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.4 [Gameplay Effect Modifiers](#concepts-ge-mods)  
>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.5 [Stacking Gameplay Effects](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.6 [Granted Abilities](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.8 [Immunity](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [Get the Cooldown Gameplay Effect's Remaining Time](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [Listening for Cooldown Begin and End](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [Predicting Cooldowns](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.16 [Changing Active Gameplay Effect Duration](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.17 [Creating Dynamic Gameplay Effects at Runtime](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>     3.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.1 [Gameplay Abliity Definition](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [Replication Policy](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [Replicate Input Directly](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.2 [Binding Input to the ASC](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.3 [Granting Abilities](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.4 [Activating Abilities](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [Passive Abilitites](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.5 [Canceling Abilities](#concepts-ga-canceling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.6 [Getting Active Abilities](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.7 [Instancing Policy](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.8 [Net Execution Policy](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.11 [Passing Data to Abilities](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.12 [Ability Cost and Cooldown](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.13 [Leveling Up Abilities](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.6.14 [Ability Sets](#concepts-ga-sets)   
>    3.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.7.1 [Ability Task Definition](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.7.2 [Custom Ability Tasks](#concepts-at-custom)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.7.3 [Using Ability Tasks](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    3.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.8.1 [Gameplay Cue Definition](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.8.2 [Trigger Gameplay Cues](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.8.3 [Local Gameplay Cues](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.8.6 [Prevent Gameplay Cues from Firing](#concepts-gc-prevention)  
>    3.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    3.10 [Prediction](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.10.1 [Prediction Key](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.10.2 [Creating New Prediction Windows in Abilities](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.10.3 [Predictively Spawning Actors](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.10.4 [Future of Prediction in GAS](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.10.5 [Network Prediction Plugin](#concepts-p-npp)  
> 1. [Commonly Implemented Abilities and Effects](#cae)  
>    4.1 [Stun](#cae-stun)  
>    4.2 [Sprint](#cae-sprint)  
>    4.3 [Aim Down Sights](#cae-ads)  
>    4.4 [Lifesteal](#cae-ls)  
>    4.5 [Generating a Random Number on Client and Server](#cae-random)  
>    4.6 [Critical Hits](#cae-crit)  
>    4.7 [Non-Stacking Gameplay Effects but Only the Highest Magnitude is Actually Affects the Target](#cae-nonstackingge)  
>    4.8 [Generate Target Data While Game is Paused](#cae-paused)  
> 1. [Setting Up a Project Using GAS](#setup)
> 1. [Debugging](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS Logging](#debugging-log)  
> 1. [Common GAS Acronymns](#acronyms)
> 1. [Other Resources](#resources)
> 1. [GAS Changelog](#changelog)  
>    [4.24](#changelog-4.24)
   
 


<a name="intro"></a>
## 1.GameplayAbilitySystem插件简介

已用自[官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html):
>GameplayAbilitySystem是一个高度灵活的用于构建您可能在RPG或者MOBA游戏里经常见到的技能与属性系统的框架.你可以为游戏中的角色创建主动技能或者被动技能;这些技能可以对角色产生某些效果,可以增强或者削弱角色属性;可以为这些技能设计冷却时间与资源消耗用来规范这些技能的使用;改变技能的等级以及不同等级下技能的威力,播放技能的粒子特效与音效,等等...简而言之,该系统可以帮助您高效的设计与实现各种角色技能.

GameplayAbilitySystem插件是由Epic Games开发,其功能已经在诸如Fortnite和Paragon之类的AAA商业游戏中经历过检验.

该插件为单人或多人游戏厅开箱即用的解决方案:
* 基于等级的角色技能,带有可选的冷却时间与技能资源消耗([GameplayAbilities](#concepts-ga))
* 处理角色拥有的数值`属性`([Attributes](#concepts-a))
* 对角色施加`效果`([GameplayEffects](#concepts-ge))
* 对角色应用`标签`([GameplayTags](#concepts-gt))
* 产生视觉与听觉特效([GameplayCue](#concepts-gc))
* 网络复制上述所有内容

在多人游戏中,GAS为客户端预测提供支持:
* 技能激活
* 播放动画蒙太奇
* `属性Attributes`的改变
* 应用`GameplayTags`
* 产生`GameplayCue`
* 通过`RootMotionSource`函数连接到`CharacterMovementComponent`进行运动。**Note:** Predicting movement with `RootMotionSource` broke in UE 4.20; however, [fixes for the RootMotionSource prediction issues](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c) are in UnrealEngine GitHub master possibly slated for 4.25.

**GAS必须使用C++中设置**,但是`GameplayAbilities`与`GameplayEffects`可以由设计师使用蓝图创建

GAS目前存在的问题:
* 在UE4.20版本中预测 `RootMotionSource` `AbilityTasks`不正常.这可能在新的网络预测插件集成到GAS之前都不会修复.
* GameEffect 延迟调和(无法预测技能冷却时间会导致高延迟玩家相比低延迟玩家在技能冷却时间比较小时射速比较低)
* 无法预测`GameEffect`的删除,我们可以添加一个与要移除`GameplayEffect`效果相反的`GameplayEffect`,来等效的删除它.但这并不是总是可行的办法.
* 缺少示例程序,缺少文档.希望此文档会有所帮助.

**[⬆ Back to Top](#table-of-contents)**

<a name="sp"></a>
## 2.Sample Project

本文档配套有一个第三人称多人射击示例项目,用来帮助那些熟悉UE4但是不熟悉`Gameplay Ability System`的人们.读者需要熟悉C++,蓝图,UMG,复制和其他UE4的中级主题.该项目展示了在第三人称多人射击游戏中,如何为由玩家或AI控制的英雄角色在PlayerState类上设置AbilitySystemComponent(ASC),以及如何为由AI控制的小兵在Character类上设置ASC.

为了使得项目保持简单,同时展示GAS的基础知识,并通过良好注释的代码展示一些常用的功能.由于是针对GAS初学者,此项目未展示**[预测弹道](#concepts-p-spawn)**之类的高级主题.

演示的概念:
* `ASC`在`PlayerState`类中 VS 在 `Character`类中
* 复制`属性`
* 复制动画蒙太奇
* 游戏标签`GameplayTags`
* 在`GameplayAbilities`内部或外部启用或移除`GameplayEffects`
* 受到伤害时使用护甲(armor)属性来计算减伤
* GameplayEffectExecutionCalculations
* 眩晕效果
* 死亡与重生
* 通过`技能`在服务器上生成Actor(子弹)
* 通过`瞄准`与`冲刺`来预测性的改变本地角色的速度.
* 冲刺会不断的消耗机灵
* 使用魔法来释放技能
* 被动技能
* 叠加`GameplayEffects`
* 目标选择
* 蓝图中创建`GameplayAbilities`
* C++中创建`GameplayAbilities`
* `GameplayAbilities`实例化 - 每个Actor实力化一次
* 非实例化`GameplayAbilities`(Jump跳跃)
* 静态`GameplayCues`(子弹撞击粒子效果)
* Actor `GameplayCue`(冲刺与眩晕粒子效果)

英雄角色有以下技能:

| 技能 | 输入绑定| 预测| C++/蓝图| 描述
| --- | --- | --- | --- | --- |
| 跳跃(Jump) | 空格键 | Yes | C++ | 跳跃|
| 枪击(Gun) | 鼠标左键 | No | C++ | 从英雄的枪口发射弹丸.动画是预测的,但是弹丸不是预测的|
| 瞄准(Aim Down Sights) | 鼠标右键 | Yes | 蓝图 |按下鼠标右键时,角色移动速度减慢,相机镜头放大.|
| 加速跑(Sprint) | 左侧Shift键 | Yes | 蓝图 | 按下左侧Shift键时,角色加速跑动,并消耗耐力值 |
| 向前冲刺(Forward Dash) | Q | No* | 蓝图 | 角色向前冲刺一段距离.有**BUG** |
| 被动护甲叠加 | 被动 | No | 蓝图 | 每4秒恢复一层护甲,最大四层护甲,受到伤害移除一层护甲 |
| 陨星技能 | R | No | 蓝图 | 玩家选定一个区域召唤陨石,击中敌人后造成眩晕与伤害.|

`GameplayAbilities`可以使用C++或者蓝图创建,此项目中两种方式都有展示.

小兵没有任何预定义的`GameplayAbilities`.红色小兵秒回能力强,蓝色小兵起始血量高.

使用`_BP`后缀命名的`GameplayAbilities`表明其功能逻辑在蓝图中实现,否则其功能逻辑在C++中实现.

**蓝图资源命名前缀**

| 前缀 | 资源类型 |
| --- | --- |
| GA_ | GameplayAbility |
| GC_ | GameplayCue |
| GE_ | GameplayEffect |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts"></a>
## 3.GAS Concepts

#### Sections

> 3.1 [Ability System Component](#concepts-asc)  
> 3.2 [Gameplay Tags](#concepts-gt)  
> 3.3 [Attributes](#concepts-a)  
> 3.4 [Attributes Set](#concepts-as)  
> 3.5 [Gameplay Effects](#concepts-ge)  
> 3.6 [Gameplay Abilities](#concepts-ga)  
> 3.7 [Ability Tasks](#concepts-at)  
> 3.8 [Gameplay Cues](#concepts-gc)  
> 3.9 [Ability System Globals](#concepts-asg)  
> 3.10 [Prediction](#concepts-p)  


<a name="concepts-asc"></a>
### 3.1 Ability System Component
`AbilitySystemComponent`(`ASC`)是整个技能系统(`GAS`)的核心.它继承自`USceneComponent`(`UAbilitySystemComponent`),并处理所有与GAS系统的交互.任何想要使用[技能Gameplay Abilities](#concepts-ga),拥有[属性Attributes](#concepts-a)或者接收[Gameplay Effects](#concepts-ge)的`Actor`都必须拥有一个`ASC`.以上提及的各种对象都存在于`ASC`内部,并由`ASC`进行管理和复制(通过[Attributes Set](#concepts-as)复制的`Attributes`除外).开发人员可以子类化`UAbilitySystemComponent`,但这并不是强制的.

拥有`ASC`的`Actor`被称为`ASC`的`OwnerActor`;`ASC`的物理表示`Actor`被称为`AvatarActor`.`OwnerActor`与`AvatarActor`可以是同一个`Actor`也可以是不同`Actor`.例如MOBA游戏中简单的AI小兵可以同时是`OwnerActor`和`AvatarActor`;再例如可以使用英雄角色的`PlayerState`类作为`OwnerActor`,使用其`Character`类作为`AvatarActor`.如果你的`Actor`需要重生,并且需要在重生之间保持`Attributes`与`GameplayEffects`的持续性,那么`ASC`的理想位置是放在`PlayerState`类里.

**注意:** 如果你的ASC是处在`PlayerState`类里的,你需要增加`PlayerState`类的`NetUpdateFrequency`值.默认情况下此值是比较低的,这可能导致客户端的更新延迟.

`OwnerActor`与`AvatarActor`都需要实现`IAbilitySystemInterfac`e接口.并且必须重写`UAbilitySystemConponent* GetAbilitySystemComponent()const`函数.`ASCs`之间就是通过此接口函数来互相查找与交互的.

`ASCs`一般都是在`OwnerActor`的构造函数中创建并被标记为可复制的.**这个步骤必须在C++代码中实现.**

`ASC`在`FActiveGameplayEffectsContainer ActiveGameplayEffects;` 中保存当前激活的`GameplayEffects`.

<a name="concepts-asc-rm"></a>
### 3.1.1 Replication Mode
`ASC`为复制`GameplayEffects`,`GameplayTags`和`GameplayCues`定义了三种不同的复制模式:**FULL**,**Mixed**,**Minimal**.`属性Attribute`通过`AttributeSet`复制.   

| 复制模式 | 适用情形 | 描述 |  
| ---- | ---- | ---------------------------- |
| `Full` | 单人游戏 | 每个`GameplayEffect`都会复制到每个客户端 |  
| `Mixed` | 多人,玩家控制Actor | `GameplayEffect`只被复制到拥有它的客户端,`GameplayTags` 和 `GameplayCues`被复制给所有人 |  
| `Minimal` | 多人,AI控制Actor | `GameplayEffects`不会被复制,`GameplayTags` 和 `GameplayCues`被复制给所有人 |  

**注意:** `Mixed`模式下要求`OwnerActor`的拥有者是`Controller`类.默认情况下`PlayerState`类的拥有者是`Controller`类,而`Character`类的拥有者不是`Controller`类,此时需要主动调用`SetOwner()`函数,来设置`Controller`类为`OwnerActor`的拥有者.

从4.24版本开始,`PossessedBy()`函数设置`Pawn`类的拥有者为新的`Controller类`

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt"></a>
### 3.2 Gameplay Tags
[FGameplayTags](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html)是注册在`GameplayTagManager`中的具有类似`parent.child.grandchild...`层级结构的标签名称.这些标签对于分类和描述对象状态非常有用,例如在角色被眩晕期间我们可以为角色添加一个`State.Debuff.Stun`标签.

你会发现以前使用布尔或者枚举值处理的内容现在可以使用`GameplayTags`标签来处理了,布尔逻辑判断变成了判断对象上是否拥有某个`GameplayTag`标签.当我们为某个对象添加标签时,一般都添加到此对象的`ASC`组件中,以便`GAS`系统可以与这些标签进行交互.`UAbilitySystemComponent`实现了`IGameplayTagAssetInterface`接口,此接口提供了访问`GameplayTags`标签的函数.

多个`GameplayTags`可以存储在一个`FGameplayTagContainer`中.与`TArray<FGameplayTag>`相比最好使用`FGameplayTagContainer`,因为它提供了额外的效率.尽管标签是标准的`FName`,但是如果在项目设置中启用了`快速复制Fast Replication`,则可以将它们有效地打包在`FGameplayTagContainer`中进行复制.快速复制要求服务端与客户端拥有相同的`GameplayTags`列表.一般情况下这都不是问题,所以你应该启用此选项.`FGameplayTagContainer`也可以返回一个`TArray<FgameplayTag>`用来进行遍历.在`FGameplayTagContainer`中存储的标签拥有一个`TagMap`来保存每个标签的实例个数.一个`FGameplayTagContainer`可能包含有一个`TagMapCount`为零的`GameplayTag`,这种情况下如果`ASC`依旧使用此标签则可能出现问题.`HasTag()`或`HasMatchingTag()`或类似的函数会检查`TagMapCount`是否为零,如果为0它们会返回fasle.

GameplayTags必须事先在DefaultGameplayTags.ini中定义.UE4编辑器在项目设置中提供了一个标签编辑界面,使得开发人员无需手动编辑DefaultGameplayTag.ini文件即可管理GameplayTag.标签编辑界面可以创建,重命名,搜索标签引用,删除标签.

![GameplayTag Editor in Project Settings](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

查找`GameplayTag`引用会弹出熟悉的引用查看器(`Reference viewer`),其中显示了所有引用此标签的资源.但这个不会显示任何在C++中对标签的引用.

重命名标签会创建重定向(r`edirect`),以便引用原始标签的资源可以被重定向到新的标签.我更偏向于创建一个全新的标签,把所有对旧标签的引用都更新为对新标签的引用,然后删除旧标签,以此来避免创建重定向(`redirect`).

对于`快速复制`,标签编辑器有一个用来填充常用复制标签的选项,以此来进一步优化他们.

通过`GameplayEffect`添加的标签会被复制,同时ASC允许你添加`LooseGameplayTags`,它们不会被复制但必须手动管理.示例项目中使用了`State.Dead`这个`LosseGameplayTag`,这样本地客户端就可以在生命值降低为零后立即响应死亡.重生(`Respawn`)会自动把`State.Dead`标签的`TapMapCount`设置为零.在使用`LooseGameplayTags`的时候必须要手动设置`TapMapCount`.与手动设置`TapMapCount`相比最好使用`UAbilitySystemComponent::AddLooseGameplayTag()``和`UAbilitySystemComponent::RemoveLooseGameplayTag()`函数.

在C++中获取标签的引用:
```
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"));
```

高级标签管理功能例如获取父亲或孩子标签,请查看`GameplayTagManager`提供的函数.调用`UGameplayTagManager::Get()`函数来访问`GameplayTagManager`,在其内部标签是按照层级关系(父亲,孩子,等等)来存储的,相比于直接存储常量字符串,这种存储方式可以获得更高的处理与比较速度.

`GameplayTag`与`GameplayTagContainer`类型的变量可以使用可选的`UPROPERTY`说明符`Meta = (Categories = "GameplayCue")`,加上此说明符后,在蓝图中就会自动过滤标签,只显示那些父标签是`GameplayCue`的标签.当你知道`GameplayTag`或者`GameplayTagContainer`变量只应该被用做`GameplayCue`时此说明符是很有用的.

另外还有一个单独的叫做`FGameplayCueTag`的结构,它的内部封装了`FGameplayTag`.这种类型的变量在蓝图中也会自动过滤标签,只显示那些父标签是`GameplayCue`的标签.

**这段掐了,没翻译**
If you want to filter a GameplayTag parameter in a function, use the UFUNCTION specifier Meta = (GameplayTagFilter = "GameplayCue"). GameplayTagContainer parameters in functions can not be filtered. If you would like to edit your engine to allow this, look at how SGameplayTagGraphPin::ParseDefaultValueData() from Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp calls FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType); and passes FilterString to SGameplayTagWidget in SGameplayTagGraphPin::GetListContent(). The GameplayTagContainer version of these functions in Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp do not check for the meta field properties and pass along the filter.

示例项目大量的使用了`GameplayTags`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-change"></a>
#### 3.2.1 Responding to Changes in Gameplay Tags
ASC为GameplayTags的添加与移除提供了一个委托.注册时需要提供一个EGameplayTagEventType的参数,来指定委托何时被触发(添加/删除时 或者 TapMapCount发生任何变化时).
```
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")),EGameplayTagEventType::NewOrRemoved).AddUObject(this,&AGDPlayerState::StunTagChanged);
```
回调函数的参数包含一个`GameplayTag`与一个新的`TagCount`
```
virtual void StunTagChanged(const FGameplayTag CallbackTag,int32 NewCount);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a"></a>
### 3.3 Attributes

<a name="concepts-a-definition"></a>
#### 3.3.1 Attribute Definition
属性(`Attributes`)是由[FGameplayAttributeData](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html)结构体定义的浮点(float)值.他们可以代表很多概念,例如角色生命值,角色等级值,药水的费用等等...如果一个数值量与游戏性有关并且属于一个`Actor`,你应该考虑为它使用属性(`Attribute`).通常只能使用[GameplayEffects](#concepts-ge)来修改属性(`Attribute`),这样ASC可以预测([predict](#concepts-p))他们的变化

属性(`Attributes`)被定义并存在于[AttributeSet](#concepts-as)中.`AttributeSet`负责复制被标记为可复制的属性(`Attributes`).详细请查看[AttributeSet](#concepts-as)章节来了解如何定义`Attributes`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 3.3.2 BaseValue vs CurrentValue
一个属性`Attributes`由两个值组成:`BaseValue`和`CurrentValue`.`BaseValue`是属性的永久值,`CurrentValue`等于`BaseValue`加上从`GameplayEffects`获得的临时修改值.例如,你的角色可能拥有一个`BaseValue`为600u/s的移动速度属性.当没有`GameplayEffects`修改移动速度时`CurrentValue`也是600u/s.如果获得了一个50u/s的加速buff,此时`BaseValue`保持600u/s不变,`CurrentValue`变成了600u/s + 50u/s = 650u/s.当加速buff结束后`CurrentValue`恢复为`BaseValue`的600u/s.

Often beginners to GAS will confuse BaseValue with a maximum value for an Attribute and try to treat it as such. This is an incorrect approach. Maximum values for Attributes that can change or are referenced in abilities or UI should be treated as separate Attributes. For hardcoded maximum and minimum values, there is a way to define a DataTable with FAttributeMetaData that can set maximum and minimum values, but Epic's comment above the struct calls it a "work in progress". See AttributeSet.h for more information. To prevent confusion, I recommend that maximum values that can be referenced in abilities or UI be made as separate Attributes and hardcoded maximum and minimum values that are only used for clamping Attributes be defined as hardcoded floats in the AttributeSet. Clamping of Attributes is discussed in PreAttributeChange() for changes to the CurrentValue and PostGameplayEffectExecute() for changes to the BaseValue from GameplayEffects.

即刻(`Instant`)型`GameplayEffects`会改变`BaseValue`;持续(`Duration`)型与无限(`Infinite`)型的`GameplayEffects`会改变`CurrentValue`;定期(`Periodic`)触发的`GameplayEffect`被认为是即刻(`Instant`)型的`GameplayEffects`,同样会改变`BaseValue`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 3.3.3 Meta Attributes
有些属性被视为与其他属性进行交互的临时值的占位符；它们被称为元属性。例如：我们通常将伤害(`Damage`)定义为元属性.我们没用使用`GameplayEffect`去直接修改血量(`Health`)属性,而是使用一个叫做伤害(`Damage`)的元属性来作为占位符.这样就可以在`GameplayEffectExecutionCalculation`中根据`buffs`与`debuffs`来修改伤害值,并且可以在`AttributeSet`中进行进一步的修改伤害值,例如可以根据当前护甲(`shield`)属性来减少一定的伤害值.最后才从血量(`Health`)属性中减去剩下的伤害(`Damage`)属性.伤害元属性(`Damage Meta Attribute`)在`GameplayEffects`之间没有持久性,每个相关的`GameplayEffects`执行时都要覆盖它的值.

元属性(`Meta Attribute`)通常情况下是不进行网络复制的.

**[⬆ Back to Top](#table-of-contents)**  

<a name="concepts-a-changes"></a>
#### 3.3.4 Responding to Attribute Changes
如果想要监听属性值的变化以更新UI界面或者其他游戏玩法时,我们需要使用`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`函数.此函数返回一个你可以绑定的委托,当属性值发生变化时,此委托会被自动调用.此委托提供`FOnAttributeChangeData`,`NewValue`,`OldValue`和`FGameplayEffectModCallbackData`参数.其中`FGameplayEffectModCallbackData`只在服务器端有设置.

示例项目中属性变化委托被绑定在了`GDPlayerState`类上,用来更新用户`HUD`界面以及当血量(`Health`)属性变为零时响应角色的死亡与重生逻辑.
示例项目中包含一个将属性值变化委托包装到`ASyncTask`中的自定义蓝图节点.这个蓝图节点被用在了`UI_HUD` `UMG`组件中来更新血量(`Health`),蓝量(`Mana`)与耐力(`Stamina`)值.这个`ASyncTask`会一直存在,直到手动调用`EndTask()`函数.详情请参阅`AsyncTaskAttributeChanged.h/cpp`文件.  

![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ Back to Top](#table-of-contents)**  

<a name="concepts-as"></a>
### 3.4 Attributes Set

<a name="concepts-as-definition"></a>
#### 3.4.1 Attribute Set Definition
`AttributeSet`定义属性,保管属性,并管理属性的变化.开发者应该从`UAttributeSet`类继承出自己的`AttributeSet`子类.在`OwnerActor`的构造函数中创建`AttributeSet`,这样自动把它注册给对应的`ASC`.`AttributeSet`的定义与创建必须在C++中完成.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 3.4.2 Attribute Set Design
一个ASC可以拥有一个或多个AttributeSets.AttributeSets的内存开销可以忽略不计,所以要使用多少个AttributeSets是由开发人员自己决定的组织决策.

可以让所有的Actor类共享使用同一巨大的AttributeSet.不同的Actor只使用自身需要的属性而忽略其他不需要的属性.

或者你可以选择使用多个AttributeSet,每个AttributeSet代表不同的属性分组,可以按需添加到Actor中.例如你可以为血量(Health)相关的属性建立一个AttributeSet,也可以为蓝量(Mana)相关的属性建立另一个AttributeSet.在一个MOBA游戏中,英雄角色可能需要蓝量(Mana)相关的属性而小兵可能不需要.

此外AttributeSet是可以层级继承的,这也可以看做是另外一种为不同Actor添加不同属性的途径.Attributes在内部以AttributeSetClassName.AttributeName的形式引用.当你继承了一个AttributeSet,所有父类里的属性的内部引用形式依旧使用父类名称作为其前缀.

虽然`ASC`可以同时拥有多个`AttributeSet`实例,但是这些`AttributeSet`实例必须来自不同的`AttributeSet`类.如果ASC拥有的多个`AttributeSet`实例都是来自同一个`AttributeSet`类,`ASC`不知道该使用哪一个实例并随机挑选一个使用.

可以在运行时从ASC上添加或删除`AttributeSet`.例如可以把一个`AttributeSet`放在某个武器上,当装备此武器时我们为`ASC`添加对应的`AttributeSet`,当丢掉武器时我们从`ASC`上移除对应的`AttributeSet`,这样我们可以为不同的武器保存不同的特殊弹药.

装备武器时:
```
AbilitySystemComponent->SpawnedAttributes.AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
卸下武器时:
```  
AbilitySystemComponent->SpawnedAttributes.Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```  

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 3.4.3 Defining Attributes
**属性(`Attributes`)只能在`AttributeSet`类的头文件中用C++定义.** 建议将下面的的宏代码添加到每个`AttributeSet`类头文件的开头.它们可以自动为你的属性生成`getter`和`setter`函数.  
```
// Uses macros form AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName,PropertyName) \
              GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName,PropertyName) \
			  GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
			  GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
			  GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```
可网络复制的血量(Health)属性定义如下:
```
UPROPERTY(BlueprintReadOnly,Category = "Health",ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase,Health);
```
同时在头文件中定义`OnRep`函数
```
UFUNCTION()
virtual void OnRep_Health();
```
在`AttributeSet`的cpp文件中,使用预测系统使用的`GAMEPLAYATTRIBUTE_REPNOTIFY`宏来填充`OnRep`函数
```
void UGDAttributeSetBase::OnRep_Health()
{
	    GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase,Health);
}
```
最后,属性需要被添加到`GetLifetimeReplicatedProps`中:
```
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps)const
{
				Super::GetLifetimeReplicatedProps(OutLifetimeProps);
				DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```
如果属性不需要网络复制(例如元属性),那么`OnRep`与`GetLifetimeReplicatedProps`步骤可以跳过.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 3.4.4 Initializing Attributes
有多种初始化属性的方法(设置BaseValue与CurrentValue为同一个初始值).Epic建议使用一个即时(Instant)型GameplayEffect,示例程序中演示了这种方法.

有关如何制作即时(Instant)型GameplayEffect来初始化属性的信息,请参见示例项目中的GE_HeroAttributes蓝图.此GameplayEffect在C++代码中被应用.

有关初始化属性的更多方法,请参见AttributeSet.h.

**Note:** `FAttributeSetInitterDiscreteLevels` does not work with `FGameplayAttributeData`. It was created when` Attributes` were raw floats and will complain about `FGameplayAttributeData` not being `Plain Old Data` (`POD`). This is set to be fixed in 4.24 https://issues.unrealengine.com/issue/UE-76557.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 3.4.5 PreAttributeChange()
PreAttributeChange(const FGameplayAttribute& Attribute,float& NewValue)是AttributeSet的主要函数之一,用于在更改发生之前响应对CurrentValue的值进行的更改.这是将通过引用传值的NewValue钳位到合适值的理想时机.

例如在示例项目中是这样钳位移动速度(MoveSpeed)属性的:
```
if(Attribute == GetMoveSpeedAttribute())
{
	    NewValue = FMath::Clamp<float>(NewValue,150,1000);
}
```
只要属性值发生变化PreAttributeChange()都会被调用,无论是使用GameplayEffects还是使用属性的setter函数导致的变化.

**Note:** Any clamping that happens here does not permanently change the modifier on the `ASC`. It only changes the value returned from querying the modifier. This means anything that recalculates the `CurrentValue` from all of the modifiers like [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) and [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) need to implement clamping again. 

**注意:** Epic对`PreAttributeChange()`函数的注释中说:不要将其用于游戏性事件,儿主要用于数值钳位.关于属性更改的游戏性事件的推荐位置是`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`([Responding to Attribute Changes](#concepts-a-changes))

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 3.4.6 PostGameplayEffectExecute()
只有在即时(`Instant`)型[GameplayEffect](#concepts-ge)更改属性的`BaseValue`后才会触发`PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data)`.这里是属性被GameplayEffect修改后进行更多属性操作的有效位置. 
  
例如:示例项目中我们在这个函数中,从血量(`Health`)属性中减去最终的伤害(`Damage`)元属性(`Meta Attribute`)的值.如果有护甲(`Shield`)属性,我们先从护甲(`Shield`)属性中减去伤害元属性,然后再从血量(Health)属性中减去剩余的伤害元属性.示例项目还在此函数中播放击中反应动画,显示浮动伤害数字,为击杀者增加经验与金币奖励.按照设计应该始终通过即时(`Instant`)型`GameplayEffect`来改变伤害元数据(`Damage Meta Attribute`).永远不要使用它的属性`setter`函数来改变它.

Other `Attributes` that will only have their `BaseValue` changed from instant `GameplayEffects` like mana and stamina can also be clamped to their maximum value counterpart `Attributes` here.

**注意:** 当`PostGameplayEffectExecute()`被调用时,属性值已经发生改变,但是尚未通过网络复制给客户端,所以在此函数中钳位属性,并不会导致客户端更新两次数据,客户端只会收到一次钳位后的数值.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge"></a>
### 3.5 Gameplay Effects

<a name="concepts-ge-definition"></a>
#### 3.5.1 Gameplay Effect Definition
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) (`GE`)是`ASC`通过其改变自身或其他`ASC`的属性和`GameplayTags`的容器.`GameplayEffects`可以导致属性立即发生变化(例如伤害与治疗恢复),也可以添加长期的状态buff或者debuff(例如加速与击晕效果).`GameplayEffects`被设计为定义一个特定`游戏性效果`的**数据类**(**Data-Only**).不应该将其他的逻辑加入其中.通常设计人员会创建许多`UGameplayEffect`的蓝图子类.

`GameplayEffects`通过[Modifiers](#concepts-ge-mods)与[Executions (GameplayEffectExecutionCalculation)](#concepts-ge-ec)来改变属性(`Attributes`).

`GameplayEffects` 有三种类型的持续时间:瞬时型(`Instant`),持续型(`Duration`),永久型(`Infinite`).

另外`GameplayEffect`可以添加或者执行`GameplayCues`.瞬时(`Instant`)型`GameplayEffect`会在`GameplayCue GameplayTags`上调用`Execute`.持续型(`Duration`)与永久(`Infinite`)型`GameplayEffect`会在`GameplayCue GameplayTags`上调用`Add`和`Remove`.  
| Duration Type | GameplayCue Event | When to use|
| ---- | ---- | --------|
| Instant | Execute | 立即对属性的`BaseValue`造成永久性的改变,`GameplayTags`不会被应用 (哪怕一帧的时间都不会) |
| Duration | Add & Remove | 对属性的`CurrentValue`造成临时性的改变,`GameplayTags`会被应用,并会在`GameplayEffect`到期时自动移除 (或者手动移除).持续的时间在`UGameplayEffect`类或蓝图类中指定.|
| Infinite | Add & Remove | 对属性的`CurrentValue`造成临时性的改变, `GameplayTags`会被应用,当`GameplayEffect`被删除时`GameplayTags`也会被删除.它们永远不会自行失效,必须手动由`Ability`或者`ASC`删除.|

持续型与永久型`GameplayEffects`可以选择应用**周期性效果**(`Periodic Effects`).按照`Period`定义的间隔,每个X秒应用一次它的`Modifiers`与`Executions`.周期性效果被视为瞬时型`GameplayEffect`,会立刻对属性的`BaseValue`进行永久性修改,并且会在`GameplayCue`上调用`Exexute`.这在制作周期性伤害效果(`DOT`)时很有用.**注意:** 周期性`GameplayEffect`无法预测.

持续型与永久型`GameplayEffects`可以在应用后被临时关闭或打开.关闭`GameplayEffect`会删除它的`Modifiers`和`GameplayTags`,但是不删除`GameplayEffect`.打开`GameplayEffect`会重新应用它的`Modifiers`和`GameplayTags`.

GameplayEffects通常都不会进行实例化.当一个Ability或者ASC想要应用一个GameplayEffect的时候,它们会根据GameplayEffect的ClassDefaultObject创建一个GameplayEffectSpec.成功应用的GameplayEffectSpec会被添加到名为FActiveGameplayEffect的新结构体中.这是ASC在称为ActiveGameplayEffects的特殊的容器结构体中追踪的内容.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-applying"></a>
#### 3.5.2 Applying Gameplay Effects
`GameplayEffects`可以通过[`GameplayAbilities`](#concepts-ga) 上的函数和`ASC`上的函数以多种方式应用.这些函数通常具有`ApplyGameplayEffectSpecToXXX`的形式.这些变体函数本质上只是一些便利函数,它们最终都是会在目标(`Target`)上调用`UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()`函数.

如果想要在GameplayAbility之外应用GameplayEffects.你需要获取目标的ASC,然后调用它的`ApplyGameplayEffectSpecToXXX`.

通过绑定到ASC的委托,你可以监听持续型与永久型的GameplayEffects被应用到ASC的事件:
```
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this,&APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
回调函数如下:
```
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target,const FGameplayEffectSpec& SpecApplied,FActiveGameplayEffectHandle ActiveHandle);
```
The autonomous proxy will only call this for replicated `GameplayEffects` in `Full` and `Mixed` replication modes. 
无论复制模式是什么,服务器都会调用此函数.自治代理将仅针对完全复制和混合复制模式下的复制GameplayEffects调用此函数.模拟代理将仅在完全复制模式下调用此代理.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-removing"></a>
#### 3.5.3 Removing Gameplay Effects
`GameplayEffects`可以通过[`GameplayAbilities`](#concepts-ga) 上的函数和`ASC`上的函数以多种方式删除.这些函数通常具有`RemoveActiveGameplayEffect`的形式.这些变体函数本质上只是一些便利函数,它们最终都是会在目标(`Target`)上调用`FActiveGameplayEffectsContainer::RemoveActiveEffects()`函数.

如果想要在GameplayAbility之外删除GameplayEffects,你需要获取目标的ASC,然后调用它的`RemoveActiveGameplayEffect`.

通过绑定到ASC的委托,你可以监听持续型与永久型的GameplayEffects被从ASC上删除的事件:
```
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this,&APACharacterBase::OnRemoveGameplayEffectCallback);
```
回调函数如下:
```
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```
The autonomous proxy will only call this for replicated GameplayEffects in Full and Mixed replication modes.
无论复制模式是什么,服务器都会调用此函数.自治代理将仅针对完全复制和混合复制模式下的复制GameplayEffects调用此函数.模拟代理将仅在完全复制模式下调用此代理.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 3.5.4 Gameplay Effect Modifiers
Modifiers可更改属性,并且是预测性更改属性的唯一方法.一个GameplayEffect可以拥有零个或多个Modifier.一个Modifier通过指定的操作只能更改一个属性.  
| 操作 | 描述 |
| -- | ---- |
| Add | 把结果与Modifier指定的属性相加, 使用负数来达到减去的效果 .|
| Multiply | 把结果与Modifier指定的属性相乘 |
| Divide | 把结果与Modifier指定的属性相除 |
| Override |用结果覆盖Modifier指定的属性 |  
一个属性的CurrentValue是所有的添加到其BaseValue上的Modifiers的合计结果.在GameplayEffectAggregator.cpp文件中的FAggregatorModChannel::EvaluateWithBase中定义了Modifier的合计公式.
```
((InlineBaseValue + Additive)*Multiplicitive) / Division
```
Any `Override` `Modifiers` will override the final value with the last applied `Modifier` taking precedence.

**注意:** 对于基于百分比的更改请确保使用`Multiply`乘法操作,以使其发生在加法操作之后.

**注意:** [预测Prediction](#concepts-p)在百分比更改中会有问题.

Modifier有四种类型:可缩放浮点数(Scalable Float),基于属性( Attribute Based), 自定义计算类(Custom Calculation Class), 调用者设置(Set by caller).它们都生成一个浮点值,此浮点值被用来根据Modifier的操作类型来修改指定的属性.  
| Modifier类型 | 描述 |
| -- | -- |
| 可缩放浮点数(`Scalable Float`) |  `FScalableFloats`是可以指向数据表(`Data Table`)的数据结构,该数据表中变量值作为行,等级(`Level`)作为列.`Scalable Float`可以自动读取数据表中`指定行`的当前Ability等级(或者是`GameplayEffectSpec`中覆写的不同等级值)`列`的值.获取到的值将进一步与一个系数相乘,如果没有指定数据表(`Data Table`),那么此值为1,此时相当于使用系数常量作为所有技能(`Ability`)等级(`Level`)下的硬编码值.![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png) |
| Attribute Based |  `基于属性的`Modifier使用源(`GameplayEffectSpec`创建者)或者目标(`GameplayEffectSpec`接收者)上的属性(`Backing Attributes`)的`CurrentValue`或者`BaseValue`值.并且可以用一个系数乘以这个值,并且在乘以系数之前与之后加上额外的两个增量值(`Pre/Post Multiply Additive Value`).启用快照(`Snapshot`)表示在创建`GameplayEffectSpec`时就捕获`Backing Attributes`的值.不启用快照表示在`GameplayEffectSpec`应用时捕获`Backing Attributes`的值.|
| Custom Calculation Class | 自定义计算类为复杂的Modifier提供了最大的灵活性.它使用一个[ModifierMagnitudeCalculation](#concepts-ge-mmc)类,并且可以用一个系数乘以结果浮点值,并且在乘以系数之前与之后加上额外的两个增量值(`Pre/Post Multiply Additive Value`). |
| Set By Caller | 此种修改器的结果值是在`GameplayEffect`的外部设置的.技能(Ability)或者任何创建`GameplayEffectSpec`的东西都可以在运行时在`GameplayEffectSpec`上设置它.例如,你想根据玩家按住按键的时间的长度来为技能充能,从而决定技能可以造成的伤害的大小,你可以使用`SetByCaller`来达到此目的.`SetByCaller`本质上是存储在`GameplayEffectSpec`上的`TMap<FGameplayTag,float>`.此种修改器只是根据提供的`FGameplayTag`来查找对应的`float`值.如果指定的`GameplayTag`对应的值并不存在于`GameplayEffectSpec`上,此时会抛出一个错误,并且返回值为0.这在除法操作中会导致错误.详情参阅[SetByCallers](#concepts-ge-spec-setbycaller) |  

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 3.5.5 Stacking Gameplay Effects
在默认情况下`GameplayEffect`的新应用的`GameplayEffectSpec`实例是不知道也不关心以前应用的`GameplayEffectSpec`实例的情况的.可以把`GameplayEffect`设置为叠加(`Stacking`)的,这样就不会添加新的`GameplayEffectSpec`,而是增加现有`GameplayEffectSpec`的叠加层数(`Stack count`).叠加只对持续型(`Duration`)与永久(`Infinite`)型`GameplayEffect`有效.

叠加有两种类型:按源计数叠加层数 与 按目标计数叠加层数.
| 叠加类型 | 说明 |
| -- | -- |
| Aggregate by Source | 每个源`ASC`都在目标上对应一个单独的叠加实例,每个源都可以叠加X层 |
| Aggregate by Target | 在目标上只有一个叠加实例,忽略源`ASC`的不同,所有源`ASC`总共只能叠加X层 |

关于GameplayEffect到期,持续时间刷新,周期效果刷新的策略,请参看GameplayEffect蓝图里的鼠标悬停提示信息...

示例项目中包含有一个自定义蓝图节点用来监听`GameplayEffect`叠加层数的变化.`UMG`组件会使用它来更新角色拥有的被动护甲层数.这个异步任务会永远存在,直到手动调用`EndTask()`函数,可以在`UMG`组件的析构事件中调用此函数.详情参阅:AsyncTaskEffectStackChanged.h/cpp.
![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 3.5.6 Granted Abilities
`GameplayEffects`可以为ASCs授予新的`GameplayAbilities`.只有持续型与永久型`GameplayEffects`才可以授予`Abilities`.

一个常见的用例是:当你想强迫其他玩家执行某些操作,例如被击中或推动时强迫移动他们.你可以对他们应用一个`GameplayEffect`, 以授予他们自动激活的Ability,可以对他们执行所需要的操作.(如何在授予后就自动激活一个Ability,请参见[Passive Abilities](#concepts-ga-activating-passive)).

设计者可以决定一个`GameplayEffect`可以授予那些`Abilities`,这些`Abilities`的级别,这些`Abilities`的输入绑定和这些`Abilities`的删除策略.
| 删除策略 | 说明 |
| -- | -- |
| 立刻取消`Ability` | 当`GameplayEffect`被从目标上移除时,授予的`Ability`会被立刻取消和删除 |
| `Ability`结束时删除 | 允许授予的`Ability`继续执行到结束,然后才会被删除 |
| 无操作 | 当`GameplayEffect`被从目标上移除时,授予的`Ability`不会受到任何影响.目标ASC将永久拥有此`Ability`,直到将其手动删除. |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 3.5.7 Gameplay Effect Tags
`GameplayEffects`上携带有多个[GameplayTagContainers](#concepts-gt).一种类别的标签通常由三个`GameplayTagContainer`组成:`Added`,`Removed`和`Combined`.设计者可以编辑`Added`与`Removed` `GameplayTagContainer`,编译蓝图后结果会自动显示在`Combined` `GameplayTagContainer`中.

`Added`标签是`GameplayEffect`的拥有者以前没有拥有的新添加的标签.`Removed`标签 tags are tags that parent classes have but this subclass does not have.`Added` tags are new tags that this `GameplayEffect` adds that its parents did not previously have. `Removed` tags are tags that parent classes have but this subclass does not have.

| 类别 | 描述 |
| -- | :-- |
| GameplayEffect Asset Tags | `GameplayEffect`拥有的标签. 它们自身不执行任何功能,仅用于描述`GameplayEffect`的目的. |
| Granted Tags | 既存在于`GameplayEffect`上,也会授予目标`ASC`的标签.当`GameplayEffect`移除时它们也会被从`ASC`上移除.只对持续型和永久型`GameplayEffect`有效.|
| Ongoing Tag Requirements	 | 一旦应用后,这些标签将决定`GameplayEffect`是打开还是关闭,`GameplayEffect`被关闭后仍然是处于应用状态(并不一定要被删除).如果`GameplayEffect`因为未能满足`Require Tags`而被关闭,当`Require Tags`被满足时,`GameplayEffect`会被再次打开,并重新应用它的`Modifiers`.只对持续型和永久型`GameplayEffect`有效.|
| Application Tag Requirements | 目标`ASC`上的标签,决定`GameplayEffect`是否可以被应用到目标`ASC`上,如果无法满足要求,`GameplayEffect`是不会被应用到目标`ASC`上的.|
| Remove Gameplay Effects with Tags | 当此`GameplayEffect`成功应用时,目标`ASC`上所有拥有这些标签中的任何一个的`GameplayEffect`都要被移除.|

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 3.5.8 Immunity免疫
`GameplayEffects`可以授予免疫,基于[`GameplayTags`](#concepts-gt)有效的阻止其他`GameplayEffects`的应用.虽然可以使用其他方法(例如上节里讲到的`Application Tag Requirements`)来实现类似免疫的效果,但是使用免疫可以在`GameplayEffect`被免疫阻止时提供一个委托:`UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`

`GrantedApplicationImmunityTags`检查源`ASC`(包括源`Ability`的`AbilityTags`,如果有)是否拥有任何指定标签. 这是一种基于标签的为来自于特定角色或者源的`GameplayEffect`提供免疫的方法.  

`Granted Application Immunity Query`检查传入的`GameplayEffectSpec`是否与任何查询相匹配以阻止或允许其应用. 详情参见`GameplayEffect`蓝图中`Granted Application Immunity Query`字段的鼠标悬停提示. 

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 3.5.9 Gameplay Effect Spec
[`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`) 可以被看作是`GameplayEffects`的实例. 它拥有一个它所代表的`GameplayEffect`类的引用,等级,以及创建者等信息.它可以自由的在运行时创建与修改,而`GameplayEffects`只能由设计者在运行时之前事先创建完成.当应用一个`GameplayEffect`时,一个`GameplayEffectSpec`就会从此`GameplayEffect`上创建出来,然后新创建的`GameplayEffectSpec`被应用到目标上. 

使用一个`BlueprintCallable`的函数`UAbilitySystemComponent::MakeOutgoingSpec()`来从`GameplayEffects`创建`GameplayEffectSpec`. `GameplayEffectSpecs`创建后不必立即被应用到目标上.通常可以把`GameplayEffectSpec`传递给由某个技能创建的子弹对象,稍后子弹对象可以把`GameplayEffectSpec`应用到它所击中的目标上.当`GameplayEffectSpecs`被成功应用后会返回一个叫做`FActiveGameplayEffect`的新结构体. 

值得留意的 `GameplayEffectSpec`包含的内容:
* 创建此`GameplayEffectSpec`的`GameplayEffect`类
* `GameplayEffectSpec`的等级,通常和创建`GameplayEffectSpec`的技能的等级相同,但也可以不同.
* `GameplayEffectSpec`的持续时间,默认与`GameplayEffect`中的定义相同,但可以不同.
* 周期性`GameplayEffectSpec`的周期,默认与`GameplayEffect`中定义的相同,但可以不同.
* `GameplayEffectSpec`的当前堆叠层数,堆叠限制是在`GameplayEffect`中定义的.
*  [`GameplayEffectContextHandle`](#concepts-ge-context) 告诉我们谁创建了这个`GameplayEffectSpec`
* 在创建`GameplayEffectSpec`时捕获的`Attributes`值.(`Snapshotting`)
* 除了`GameplayEffect`授予的`GameplayTags`之外,`GameplayEffectSpec`授予目标的`DynamicGrantedTags`.
* 除了`GameplayEffect`具有的`AssetTag`之外，`GameplayEffectSpect`还具有`DynamicAssetTags`.
* `SetByCaller` `TMaps`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### SetByCallers
`SetByCallers`允许`GameplayEffectSpec`携带与`GameplayTag`或`FName`相关联的浮点类型的数值.它们被存储在各自的`TMaps`中:`TMap<FGameplayTag,float>`和`TMap<FName,float>`.它们可以被用做`GameplayEffect`上的修改器(`Modifiers`),也可以用作传递浮点值的一般通用手段.通常通过`SetByCallers`函数,把技能内部生成的数值数据传递给[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)或[`ModifierMagnitudeCalculations`](#concepts-ge-mmc).  

| SetByCaller Use  |  Notes   |
|  --- |  ---  |
| `Modifiers`       | 必须在`GameplayEffect`类中事先定义.只能使用`GameplayTag`版本.如果在`GameplayEffect`类中事先由定义,但在`GameplayEffectSpec`中没有对应的`GameplayTag`-`float`值对, 当`GameplayEffectSpec`被应用时游戏会产生一个运行时错误然后返回0,对于除法运算这是一个潜在的问题.参见 [`Modifiers`](#concepts-ge-mods).|
| Elsewhere         | 不需要在任何地方事先定义,读取`GameplayEffectSpec`上不存在的`GameplayTag`-`float`值对时返回一个开发者自定义的默认值与可选的警告信息. | 

在蓝图中设置`SetByCaller`值,请使用所需的对应版本的蓝图节点(`GameplayTag` 或 `FName`):

![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

在蓝图中读取`SetByCaller`值, 需要在蓝图库中创建自定义节点.

在C++中设置`SetByCaller`值,请使用所需的对应版本的函数 (`GameplayTag` or `FName`):

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

在C++中读取`SetByCaller`值,请使用所需的对应版本的函数 (`GameplayTag` or `FName`):

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
我建议使用`GameplayTag`版本而不是`FName`版本. 这可以避免蓝图中的拼写错误,当复制`GameplayEffectSpec`时，与`FNames`相比，`GameplayTags`在网络上发送的效率更高，因为`TMaps`也可以复制。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 3.5.10 Gameplay Effect Context
[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html)结构体保存有`GameplayEffectSpec`的发起者与目标位置数据. 也可以继承此结构,以便在 `ModifierMagnitudeCalculations` / `GameplayEffectExecutionCalculations`和`AttributeSet`之间传递任意数据. 

如何继承`GameplayEffectContext`:

1. 继承 `FGameplayEffectContext`
1. 重写 `FGameplayEffectContext::GetScriptStruct()`
1. 重写 `FGameplayEffectContext::Duplicate()`
1. 重写 `FGameplayEffectContext::NetSerialize()` 如果你的新数据需要进行网络复制.
1. 如果你重写 `FGameplayEffectContext::NetSerialize()`, 请确保`TStructOpsTypeTraits`和父亲结构体`FGameplayEffectContext`的一样.
1. 重写 [`AbilitySystemGlobals`](#concepts-asg) 类中的`AllocGameplayEffectContext()`函数,以返回一个子类的新的对象.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 3.5.11 Modifier Magnitude Calculation
[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagcCalc` or `MMC`) 是在`GameplayEffects`中用作 [`Modifier`](#concepts-ge-mods)的强大的类.它的功能类似与 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec),但是功能较弱一点,最重要的是它是可预测的 [predicted](#concepts-p).它的唯一目标是从`CalculateBaseMagnitude_Implementation()`返回一个浮点值.你可以在C++或蓝图中继承并重载此函数.

`MMCs'`的优势在与可以捕获`GameplayEffect`的`源`或`目标`上的任意数量的`Attributes`,可以完全访问`GameplayEffectSpec`来读取`GameplayTags`和`SetByCallers`值对.`Attributes`可以快照也可以不快照. `快照属性(Snapshotted)`在创建`GameplayEffectSpec`时捕获,`非快照属性(Non snapshotted)`在应用`GameplayEffectSpec`时捕获.被捕获的属性会根据`ASC`上现有的MODs来重新计算其`CurrentValue`.这个重新计算**不会**运行`AttributeSet`类的 [`PreAttributeChange()`](#concepts-as-preattributechange) 函数.所以此处需要重新做数值夹取(`clamping`)操作. 

`MMC`返回的浮点值可以在`GameplayEffect`的`Modifier`中通过一个乘法系数以及两个前后相加系数进行进一步的计算处理.
 
下面的例子中,`MMC`从目标上捕获`Mana`属性,使用一个毒药`Effect`来减少`Mana`属性值,具体减少的数值根据目标当前拥有的`Mana`值与目标上可能带有的`Tag`来确定.
```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}
	
	return Reduction;
}
```
如果在`MMC`的构造函数中,你没有把`FGameplayEffectAttributeCaptureDefinition`添加到`RelevantAttributesToCapture`中去,后面又尝试捕获属性,你会收到一个关于缺少`Spec`的错误.如果你不需要捕获属性值,你不需要向`RelevantAttributesToCapture`中添加任何东西.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 3.5.12 Gameplay Effect Execution Calculation
[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html) (`ExecutionCalculation` or `ExecCalc`) 是`GameplayEffects`更改`ASC`的最强大的方法.如同[`ModifierMagnitudeCalculations`](#concepts-ge-mmc),它也可以捕获属性,并对其进行快照操作.与`MMC`不同的是它可以同时修改多个属性,并且本质上可以执行任何程序员想要执行的操作.这种强大的功能与灵活性带来的缺点是它不能被预测 ([predicted](#concepts-p)),并且必须在C++中实现.

`快照属性(Snapshotted)`在创建`GameplayEffectSpec`时捕获,`非快照属性(Non snapshotted)`在应用`GameplayEffectSpec`时捕获.被捕获的属性会根据`ASC`上现有的MODs来重新计算其`CurrentValue`.这个重新计算**不会**运行`AttributeSet`类的 [`PreAttributeChange()`](#concepts-as-preattributechange) 函数.所以此处需要重新做数值夹取(`clamping`)操作.

为了设置属性捕获,我们遵循Epic公司的ActionRPG示例项目展示的方法:定义一个结构体保存并定义如何捕获属性,然后在该结构体的构造函数中创建一个副本.每个`ExecCalc`都将拥有一个类似的结构体.**注意:** 每个结构体都必须有一个唯一的名字,因为它们共享同一个名称空间.为结构体使用相同的名称会导致属性捕获时出现错误(主要是捕获到错误的属性的值). 

对于 `Local Predicted`, `Server Only`, and `Server Initiated`的[`GameplayAbilities`](#concepts-ga), `ExecCalc`只在服务端运行.

`ExecCalc`的最常见应用场景就是从源和目标上读取许多属性值再根据一个复杂的公式来计算受到的伤害值.示例项目中包含有一个简单的`ExecCalc`,它从`GameplayEffectSpec`的[`SetByCaller`](#concepts-ge-spec-setbycaller)中读取伤害值,然后根据从目标上捕获的护甲属性得值来进行减伤计算.详情参见`GDDamageExecCalculation.cpp/.h`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 3.5.13 Custom Application Requirement
相比于简单的`GameplayTag`检查,[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html) (`CAR`)使设计人员可以对是否可以应用`GameplayEffect`进行高级控制.可以通过在蓝图中重写`CanApplyGameplayEffect()`,在C++中重写`CanApplyGameplayEffect_Implementation()`函数实现.

`CARs`应用实例: 
* 目标需要拥有一定数量的属性. 
* 目标需要某个`GameplayEffect`达到一定数量的堆叠.
 
`CARs`也可以用来做更高级的事情,例如检查目标上是否已经有过一个当前`GameplayEffect`的实例,然后可以更改现存的实例的持续时间 ([changing the duration](#concepts-ge-duration)),而不是重新应用一个新的实例(让`CanApplyGameplayEffect()`返回false).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 3.5.14 Cost Gameplay Effect
[`GameplayAbilities`](#concepts-ga) 拥有一个可选的`GameplayEffect`专门用作该技能的消耗值(`Cost`). `ASC`的某个属性(`Attribute`)的值必须大于消耗值才能激活技能`GameplayAbility`.如果无法负担`Cost GE`,那么这个技能将不会激活. `Cost GE`应该是拥有一个或多个`Modifiers`的从某些属性上减去一定数值的即时型的`GameplayEffect`.默认情况下`Cost GE`是可以预测的,建议保持此功能含义,不要使用`ExecutionCalculations`.建议使用`MMCs`来进行复杂的消耗值计算. 

刚开始的时候,你可能会为每一个技能`GA`都设计一个独特的`Cost GE`.更高级的做法是为多个技能`GA`使用同一个`Cost GE`,并使用不同`GA`的专用数据来修改在根据`Cost GE`创建的`GameplayEffectSpec`(技能具体的消耗数值保存在不同的`GA`中).**这种方法只对`实例化`的技能有效**.

重用`Cost GE`的两种方法:

1. **使用一个`MMC`.** 这是最简单的方法.创建一个[`MMC`](#concepts-ge-mmc)从`GameplayAbility`实例中读取消耗值,你可以从`GameplayEffectSpec`中获取到`GameplayAbility`实例.
```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```
在上面的例子中消耗值是我添加在`GameplayAbility`子类中的一个`FScalableFloat`.
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **重写`UGameplayAbility::GetCostGameplayEffect()`函数.** 重写此函数,并在运行时创建一个[`GameplayEffect`](#concepts-ge-dynamic),并从`GameplayAbility`中读取消耗值.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 3.5.15 Cooldown Gameplay Effect
[`GameplayAbilities`](#concepts-ga) 拥有一个可选的`GameplayEffect`专门用作该技能的冷却时间.冷却时间决定了技能激活后要经过多久才能再次激活技能.如果一个技能正在冷却中,它是无法再次激活的.这个`Cooldown GE`应该是一个持续型(`Duration`)`GameplayEffect`,不带`Modifiers`,并在`GameplayEffect`的`GrantedTags`(`Cooldown Tag`)中为每个`GameplayAbility`或每个技能槽(如果你的技能可以放在共享冷却时间的技能槽中)设置一个唯一的`GameplayTag`.技能(`GA`)实际上是去检查是否存在冷却标签(`Cooldown Tag`)而不是`Cooldown GE`.默认情况下`Cooldown GEs`是可以预测的,建议保持此功能含义,不要使用`ExecutionCalculations`,建议使用`MMCs`来进行复杂的冷却时间计算.

刚开始的时候,你可能会为每一个有冷却时间的技能`GA`都设计一个独特的`Cooldown GE`.更高级的做法是为多个技能`GAs`使用同一个`Cooldown GE`,并使用不同`GA`的专用数据来修改在根据`Cooldown GE`创建的`GameplayEffectSpec`(技能具体的冷却时长`duration`与冷却标签`Cooldown Tag`定义在不同的`GA`中).**这种方法只对`实例化`的技能有效**.

重用`Cooldown GE`的两种方法:

1. **使用一个[`SetByCaller`](#concepts-ge-spec-setbycaller).** 这是最简单的方法. 把你的冷却时长(`duration`)通过一个`GameplayTag`设置到共享的`Cooldown GE`的`SetByCaller`里.在你的`GameplayAbility`子类中,定义一个`FScalableFloat`类型的时长,一个`FGameplayTagContainer`类型的`Cooldown Tag`和一个`FGameplayTagContainer`类型的临时变量,用它来作为我们的`Cooldown Tag`与`Cooldown GE`的标签的联合返回结果.

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY()
FGameplayTagContainer TempCooldownTags;
```
然后重载`UGameplayAbility::GetCooldownTags()`函数,返回我们的`CooldownTags`与任何存在的`Cooldown GE`的标签的联合结果.
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```
最后,重载`UGameplayAbility::ApplyCooldown()`来注入我们的冷却标签(`Cooldown Tags`),并将`SetByCaller`设置到我们的冷却`GameplayEffectSpec`中去.
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
``` 
下图中,`Cooldown GE`的持续时间(`Duration`)被设置为一个`SetByCaller`,它的`Data Tag`标签被设置为`Data.Cooldown`.上面代码中的`OurSetByCallerTag`变量的值就应该是`Data.Cooldown`.

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **使用一个[`MMC`](#concepts-ge-mmc).** 这种方法与第一种方法的设置步骤基本一致.   setting the `SetByCaller` as the duration on the `Cooldown GE` and in `ApplyCooldown`. Instead, set the duration to be a `Custom Calculation Class` and point to the new `MMC` that we will make.

差别在于,第一种方法通过在`ApplyCooldown`函数中通过设置一个`SetByCaller`来作为`Cooldown GE`的冷却时长;第二种方法通过把冷却时长设置为`Custom Calculation Class`然后提供一个新的`MMC`来计算最终冷却值.
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY()
FGameplayTagContainer TempCooldownTags;
```
然后重载`UGameplayAbility::GetCooldownTags()`函数,返回我们的`CooldownTags`与任何存在的`Cooldown GE`的标签的联合结果.
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```
最后,重载`UGameplayAbility::ApplyCooldown()`来注入我们的冷却标签(`Cooldown Tags`)到冷却`GameplayEffectSpec`中.
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### Get the Cooldown Gameplay Effect's Remaining Time 获取冷却剩余时间
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**注意:** 查询客户端的冷却剩余时间要求客户端可以接收复制的`GameplayEffects`.这将取决于`ASC`的复制模式([replication mode](#concepts-asc-rm)).

<a name="concepts-ge-cooldown-listen"></a>
##### Listening for Cooldown Begin and End 监听冷却开始于结束事件
要监听冷却的开始事件,你可以通过绑定到`AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf`委托来响应`Cooldown GE`的添加事件.也可以通过绑定到`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag,EGameplayTagEventType::NewOrRemove)`来响应`Cooldown Tag`标签的添加事件.我建议监听`Cooldown GE`的添加事件,因为你此时可以访问到应用它的`GameplayEffectSpec`,由此你可以确定`Cooldown GE`是本地预测的还是服务器矫正后的.

 要监听冷却的结束事件,你可以通过绑定到`AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`委托来响应`Cooldown GE`的移除事件.也可以通过绑定到`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag,EGameplayTagEventType::NewOrRemove)`来响应`Cooldown Tag`的移除事件.我建议监听`Cooldown Tag`的移除事件,因为当服务端更正后的`Cooldown GE`到达时,我们本地预测版本的`Cooldown GE`会被移除掉,这会导致即使我们依旧在冷却中,`OnAnyGameplayEffectRemovedDelegate()`委托也会被触发.但是`Cooldown Tag`在删除本地预测`Cooldown GE`应用服务端更正后的`Cooldown GE`期间是不会发生改变的.

**注意:**  监听在客户端上添加或移除一个`GameplayEffect`要求客户端可以接收复制的`GameplayEffect`这将取决于`ASC`的复制模式([replication mode](#concepts-asc-rm)).

示例项目中包含有一个自定义蓝图节点,它可以监听冷却的开始与结束.界面UMG小组件使用它来更新陨石技能的冷却剩余时间.这个`AsyncTask`会一直存在,直到手动调用`EndTask()`函数,我们可以在UMG小组件的析构事件中调用此函数.详情参见 `AsyncTaskEffectCooldownChanged.h/cpp`.

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### Predicting Cooldowns 预测冷却时间
目前无法真正预测冷却时间.当本地预测`Cooldown GE`被应用时我们可以启动UI冷却计时器.但是`GameplayAbility`的实际冷却时间与服务器的冷却时间相联系.受玩家的网络延迟影响,本地预测冷却可能已经结束,但是在服务器上`GameplayAbility`可能依旧处于冷却状态.这将阻止`GameplayAbility`的重新激活,直到服务端的冷却结束.

在示例项目,当本地预测`Cooldown GE`开始时把陨石技能的UI图标显示为灰色,收到服务端校正后的`Cooldown GE`后才启动UI冷却计时器.来解决上面提到的问题.

这种处理方法导致的一个结果是,当技能的冷却时间很短时高延迟的玩家相比于低延迟的玩家技能释放率会较低,这对于高延迟玩家是很不利的. Fortnite avoids this by their weapons having custom bookkeeping that do not use cooldown `GameplayEffects`.

 Epic希望在GAS的未来迭代版本中实现真正的预测冷却时间(当本地预测冷却时间到期时,玩家可以重新激活技能,但服务器可能仍然处于冷却状态).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 3.5.16 Changing Active Gameplay Effect Duration 更改活动Gameplay Effect的持续时间
要更改`Cooldown GE`或者任何持续型`Gameplay Effect`的剩余时间,我们需要更改`GameplayEffectSpec`的`Duration`,更新它的`StartServerWorldTime`,更新它的`CachedStartServerWorldTime`,更新它的`StartWorldTime`,然后使用`CheckdDuration()`来重新允许持续时间检查.在服务端执行这些操作,并将`FActiveGameplayEffect`标记为脏,这些改变就会被复制到客户端.

**注意:** 这里使用了`const_cast`，可能不是Epic更改持续时间的预期方式，但到目前为止效果很好。

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 3.5.17 Creating Dynamic Gameplay Effects at Runtime  运行时动态创建GameplayEffects
运行时动态创建`GameplayEffects`是个高级主题,你不必经常这样做.

只有即时型(`Instant`)的`GameplayEffects`才可以在运行时使用C++从头创建.示例项目中动态创建了一个`GameplayEffect`,用来当角色受到致命一击时把金币与经验值发送给杀手.

```c++
// Create a dynamic instant Gameplay Effect to give the bounties
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

持续型(`Duration`)的和无限型(`Infinite`)的`GameplayEffect`无法在运行时动态创建.因为当它们发生复制时,会去查找不存在的`GameplayEffect`类的定义.为了实现此功能,你应该在编辑器中像平常一样制作`GameplayEffect`的原型类,然后在运行时根据需要定制`GameplayEffectSpec`实例.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 3.5.18 Gameplay Effect Containers (GameplayEffect 容器)
Epic的 [动作RPG示例项目](https://www.unrealengine.com/marketplace/en-US/slug/action-rpg) 实现了一个叫做`FGameplayEffectContainer`的结构体.它不是普通`GAS`类中,但是对于存放`GameplayEffects`和`TargetData`却极其方便.他可以自动完成一些工作,例如从`GameplayEffect`创建`GameplayEffectSpec`,并为其`GameplayEffectContext`设置默认值.在`GameplayAbility`中创建一个`GameplayEffectContainer`并把它传递给生成的子弹是很容易和直接的.我选择不在本示例项目中实现`GameplayEffectContainers`,以便展示在没有`GameplayEffectContainers`时如何使用普通的`GAS`类来进行工作.但我强烈建议你研究它们并把它们加入到你的项目中去.

如果想要访问`GameplayEffectContainers`中的`GESpecs`以执行类似添加`SetByCallers`之类的操作,请断开`FGameplayEffectContainer`,并通过`GESpec`数组的索引来访问`GESpec`引用,这要求你事先知道`GESpec`的索引位置.

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga"></a>
### 3.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>
#### 3.6.1 Gameplay Abliity Definition
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`) are any actions or skills that an `Actor` can do in the game. More than one `GameplayAbility` can be active at one time for example sprinting and shooting a gun. These can be made in Blueprint or C++.

[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`)是角色在游戏中可以执行的任何动作或技能,同时可以激活多个`GameplayAbility`,例如冲刺与射击可以同时进行.我们可以通过蓝图或C++来制作`GameplayAbility`.


`GameplayAbilities`示例:
* 跳跃  
* 冲刺 
* 射击 
*  每隔X秒被动的防御以此攻击 
* 使用药水 
* 开门 
* 收集资源 
* 建造建筑物

Things that should not be implemented with `GameplayAbilities`:
不应该作为`GameplayAbilities`来实现的功能:
* 基础运动输入
* 与UI界面的某些交互-不要使用`GameplayAbility`从商店购买东西.

这些不是强制规则,只是为的个人建议,您的设计和实现都可以有所不同.

`GameplayAbilitities`默认附带有一个等级功能.`GameplayAbility`的等级不同其对属性值的改变量大小或者技能本身的功能可以发生变化.
 
根据网络执行策略,`GameplayAbilities`可以在客户端与服务器上运行,但不能在模拟代理上运行.网络执行策略决定了`GameplayAbility`是否会本地预测([predicted](#concepts-p)).他们包括可选的[消耗与冷却](#concepts-ga-commit)`GameplayEffect`的默认行为.`GameplayAbilities`使用[`AbilityTasks`](#concepts-at)来执行随时间推移发生的动作,例如等待一个事件,等待一个属性发生变化,等待玩家选择目标,或者通过`Root Motion Source`来移动角色.

所有的`GameplayAbilities`都会将其`ActivateAbility()`替换为你自己的游戏逻辑.也可以在`EndAbility()`中添加额外的游戏逻辑,当`GameplayAbility`结束或取消时,`EndAbility()`会被执行.

简单`GameplayAbility`的流程图:
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

更复杂的`GameplayAbility`的流程图:
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

可以使用多个相互交互(激活,取消等)的`GameplayAbilities`来实现更复杂的技能.

<a name="concepts-ga-definition-reppolicy"></a>
##### Replication Policy 复制政策
不要使用此选项.该名称具有误导性,你不需要使用它.默认情况下 [`GameplayAbilitySpecs`](#concepts-ga-spec) 会从服务端复制到拥有它的客户端.来自Epic的Dave Ratti表示[将来会将其删除](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89).

<a name="concepts-ga-definition-remotecancel"></a>
##### Server Respects Remote Ability Cancellation 服务器尊重远程技能取消
此选项经常会引起麻烦.这意味着如果客户端的`GameplayAbility`由于取消或自然完成而终止,都会强制服务器版本的的`GameplayAbility`终止,而无论服务器版本是否真正终止.后一个问题很严重,尤其是对于使用本地预测`GameplayAbilities`的高延迟的玩家来说.通常你要禁用此选项.

<a name="concepts-ga-definition-repinputdirectly"></a>
##### Replicate Input Directly 直接复制输入
设置此选项,将始终复制输入按下与释放事件到服务器端.如果你[将输入绑定到`ASC`](#concepts-ga-input)Epic建议不要使用此选项,而应依赖于现有的输入相关的[`AbilityTasks`](#concepts-at)的内置通用复制事件.

Epic的注释:
```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
/**直接复制输入状态.当Ability的bReplicateInputDirectly为true时会调用这些函数,不推荐使用(推荐使用通用复制事件)*/
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 3.6.2 Binding Input to the ASC 将输入绑定到ASC
`ASC`允许你直接将输入操作绑定到它自身,并把这些输入分配给`GameplayAbilities`.如果满足`GameplayTag`要求,分配给`GameplayAbilities`的输入操作会在按键按下时自动激活`GameplayAbilities`.要使用响应输出的内置`AbilityTasks`,需要分配输出操作.

除了分配用于激活`GameplayAbilities`的输入操作外,`ASC`还接受常规的确认`Confirm`与取消`Cancel`输入.这些特殊的输入由`AbilityTasks`用来确认诸如`TargetData`的内容或者取消它们.

要绑定一个输入`ASC`,你必须首先创建一个枚举值,将输入操作名称转换为一个字节.枚举名称必须要与项目设置中的输入操作的名称完全匹配.`DisplayName`的取值则没有关系.

示例代码:
```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```
如果你的`ASC`驻留在角色(`Character`)上,则可以在`SetupPlayerInputComponent()`中加上用于绑定到`ASC`的函数.
```c++
// Bind to AbilitySystemComponent
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"), FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```
如果你的`ASC`驻留在`PlayerState`上,则在`SetupPlayerInputComponent()`内部存在潜在的竞争状况,其中`PlayerState`可能尚未被复制到客户端.所以我建议在`SetupPlayerInputComponet()`与`OnRep_PlayerState()`中都尝试绑定到输入操作.`OnRep_PlayerState()`自己是不足够的,因为`PlayerState`可能在`PlayerController`告诉客户端调用`ClientRestart()`创建`InputComponent`之前就已经被复制了,此种情况下会导致`Actor`的`InputComponent`为空.示例程序展示了如何使用一个布尔变量来控制两处绑定的操作的流程,最终仅将输出操作绑定一次.

**注意:** 在示例项目的枚举中`Confirm`和`Cancel`与项目设置中的输出操作名称(`ConfirmTarget`和`CancelTarget`)不匹配,但是我们在`BindAbilityActivationToInputComponent()`中提供他们之间的映射关系.它们是特殊的,因为我们提供了它们之间的映射关系,所以它们的名称不需要是完全匹配的,(当然它们可以是完全匹配的).所有其他的枚举中的名称必须要和项目设置中的输入操作名称完全匹配.

对于只能通过一个输入激活的`GameplayAbilities`(它们将始终存在于同一个插槽中,例如MOBA游戏中常见的那样),我更喜欢在自己的`UGameplayAbility`子类中添加一个变量来定义它的输入操作.然后在授予此`GameplayAbility`时我可以从`ClassDefaultObject`读取这个变量值.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 3.6.3 Granting Abilities 授予技能
向`ASC`授予一个`GameplayAbility`会把它加入到`ASC`的`ActivatableAbilities`列表中,从而在满足`GameplayTag`的要求的前提下随意激活`GameplayAbility`.

我们在服务端授予`GameplayAbilities`,服务器会自动将 [`GameplayAbilitySpec`](#concepts-ga-spec) 复制到拥有的客户端.其他的客户端/模拟代理不会收到`GameplayAbilitySpec`.

示例程序中,`Character`类中保存有一个`TArray<TSubclassOf<UGDGameplayAbility>>`.当游戏开始时会从中读取`GameplayAbility`并把它们授予`ASC`
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Grant abilities, but only on the server	
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->CharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->CharacterAbilitiesGiven = true;
}
```
当授予`GameplayAbility`时,我们需要创建一个`GameplayAbilitySpec`,为此我们需要以下信息:`UGameplayAbility`类,技能等级,技能绑定的输入操作,源对象(`SourceObject`)或者将此`GameplayAbility`授予`ASC`的源对象.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 3.6.4 Activating Abilities 激活技能
如果一个`GameplayAbility`被分配了输入操作,当输入按键按下时它会自动激活(当然要满足`GameplayTag`需求).这可能并不总是激活`GameplayAbility`的理想方式.`ASC`提供了另外四种方式来激活`GameplayAbilities`:使用`GameplayTag`,`GameplayAbility`类,`GameplayAbilitySpec`句柄,以及事件.通过事件激活`GameplayAbility`允许你随事件传递一个负载数据.

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec);
```
要通过事件激活`GameplayAbility`,必须在`GameplayAbility`中设置其触发器(`Triggers`):分配一个`GameplayTag`并把`TriggerSource`设置为`GameplayEvent`.使用函数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor,FGameplayTag EventTag,FGameplayEventData Payload)`来发送事件.使用事件激活`GameplayAbility`时可以传递一个负载数据.

`GameplayAbility`的触发器(`Triggers`)也允许你在添加或删除`GameplayTag`的时候激活`GameplayAbility`(把`TriggerSource`设置为`Owned Tag Added`或者`Owned Tag Present`).

**注意:**  当从蓝图中通过事件激活`GameplayAbility`时,你必须使用`ActivateAbilityFromEvent`节点,并且图表中不能存在标准的`ActivateAbility`节点.如果`ActivateAbility`节点存在,它将始终被从`ActivateAbilityFromEvent`节点调用.

**注意:** 除非你希望`GameplayAbility`像被动技能一样一直运行,否则不要忘记在`GameplayAbility`需要终止时调用`EndAbility()`.

**本地预测**`GameplayAbilities`的激活顺序:
1. 客户端调用`TryActivateAbility()`
2. 调用`InternalTryActivateAbility()`
3. 调用`CanActivateAbility()`,检查是否满足`GameplayTag`需求,`ASC`是否能负担的起消耗值,`GameplayAbility`是否正在冷却中,当前是否有其它实例处于活动状态.
4. 调用`CallServerTryActivateAbility()`并把生成的预测密匙(`Prediction Key`)发送给它.
5. 调用 `CallActivateAbility()`
6. 调用`PreActivate()`Epic将其称为样"板化初始化内容".
7. 调用`ActivateAbility()`最终激活技能

**服务端** 收到 `CallServerTryActivateAbility()`:
1. 调用 `ServerTryActivateAbility()`
1. 调用 `InternalServerTryActiveAbility()` 
1. 调用 `InternalTryActivateAbility()`
1. 调用 `CanActivateAbility()` 检查是否满足`GameplayTag`需求,`ASC`是否能负担的起消耗值,`GameplayAbility`是否正在冷却中,当前是否有其它实例处于活动状态.
1. 调用 `ClientActivateAbilitySucceed()`.如果成功,告诉它更新其`ActivationInfo` ,表面服务端已经确认其激活状并广播`OnConfirmDelegate`委托. 这与输入确认不同.
1. 调用 `CallActivateAbility()`
1. 调用 `PreActivate()` Epic将其称为样"板化初始化内容".
1. 调用 `ActivateAbility()` 最终激活技能

如果服务端在任何时间激活失败,它将会调用`ClientActivateAbilityFailed()`,立即终止客户端的`GameplayAbility`并且撤销任何可预测的更改.

<a name="concepts-ga-activating-passive"></a>
##### Passive Abilities 被动技能
若要实现自动激活并连续运行的被动`GameplayAbilities`,重载`UGameplayAbility::OnAvatarSet()`,这个重载函数会在`GameplayAbility`被授予,`AvatarActor`被设置,`TryActivateAbility()`被调用后自动调用.

我建议在你的`UGameplayAbility`类的子类中添加一个布尔变量,来指明`GameplayAbility`是否需要在被授予后就立刻激活.在示例程序中`被动护甲堆叠技能`是授予后立即就被激活的.

被动`GameplayAbilities`同行具有`只在服务器运行`(`Server Only`)的[`网络执行策略`](#concepts-ga-net)

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (ActivateAbilityOnGranted)
	{
		bool ActivatedAbility = ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```
Epic说这个函数是初始化被动技能的正确的地方,可以在这里做一些类似`BeginPlay`函数里要做的事情.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-canceling"></a>
#### 3.6.5 Canceling Abilities 取消技能
要从内部取消一个`GameplayAbility`,可以调用`CancelAbility()`函数.它会调用`EndAbility()`函数,并设置它的`WasCancelled`参数为true.

`ASC`为从外部取消一个`GameplayAbility`提供了以下方法:

```c++
/** Cancels the specified ability CDO. */
void CancelAbility(UGameplayAbility* Ability);	

/** Cancels the ability indicated by passed in spec handle. If handle is not found among reactivated abilities nothing happens. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** Cancel all abilities with the specified tags. Will not cancel the Ignore instance */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities regardless of tags. Will not cancel the ignore instance */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities and kills any remaining instanced abilities */
virtual void DestroyActiveState();
```
**注意:** 我发现如果你有非实例化(`Non-Instanced`)`GameplayAbilities` `CancelAllAbilities`似乎无法正常工作.当遇到非实例化`GameplayAbility`时它就会放弃继续执行了.`CancelAbilities`函数可以更好的处理非实例化`GameplayAbilities`,示例项目中使用的也是此函数(跳跃技能是一个非实例化`GameplayAbility`).你可能有你自己的不同实现方法.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 3.6.6 Getting Active Abilities 获取激活的技能
初学者经常问"我如何获取到激活的能力?"也许是为了在它上面设置变量或者想取消它.同时可以由多个`GameplayAbility`处在激活状态,所以不存在唯一的一个`激活的技能`.相反,你需要遍历`ASC`的`ActivatableAbilities`列表(`ASC`拥有与的被授予的`GameplayAbilities`),找到与你查询的[`Assert`或者`Granted` `GameplayTag`]相匹配的那一个.

`UAbilitySystemComponent::GetActivatableAbilities()`函数返回一个 `TArray<FGameplayAbilitySpec>`供你遍历查询.

The `bOnlyAbilitiesThatSatisfyTagRequirements` parameter will only return `GameplayAbilitySpecs` that satisfy their `GameplayTag` requirements and could be activated right now. For example, you could have two basic attack `GameplayAbilities`, one with a weapon and one with bare fists, and the correct one activates depending on if a weapon is equipped setting the `GameplayTag` requirement. See Epic's comment on the function for more information.
`ASC`也提供了另外一个辅助函数,它接受一个`GameplayTagContainer`参数,来辅助搜索,避免手动遍历整个`GameplayAbilitySpecs`列表.当`bOnlyAbilitiesThatSatisfyTagRequirements`参数为true时,只会返回能满足`GameplayTag`需求并可以立即激活的`GameplayAbilitySpecs`.例如你可能有两个基础攻击`GameplayAbilities`,一种带有武器,另一种是赤手空拳,要激活哪一种需要根据是否有装备武器来确定(装备武器后会设置不同的`GameplayTag`需求).更多信息参见Epic对该函数的注释.

```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```
等你获取到要查找的`FGameplayAbilitySpec`后,你可以在其上调用`IsActive()`函数.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 3.6.7 Instancing Policy 实例化策略
`GameplayAbility`的实例化策略(`Instancing Policy`)决定了在激活时是否以及如何实例化`GameplayAbility`.

| 实例化策略  |  描述  | 示例  |
| --- | --- | --- |
| 每个Actor实例化一次(`Instanced Per Actor`)    |每个`ASC`只有一个`GameplayAbility`实例,每次激活都重用这个实例 .  | 这可能是你使用的最多的实例化策略.你可以将它用于任何的技能,并获得技能激活之间的数据持久性.设计者负责在每次激活之前重置任何需要重置的变量 |
| 每次执行实例化一次(`Instanced Per Execution`) |每当`GameplayAbility`激活时,都会创建一个新的`GameplayAbility`实例.| 这些`GameplayAbilities`的好处是,每次激活技能都会自动重置变量.相比`Instanced Per Actor`这种策略的效率略低,因为每次激活都要生成新的`GameplayAbilities`.示例项目中没有使用到个策略|
| 不实例化(`Non-Instanced`) | `GameplayAbility`在其`ClassDefaultObject`上运行,不创建实例. | 这是三种策略中性能最好的一个,但也是执行能力限制最大的一个.非实例化 `GameplayAbilities`无法存储状态,意味着没有动态变量,也没有绑定到`AbilityTask`的委托.频繁激活的简单技能最适合使用这种策略,例如MOBA或RTS游戏中小兵的基础攻击技能.在示例项目中跳跃技能使用的是`非实例化`策略|

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 3.6.8 Net Execution Policy 网络执行策略
`GameplayAbility`的网络执行策略(`Net Execution Policy`)决定了谁来运行`GameplayAbility`以及以何种顺序运行`GameplayAbility`

|  网络执行策略  |  描述  |
| --- | --- |
| 仅本地(`Local Only`)  |`GameplayAbility`仅在拥有它的客户端上运行,对那些只在本地进行修饰性修改的技能可能很有用.单人游戏应该使用仅服务器(`Server Only`)策略.|
|本地预测(`Local Predicted`) | 本地预测`GameplayAbilities`现在拥有它的客户端上运行,然后在服务端运行.服务端版本会矫正客户端版本的任何错误预测. 详情参阅[Prediction](#concepts-p). |
| 仅服务器(`Server Only`) |  `GameplayAbility`只在服务端运行,被动`GameplayAbility`通常是`Server Only`的.单人游戏也应该使用此种策略.|
| 服务器启动(`Server Initiated`)  |服务器启动`GameplayAbilities`先在服务端激活然后在拥有它的客户端激活,我个人不太使用此种策略.| 

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 3.6.9 Ability Tags 技能标签
`GameplayAbilities`附带具有内置逻辑的`GameplayTagContainers`.这些`GameplayTags`都不可复制.

| `GameplayTag Container` | 描述 |
| --- | --- |
| 技能标签`Ability Tags` | `GameplayAbility`拥有的`GameplayTags`,这些`GameplayTag`只是用来描述`GameplayAbility`的.|
| `Cancel Abilities with Tag` |当激活这个`GameplayAbilities`时,在`Ability Tags`中拥有这些`GameplayTag`的其他`GameplayAbilities`会被取消.|
| `Block Abilities with Tag`|当激活这个`GameplayAbilities`时,在`Ability Tags`中拥有这些`GameplayTag`的其他`GameplayAbilities`会被阻止激活.|
| `Activation Owned Tags`| 当激活这个`GameplayAbility`时这些`GameplayTags`会被提供给`GameplayAbility`的所有者.   记住,这些标签不会被复制.|
|激活所需标签`Activation Required Tags`  |只有当拥有者拥有**全部**这些`GameplayTag`时这个`GameplayAbility`才可以被激活.|
|激活阻止标签 `Activation Blocked Tags`   | 如果拥有者拥有这些标签中的**任何**一个,则无法激活此`GameplayAbility`|
| `Source Required Tags` | 只有当源(`Source`)拥有**全部**这些`GameplayTag`时,这个`GameplayAbility`才可以被激活.仅当`GameplayAbility`是通过事件激活时才回设置`Source` `GameplayTags`|
| `Source Blocked Tags`| 如果源(`Source`)拥有这些标签中的**任何**一个,则无法激活此`GameplayAbility`.仅当`GameplayAbility`是通过事件激活时才会设置`Source` `GameplayTags`|
| `Target Required Tags`| 只有当目标(`Target`)拥有**全部**这些`GameplayTag`时,这个`GameplayAbility`才可以被激活. 仅当`GameplayAbility`是通过事件激活时才会设置`Target` `GameplayTags` |
| `Target Blocked Tags`|  如果目标(`Target`)拥有这些标签中的**任何**一个,则无法激活此`GameplayAbility`.仅当`GameplayAbility`是通过事件激活时才会设置`Target` `GameplayTags`|

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 3.6.10 Gameplay Ability Spec
当`GameplayAbility`被授予后,一个对应的`GameplayAbilitySpec`就存在于`ASC`上了.它定义了一个可激活的`GameplayAbility` - `GameplayAbility`类,等级,输入绑定,必须与`GameplayAbility`类分开的运行时状态.

当在服务端授予一个`GameplayAbility`后,服务端会复制`GameplayAbilitySpec`到拥有它的客户端,以便可以激活它.

激活一个`GameplayAbilitySpec`会根据实例化策略(`Instancing Policy`)来创建`GameplayAbility`类的实例.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 3.6.11 Passing Data to Abilities 将数据传递给技能
`GameplayAbilities`工作的一般流程是:激活->生成数据->应用->结束.有的时候你可能需要使用现有数据,`GAS`提供了一些将现有的外部数据输入到`GameplayAbilities`的选项:

| 方法 | 描述 |
| --- | --- |
| 使用事件激活`GameplayAbility`|通过事件来激活`GameplayAbility`可以传递一个负载数据. 本地预测`GameplayAbility`的事件负载会从客户端复制到服务端,将两个Optional Object变量用于不适合任何现有变量的任意数据.这种方法的缺点是你无法通过输入绑定来激活技能了.`GameplayAbility`内部必须要设置触发器才能使用事件来激活.请使用函数:`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`来发送事件. |
| 使用 `WaitGameplayEvent` `AbilityTask` | 使用`WaitGameplayEvent` `AbilityTask`告诉`GameplayAbility`在激活后侦听带有负载数据的事件.事件的负载数据与其发送过程和方法一中的完全相同.此方法的缺点是事件不会由`AbilityTask`复制,只能应用于`Local Only` 和 `Server Only` 的`GameplayAbilities`.你可能编写自己的`AbilityTask`来复制事件有效负载.|
| 在`OwnerActor`或者`AvatarActor`上存储数据 | 使用存储在`OwnerActor`,`AvatarActor`或者其他任何可以引用到的对象上的可复制变量.此方法最灵活,并且可以与通过输入绑定激活的`GameplayAbility`一起工作. However, it does not guarantee the data will be synchronized from replication at the time of use.然而,它不保证在使用时数据已经从网络复制中进行过同步.你必须提前确保数据的同步,这意味着如果你设置了一个复制变量,然后立即激活`GameplayAbility`,由于潜在的数据丢失问题,不能保证在接收端它们复制的先后顺序.|

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 3.6.12 Ability Cost and Cooldown 技能消耗与冷却
`GameplayAbilities`带有可选的消耗与冷却功能.技能消耗(`Cost`)是为了激活某个`GameplayAbility` `ASC`所必须具有的预定义数量的某一属性,用一个即时型(`Instant`)`GameplayEffect`([`Cost GE`](#concepts-ge-cost))实现.`冷却`是一个计时器用来阻止某个`GameplayAbility`再次激活,直到计时器到期,用一个持续型(`Duration`)`GameplayEffect`([`Cooldown GE`](#concepts-ge-cooldown))实现.

在`GameplayAbility`调用`UGameplayAbility::Activate()`之前,会先调用`UGameplayAbility::CanActivateAbility()`.这个函数会检查`ASC`是否能负担的起技能消耗(`UGameplayAbility::CheckCost()`)并保证`GameplayAbility`不是正在冷却中(`UGameplayAbility::CheckCooldown()`).

当`GameplayAbility`调用过`Activate()`函数之后,它可以随时使用`UGameplayAbility::CommitAbility()`函数来提交技能消耗与技能冷却,此函数会调用`UGameplayability::CommitCost()`和`UGameplayAbility::CommitCooldown()`.如果它们不应同时提交,设计者也可以选择分开调用`CommitCost()`和`CommitCooldown()`函数.提交技能消耗与技能冷却会再次调用`CheckCost()`和`CheckCooldown()`,这是`GameplayAbility`可能因为它们而失败的最后机会.

激活`GameplayAbility`,`ASC`的属性值可能会发生变化,在提交时无法满足技能消耗需求.如果提交时预测密匙([prediction key](#concepts-p-key) )有效则可以本地预测([locally predicted](#concepts-p) )提交技能消耗与技能冷却.

详细实现请参见 [`CostGE`](#concepts-ge-cost) 和 [`CooldownGE`](#concepts-ge-cooldown).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 3.6.13 Leveling Up Abilities 升级技能
通常有两种方法来升级技能:

| 升级方法 | 描述 |
| --- | --- |
| 取消授予技能并重新授予一个新等级技能 | 从`ASC`上取消授予(删除)`GameplayAbility`然后重新授予`ASC`新等级的`GameplayAbility`.如果此时`GameplayAbility`处于激活状态,这将会终止激活. |
| 增加`GameplayAbilitySpec`的等级  | 在服务端,找到`GameplayAbilitySpec`,增加它的等级,并将其标记为脏以便复制到拥有者客户端,如果此时`GameplayAbility`处于激活状态,不会终止激活.|

这两种方法的主要区别是你是否希望升级技能时,终结正在激活的`GameplayAbility`.根据你自己的技能特性,你很可能两种方法都会用到,我建议你在`UGameplayAbility`子类中添加一个布尔变量来表明要使用哪一种方法进行技能升级.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 3.6.14 Ability Sets 技能集
`GameplayAbilitySets`是便利`UDataAsset`类,用来保存:输入绑定,角色初始`GameplayAbilities`列表,技能授予逻辑.其子类也可以包含额外的逻辑与属性.Paragon的每个英雄都有一个`GameplayAbilitySet`,其中包含所有他们给定的`GameplayAbility`.

依我目前所了解到的内容来看,此类并不是必须的.示例项目在`GDCharacterBase`及其子类中处理了`GameplayAbilitySets`的所有功能.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at"></a>
### 3.7 Ability Tasks

<a name="concepts-at-definition"></a>
#### 3.7.1 Ability Task Definition
`GameplayAbilities`只在一帧内执行,这不能提供很大的灵活性.为了执行随时间推移而发生的动作,或者需要响应某个稍后时间点触发的委托,我们使用叫做`AbilityTasks`的潜在动作.

`GAS`提供了许多现成的`AbilityTasks`:
* 移动角色的任务
* 播放动画蒙太奇的任务
* 响应属性更改的任务
* 响应`GameplayEffect`更改的任务
* 响应玩家输入的任务

`UAbilityTask`的构造函数中强制硬编码了一个全游戏范围的最高1000个并发`AbilityTasks`同时运行的限制.为可能有几百个角色同时运行在世界中的游戏(例如RTS游戏)设计`GameplayAbilities`时要谨记这条限制.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-custom"></a>
#### 3.7.2 Custom Ability Tasks 自定义Ability Tasks
通常你将创建自己的自定义`AbilityTasks`(在C++中).示例项目中有两个`AbilityTasks`:

1. `PlayMontageAndWaitForEvent`是默认的`PlayMontageAndWait`和`WaitGameplayEvent` `AbilityTasks`的组合.这允许动画蒙太奇将游戏事件从`AnimNotifies`发送回启动他们的`GameplayAbility`.这样可以在动画蒙太奇的特定时间触发动作.
1. `WaitReceiveDamage`监听`OwnerActor`受到伤害.当英雄受到伤害时,被动护甲堆叠`GameplayAbility`将会移除一层护甲.

`AbilityTasks` 由以下组成:
* 一个创建`AbilityTask`新实例的静态函数.
* 当`AbilityTask`完成它的目的时进行广播的委托.
* 一个`Activate()`函数用来开始主要工作,绑定到外部委托等.
* 一个`OnDestroy()`函数用来做清理工作,包裹清理绑定的外部委托.
* 供外部委托绑定的回调函数.
* 成员变量和任何内部便利辅助函数.

**注意:** `AbilityTasks` 只能声明一种类型的输出委托.你所有的输出委托都必须是此种类型的,无论它是否有使用到声明中的那些参数.可以为没使用到的参数传递默认值.

`AbilityTasks`仅在运行拥有它的`GameplayAbility`的客户端或者服务端上运行.但是可以在`AbilityTask`的构造函数中将`bSimulatedTask`设置为true,使得其可以在模拟客户端上运行.重载`virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent)`函数,并设置任何需要复制的成员变量.这仅在很少的情况下有用,,例如运动`AbilityTasks`,此时你不想复制每个运动更改,而是模拟整个运动`AbilityTask`.所有的`RootMotionSource` `AbilityTasks`都会这样做. 示例请查看 `AbilityTask_MoveToLocation.h/.cpp`.

如果你在`AbilityTasks`的构造函数中设置`bTickingTask`为true,则`AbilityTasks`可以进行`Tick`,然后重载`virtual void TickTask(flaot DeltaTime);`函数.如果你想在帧之间平滑的差值的话这将会很有用.示例请查看 `AbilityTask_MoveToLocation.h/.cpp`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-using"></a>
#### 3.7.3 Using Ability Tasks 使用Ability Task
从C++中创建并激活`AbilityTask`(截取自`GDGA_FireGun.cpp`):
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

在蓝图中我们直接使用我们为`AbilityTask`创建的蓝图节点.我们不必调用`ReadyForActive()`函数.它在`Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`. `K2Node_LatentGameplayTaskCall`函数中自动调用,同时自动调用`BeginSpawningActor()` 和 `FinishSpawningActor()`(如果他们存在于你的`AbilityTask`类里) (参见 `AbilityTask_WaitTargetData`). 重申一下, `K2Node_LatentGameplayTaskCall`只在蓝图中施展其自动调用魔法,在C++中,我们还是需要手动调用 `ReadyForActivation()`, `BeginSpawningActor()`, 和 `FinishSpawningActor()`函数的.

![Blueprint WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

想要手动取消一个`AbilityTask`,只需要调用蓝图或者c++中的`AbilityTask`对象的`EndTask()`函数.

一些`AbilityTasks`在`GameplayAbility`结束时不会自动终止,例如`WaitTargetData`.如果它们依旧在运行,我们需要在`GameplayAbility`的`OnEndAbility`函数中手动终止掉.(`WaitTargetData`在玩家按下`确认`或`取消`输入时自动结束)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-rms"></a>
#### 3.7.4 Root Motion Source Ability Tasks 根运动源任务
`GAS`自带有随时间移动角色(`Characters`)的`AbilityTasks`,例如击退,复杂跳跃,拉动与投掷等使用连接到`CharacterMovementComponent`的`Root Motion Source`的动作.

在UE4.20之前,这些`RootMotionSource` `AbilityTasks`和`CharacterMovementComponent`一起可预测的工作.

 在UE4.20中发生的某些更改,打破了`Root Motion Source` `AbilityTasks`的可预测性,或者以我们不知道的如何正确使用它的方式进行了更改.我们已经联系了Epic,但他们尚未回.我担心我们需要等到新的 [Network Prediction plugin](#concepts-p-npp) 完成后,此功能才能恢复正常.

目前如果你需要一个真正可预测的移动技能,你需要手动在`CharacterMovementComponent`中实现它.

`RootMotionSource` `AbilityTasks`在单人游戏中依旧工作的很好,并且在低延迟的多人游戏中也是可以接受的.

示例项目中将`AbilityTask_ApplyRootMotionConstantForce` 用于冲刺`GameplayAbility`.

**注意:** 最近在Unreal Engine master上有一个[提交](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c)可能修复了预测错误.并可能在4.25中发布.如果需要你现在就可以把修改纳入到自己的自定义引擎中.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc"></a>
### 3.8 Gameplay Cues

<a name="concepts-gc-definition"></a>
#### 3.8.1 Gameplay Cue Definition 定义
`GameplayCues` (`GC`) 执行与游戏性无关的东西,例如音效,粒子效果,相机抖动,等.`GameplayCues`通常都是复制的(除非明确的在本地`执行`,`添加`,`移除`)与可预测的.

 我们通过`ASC`向`GameplayCueManager`发送一个前缀强制为**GameplayCue**的`GameplayTag`以及一个事件类型(`执行`,`添加`,`删除`)来触发`GamepalyCues`.
 
`GameplayCueNotify` 对象和其他实现了`IGameplayCueInterface`接口的`Actors`可以基于`GameplayCue`的`GameplayTag`(`GameplayCueTag`)来订阅这些事件.

**注意:** 强调一下 `GameplayCue`的 `GameplayTags` 必须使用**GameplayCue**作为前缀.所以一个正确的`GameplayCue`的 `GameplayTag` 必须看起来像这样:`GameplayCue.A.B.C`.

`GameplayCueNotifies`有两类:`Static`和`Actor`.它们响应不同的事件,并且不同的`GameplayEffect`可以出发它们.使用你自己的逻辑覆盖对应的事件.

| `GameplayCue` 类别  |  事件 | `GameplayEffect` 类型   |  描述 |
| --- | --- | --- | --- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute` | `Instant` or `Periodic` | 静态`GameplayCueNotifies`运行在`ClassDefaultObject`上(意味着没有实例),非常适合一次性效果例如撞击.|
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)  | `Add` or `Remove` | `Duration` or `Infinite` | 当添加
Actor `GameplayCueNotifies` 时会生成一个新的实例. 因为它们是实例化的,它们可以随着时间推移执行操作,直到将其移除.这对于循环播放音乐与粒子效果非常有用;这些声音和粒子效果会在背后的`Duration`或者`Infinite` `GameplayEffect`移除时被同时删除(或者手动调用将其删除.) 它们还带有选项以便管理同一时间可以添加的数量,以便具有相同效果的多个应用只能启动以此声音或粒子效果. |

`GameplayCueNotifies`技术上可以响应任何事件,但这通常使我们使用它们的方法.

**注意:**  当使用 `GameplayCueNotify_Actor`时,请选中 `Auto Destroy on Remove`选项,否则该`GameplayCueTag`后续的`Add`调用将不起作用.

When using an `ASC` [Replication Mode](#concepts-asc-rm) other than `Full`, `Add` and `Remove` `GC` events will fire twice on Server players (listen server) - once for applying the `GE` and again from the "Minimal" `NetMultiCast` to the clients. However, `WhileActive` events will still only fire once. All events will only fire once on clients.

示例项目中包含有一个 `GameplayCueNotify_Actor`用于眩晕与冲刺效果.还有个一个`GameplayCueNotify_Static`用于子弹撞击效果.通过本地触发它们而不是通过`GE`复制它们,可以进一步优化这些`GC`.我在示例项目中展示了使用它们的初级方法.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 3.8.2 Triggering Gameplay Cues

From inside of a `GameplayEffect` when it is successfully applied (not blocked by tags or immunity), fill in the `GameplayTags` of all the `GameplayCues` that should be triggered.

![GameplayCue Triggered from a GameplayEffect](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` offers Blueprint nodes to `Execute`, `Add`, or `Remove` `GameplayCues`.

![GameplayCue Triggered from a GameplayAbility](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

In C++, you can call functions directly on the `ASC` (or expose them to Blueprint in your `ASC` subclass):

```c++
/** GameplayCues can also come on their own. These take an optional effect context to pass through hit result, etc */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Add a persistent gameplay cue */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Remove a persistent gameplay cue */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** Removes any GameplayCue added on its own, i.e. not as part of a GameplayEffect. */
void RemoveAllGameplayCues();
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 3.8.3 Local Gameplay Cues
The exposed functions for firing `GameplayCues` from `GameplayAbilities` and the `ASC` are replicated by default. Each `GameplayCue` event is a multicast RPC. This can cause a lot of RPCs. GAS also enforces a maximum of two of the same `GameplayCue` RPCs per net update. We avoid this by using local `GameplayCues` where we can. Local `GameplayCues` only `Execute`, `Add`, or `Remove` on the invidiual client.

Scenarios where we can use local `GameplayCues`:
* Projectile impacts
* Melee collision impacts
* `GameplayCues` fired from animation montages

Local `GameplayCue` functions that you should add to your `ASC` subclass:

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

If a `GameplayCue` was `Added` locally, it should be `Removed` locally. If it was `Added` via replication, it should be `Removed` via replication.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 3.8.4 Gameplay Cue Parameters
`GameplayCues` receive a `FGameplayCueParameters` structure containing extra information for the `GameplayCue` as a parameter. If you manually trigger the `GameplayCue` from a function on the `GameplayAbility` or the `ASC`, then you must manually fill in the `GameplayCueParameters` structure that is passed to the `GameplayCue`. If the `GameplayCue` is triggered by a `GameplayEffect`, then the following variables are automatically filled in on the `GameplayCueParameters` structure:

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude (if the `GameplayEffect` has an `Attribute` for magnitude selected in the dropdown above the `GameplayCue` tag container and a corresponding `Modifier` that affects that `Attribute`)

The `SourceObject` variable in the `GameplayCueParameters` structure is potentially a good place to pass arbitrary data to the `GameplayCue` when triggering the `GameplayCue` manually.

**Note:** Some of the variables in the parameters structure like `Instigator` might already exist in the `EffectContext`. The `EffectContext` can also contain a `FHitResult` for location of where to spawn the `GameplayCue` in the world. Subclassing `EffectContext` is potentially a good way to pass more data to `GameplayCues`, especially those triggered by a `GameplayEffect`.

See the 3 functions in [`UAbilitySystemGlobals`](#concepts-asg) that populate the `GameplayCueParameters` structure for more information. They are virtual so you can override them to autopopulate more information.

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 3.8.5 Gameplay Cue Manager
By default, the `GameplayCueManager` will scan the entire game directory for `GameplayCueNotifies` and load them into memory on play. We can change the path where the `GameplayCueManager` scans by setting it in the `DefaultGame.ini`.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

We do want the `GameplayCueManager` to scan and find all of the `GameplayCueNotifies`; however, we don't want it to async load every single one on play. This will put every `GameplayCueNotify` and all of their referenced sounds and particles into memory regardless if they're even used in a level. In a large game like Paragon, this can be hundreds of megabytes of unneeded assets in memory and cause hitching and game freezes on startup.

An alternative to async loading every `GameplayCue` on startup is to only async load `GameplayCues` as they're triggered in-game. This mitigates the unnecessary memory usage and potential game hard freezes while async loading every `GameplayCue` in exchange for minor game hitches and potentially delayed effects for the first time that a specific `GameplayCue` is triggered during play. As of now, this is my recommended solution until we figure out something better.

First we must subclass `UGameplayCueManager` and tell the `AbilitySystemGlobals` class to use our `UGameplayCueManager` subclass in `DefaultGame.ini`.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

In our `UGameplayCueManager` subclass, override `ShouldAsyncLoadRuntimeObjectLibraries()`.

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 3.8.6 Prevent Gameplay Cues from Firing
Sometimes we don't want `GameplayCues` to fire. For example if we block an attack, we may not want to play the hit impact attached to the damage `GameplayEffect` or play a custom one instead. We can do this inside of [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) by calling `OutExecutionOutput.MarkGameplayCuesHandledManually()` and then manually sending our `GameplayCue` event to the `Target` or `Source's` `ASC`.

If you never want any `GameplayCues` to fire on a specific `ASC`, you can set `AbilitySystemComponent->bSuppressGameplayCues = true;`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>
### 3.9 Ability System Globals
[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) 类保存了`GAS`的全局信息. 其中大多数变量都可以通过`DefaultGame.ini`来设置. 通常情况下你不必与此类有互动,但是你应该意识到它的存在.如果你需要子类化 [`GameplayCueManager`](#concepts-gc-manager) 或者 [`GameplayEffectContext`](#concepts-ge-context)之类的东西,则必须通过`AbilitySystemGlobals`来完成.

想要子类化`AbilitySystemGlobals`,请在 `DefaultGame.ini`中设置类名称:
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 3.9.1 InitGlobalData()
从UE4.24开始必须调用`UAbilitySystemGlobals::InitGlobalData()`以使用`TargetData`.否则你将收到与`ScriptStructCache`相关的错误,并且客户端将于服务端断开连接.这个函数只需要在项目中调用一次.Fortnite 在AssetManager类的初始加载函数中调用它,Paragon在`UEngine::Init()`函数中调用它.我发现将其放在 `UEngineSubsystem::Initialize()`函数中是个好地方,如示例项目所示.我觉得可以把它作为模板代码复制到你的项目中去,以避免`TargetData`出现问题.

If you run into a crash while using the `AbilitySystemGlobals` `GlobalAttributeSetDefaultsTableNames`, you may need to call `UAbilitySystemGlobals::InitGlobalData()` later like Fortnite in the `AssetManager` or in the `GameInstance` instead of in `UEngineSubsystem::Initialize()`. This crash is likely due to the order in which the `Subsystems` are created and the `GlobalAttributeDefaultsTables` requires the `EditorSubsystem` to be loaded to bind a delegate in `UAbilitySystemGlobals::InitGlobalData()`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>
### 3.10 Prediction
`GAS`自带客户端预测,但是并不能预测所有情况.`GAS`中的客户端预测意味着客户端不必等待服务端的许可即可激活`GameplayAbility`和应用`GameplayEffects`.它可以预测服务端授予它执行操作的权利,可以预测`GameplayEffects`将要应用到的目标.在客户端激活并经过一个网络延迟时间之后服务端才会执行`GameplayAbility`,然后告诉客户端的预测是否正确.如果客户端有任何预测错误,它将从错误预测中回滚所有的更改,以匹配服务端结果.

`GAS`相关的预测功能的权威代码在插件代码文件`GameplayPrediction.h`文件中.

Epic的态度是只预测你可以摆脱的东西.例如,Paragon和Fortnite不预测`伤害值`.他们很可能使用[`ExecutionCalculations`](#concepts-ge-ec)来计算`伤害值`,所以无法预测.这不是说你无法预测某些事情例如`伤害值`.如果你预测了,并且效果对你来说很好,那这就是很厉害的!

> ... 这不是一个"无缝的,自动预测一切的"解决方案.我们仍然认为最好将客户端预测保持在最低水平(意味着:预测可以摆脱的最小数量的东西).

*Epic的Dave Ratti在新的[`网络预测插件`](#concepts-p-npp)中的评论*

**可以预测的东西:**
> * Ability activation
> *	Triggered Events
> *	GameplayEffect application:
>    * Attribute modification (EXCEPTIONS: Executions do not currently predict, only attribute modifiers)
>    * GameplayTag modification
> * Gameplay Cue events (both from within predictive gameplay effect and on their own)
> * Montages
> * Movement (built into UE4 UCharacterMovement)

**不可以预测的东西:**
> * GameplayEffect removal
> * GameplayEffect periodic effects (dots ticking)

*From `GameplayPrediction.h`*

虽然我们可以预测`GameplayEffect`的应用,但无法预测其移除.一个绕过此限制的方法是:当我们想预测移除一个`GameplayEffect`时,我们可以预测应用一个与此`GameplayEffect`效果相反的`GameplayEffect`.例如我们目前有一个减速40%的`GameplayEffect`,我们可以预测应用一个加速40%的`GameplayEffect`,然后同时删除它们,这样就达到了预测性的移除减速40%的`GameplayEffect`的效果.这不适用与所有情况,对`GameplayEffect`的预测移除功能的支持还是需要的.来自Epicd的Dave Ratti表示希望将其添加到[GAS的未来版本](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)中.

因为我们无法预测`GameplayEffects`的移除,我们无法完整预测`GameplayAbility`的冷却时间,也没有如上面提出的反向效果解决方法.服务端复制的`Cooldown GE`会一直存在于客户端,并且服务器会拒绝任何试图绕过此操作的尝试(例如,使用最小复制模式).这意味着延迟高的客户端需要更多的时间来告诉服务器继续执行冷却时间,需要更多的时间收到服务端的`Cooldown GE`删除消息.这意味着高延迟玩家相比低延迟玩家开火率会降低,使得他们在处于不利地位.Fortnight使用自定义`簿记`而不是`Cooldown GEs`来避免这个问题.

关于`伤害值`的预测,我个人是不建议预测`伤害值`的,虽然很多初学`GAS`的人会去尝试预测它.我特别不建议尝试预测死亡.虽然你可以预测伤害值,但做起来很麻烦.如果你错误预测了伤害值,服务器修正你的错误预测,玩家会看到敌人的生命值重新跳回原始状态.基于此预测角色死亡,如果发生预测错误,服务器修正你的错误,敌人死而复生,这将是极为尴尬和令人沮丧的事情. 

**注意:** 更改`属性`的`即时` `GameplayEffects` (例如 `Cost GEs`) 可以被客户端无缝预测.预测即时型属性对其他角色的更改将在其属性中显示一个短暂的异常或`斑点Blip`预测的`即时型`的`GameplayEffects`实际上被视为`Infinite` `GameplayEffects`,因此如果预测错误,它们也是可以回滚的.当服务端的`GameplayEffect`被应用时,可能存在两个相同的`GameplayEffect`,导致`Modifier`在短时间内被应用两次或根本不被应用. 它最终会自行纠正,但有时玩家会注意到此问题.

`GAS`的预测实现尝试解决的问题:
> 1. "我可以这样做吗?" : 预测的基本协议.
> 2. "撤销"(`Undo`)   : 预测失败时如何撤销副作用.
> 3. "重做"(`Redo`)   : 如何避免即要本地预测,又要接受服务端复制导致的重复的副作用.
> 4. "完整性"(`Completeness`)   : 如何确认我们确实预测了所有的副作用.
> 5. "依赖关系"(`Dependencies`) : 如何管理互相依赖的预测与预测事件的链条.
> 6. "覆盖"(`Override`): 如何预测性的覆盖服务器复制或拥有的状态.

*来自 `GameplayPrediction.h`*

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 3.10.1 Prediction Key 预测密匙
`GAS`的预测基于`预测密匙` (`Prediction Key`)的概念,它是一个客户端在激活一个`GameplayAbility`时生成的整数标识符.

* 客户端在激活`GameplayAbility`时生成一个`预测密匙`.这是`激活预测密匙`(`Activation Prediction Key`).
* 客户端使用`CallServerTryActivateAbility()`将此预测密匙发送给服务端.
* 客户端会把此预测密匙添加到所有在此预测密匙有效时应用的`GameplayEffects`上.
* 服务端从客户端接收到预测密匙.
* 服务端把此预测密匙添加到所有它应用的`GameplayEffects`上.
* 服务器将预测密匙复制回客户端.
* 客户端收到从服务的复制的`GameplayEffects`并带有用于应用它们的预测密匙.如果任何复制的`GameplayEffects`与客户端使用相同预测密匙应用的`GameplayEffects`相匹配,则它们被正确预测.在目标上会临时存在两个`GameplayEffect`的副本,直到客户端移除其本地预测副本为止.
* 客户端从服务端接收回预测密匙.这是`复制预测密匙`(`Replicated Prediction key`).现在此预测密匙被标记为过时,不能再用于更多预测.在同一个`GameplayAbility`内的进一步的预测`Effects`需要一个新的`范围预测窗口`([`Scoped Prediction Window`](#concepts-p-windows)).
* 客户端删除**所有**使用过期的复制预测密匙创建的`GameplayEffects`. 服务端复制的`GameplayEffects` 将继续存在.客户端添加且未从服务端收到一个匹配的复制版本的任何`GameplayEffect`都是错误的预测.

Prediction keys are guaranteed to be valid during an atomic grouping of instructions "window" in `GameplayAbilities` starting with `Activation` from the activation prediction key.预测密匙在一个原子组指令窗口内是保证有效的(从`激活预测密匙`开始). 你可以认为仅在这一帧内有效(实际上是直到服务器把它复制回客户端之前都有效). 从`AbilityTasks`产生的任何潜在回调动作,很可能会在服务器把预测密匙复制回客户端并使之无效后发生.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 3.10.2 Creating New Prediction Windows in Abilities 创建新的预测窗口
为了从`AbilityTasks`的回调中预测更多操作,我们需要使用新的`范围预测密匙`(`Scoped Prediction Key`)创建一个新的`范围预测窗口`(`Scoped Prediction Window`). 有时将其称为服务器与客户端之间的`同步点`(`Sync Point`).有些`AbilityTasks`例如所有与输入有关的`AbilityTasks`具有内置的功能,来创建新的`范围预测窗口`,意味着`AbilityTasks`的回调中的原子代码具有有效的`范围预测密匙`可供使用.其他的`AbilityTask`例如`WaitDelay`没有内置的代码来为其回调创建新的`范围预测窗口`.如果你需要在一个没有内置代码创建`范围预测窗口`的`AbilityTask`(例如`WaitDelay`)的回调中预测新的操作,我们必须使用带有`OnlyServerWait`选项的`WaitNetSync` `AbilityTask`来手动执行此操作.当客户端命中一个带有`OnlyServerWait`选项的`WaitNetSync`时,会基于`GameplayAbility`的`激活预测密匙`产生一个新的`范围预测密匙`,通过RPCs传送到服务端,并将其添加到任何它应用的新的`GameplayEffects`上.当服务端命中一个带有`OnlyServerWait`选项的`WaitNetSync`时,它会等待一直等到从客户端接收到一个新的`范围预测密匙`,然后才会继续.这个`范围预测密匙`和`激活预测密匙`的动作相同:应用于`GameplayEffects`,然后复制回客户端,标记为过时.

你可以根据需要创建足够多的`范围预测窗口`.

如果你希望为自定义的`AbilityTasks`添加同步点功能,请参看输入相关的`AbilityTasks`的源代码,看它们是如何把`WaitNetSync` `AbilityTask`的代码整合进自身的.

**注意:** 当使用`WaitNetSync`时,会阻塞服务端的`GameplayAbility`的继续执行,直到接收到从客户端发来的`范围预测密匙`.这可能被恶意用户滥用,他们会侵入游戏并故意延迟发送它们的新的`范围预测密匙`.尽管Epic很少使用`WaitNetSync`, 如果你担心这个问题的话,建议你构建`AbilityTask`的新版本,使得他在没有客户端参与的情况下延迟后也可以继续执行.

示例项目在冲刺`GameplayAbility`中使用了`WaitNetSync`,在每次我们应用`耐力消耗` `Effect`的时候创建一个新的`范围预测窗口`,使得我们可以预测它.理想情况下我们在应用 消耗 与 冷却 `Effect`的时候需要一个有效的预测密匙.

如果你有一个预测的`GameplayEffect`在客户端应用了两次,那么你的预测密匙已过期,你遇到了`重做`问题.通常,你可以通过在应用`GameplayEffect`之前放置一个带有`OnlyServerWait`选项的`WaitNetSync` `AbilityTask`(这样会产生一个新的`范围预测密匙`),来解决此问题. 

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 3.10.3 Predictively Spawning Actors 可预测的创建Actor
在客户端可预测的创建`Actors`是一个高级话题.`GAS`没有为此提供开箱即用的内置功能(`SpawnActor` `AbilityTask`只在服务端创建`Actor`).关键概念是在客户端和服务端都创建复制的`Actor`.

如果这个`Actor`只是装饰性的没有任何游戏性的目的,简单的解决方案是重载`Actor`的`IsNetRelevantFor()`函数,来阻止服务端把它复制回拥有它的客户端.拥有者客户端会拥有它自己的本地创建版本,服务端和其他客户端会使用服务端的复制版本.
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

 如果创建的`Actor`会影响到游戏晚饭,例如需要预测伤害的子弹,您需要超出本文档范围的高级逻辑来实现它.参见Epic的GitHub库,看看UnrealTournament是如何预测性的创建子弹的.他们在拥有者客户端创建一个虚拟子弹,然后让他和服务端复制的子弹同步起来.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 3.10.4 Future of Prediction in GAS GAS中预测的未来
`GameplayPrediction.h`指出,他们将来会为`GameplayEffect`的移除以及周期性`GameplayEffects`添加预测功能.

来自Epic的Dave Ratti已表示[有兴趣解](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)决预测冷却时间的`延迟`问题,高延迟玩家会处于劣势.

Epic的新的[`网络预测插件`](#concepts-p-npp)有望与`GAS`完全互操作.就像之前的`CharacterMovementComponent`一样.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 3.10.5 Network Prediction Plugin 网络预测插件
Epic最近启动了一项计划,用新的`Network Prediction`插件替代`CharacterMovementComponent`.该插件仍处于早期阶段,但可以在Unreal Engine GitHub上早期访问.Epic提到他们希望在2019年底之前使用该插件,但这不是一个固定的期限,而只是一个大概的目标.现在还不能确定它将在哪个测试版的引擎中首次亮相.

希望这可以修复[`RootMotionSource` `AbilityTasks`](#concepts-at-rms)的预测问题.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae"></a>
## 4. Commonly Implemented Abilties and Effects

<a name="cae-stun"></a>
### 4.1 Stun
Typically with stuns, we want to cancel all of a `Character's` active `GameplayAbilities`, prevent new `GameplayAbility` activations, and prevent movement throughout the duration of the stun. The Sample Project's Meteor `GameplayAbility` applies a stun on hit targets.

To cancel the target's active `GameplayAbilities`, we call `AbilitySystemComponent->CancelAbilities()` when the stun [`GameplayTag` is added](#concepts-gt-change).

To prevent new `GameplayAbilitites` from activating while stunned, the `GameplayAbilities` are given the stun `GameplayTag` in their [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags).

To prevent movement while stunned, we override the `CharacterMovementComponent's` `GetMaxSpeed()` function to return 0 when the owner has the stun `GameplayTag`.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-sprint"></a>
### 4.2 Sprint
The Sample Project provides an example of how to sprint - run faster while `Left Shift` is held down.

The faster movement is handled predictively by the `CharacterMovementComponent` by sending a flag over the network to the server. See `GDCharacterMovementComponent.h/cpp` for details.

The `GA` handles responding to the `Left Shift` input, tells the `CMC` to begin and stop sprinting, and to predictively charge stamina while `Left Shift` is pressed. See `GA_Sprint_BP` for details.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ads"></a>
### 4.3 Aim Down Sights
The Sample Project handles this the exact same way as sprinting but decreasing the movement speed instead of increasing it.

See `GDCharacterMovementComponent.h/cpp` for details on predictively decreasing the movement speed.

See `GA_AimDownSight_BP` for details on handling the input. There is no stamina cost for aiming down sights.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ls"></a>
### 4.4 Lifesteal
I handle lifesteal inside of the damage [`ExecutionCalculation`](#concepts-ge-ec). The `GameplayEffect` will have a `GameplayTag` on it like `Effect.CanLifesteal`. The `ExecutionCalculation` checks if the `GameplayEffectSpec` has that `Effect.CanLifesteal` `GameplayTag`. If the `GameplayTag` exists, the `ExecutionCalculation` [creates a dynamic `Instant` `GameplayEffect`](#concepts-ge-dynamic) with the amount of health to give as the modifer and applies it back to the `Source's` `ASC`.

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-random"></a>
### 4.5 Generating a Random Number on Client and Server
Sometimes you need to generate a "random" number inside of a `GameplayAbility` for things like bullet recoil or spread. The client and the server will both want to generate the same random numbers. To do this, we must set the `random seed` to be the same at the time of `GameplayAbility` activation. You will want to set the `random seed` each time you activate the `GameplayAbility` in case the client mispredicts activation and its random number sequence becomes out of synch with the server's.

| Seed Setting Method                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use the activation prediction key                                            | The `GameplayAbility` activation prediction key is an int16 guaranteed to be synchronized and available in both the client and server in the `Activation()`. You can set this as the `random seed` on both the client and the server. The downside to this method is that the prediction key always starts at zero each time the game starts and consistently increments the value to use between generating keys. This means each match will have the exact same random number sequence. This may or may not be random enough for your needs. |
| Send a seed through an event payload when you activate the `GameplayAbility` | Activate your `GameplayAbility` by event and send the randomly generated seed from the client to the server via the replicated event payload. This allows for more randomness but the client could easily hack their game to only send the same seed value every time. Also activating `GameplayAbilities` by event will prevent them from activating from the input bind.                                                                                                                                                                     |

If your random deviation is small, most players won't notice that the sequence is the same every game and using the activation prediction key as the `random seed` should work for you. If you're doing something more complex that needs to be hacker proof, perhaps using a `Server Initiated` `GameplayAbility` would work better where the server can create the prediction key or generate the `random seed` to send via an event payload.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-crit"></a>
### 4.6 Critical Hits
I handle critical hits inside of the damage [`ExecutionCalculation`](#concepts-ge-ec). The `GameplayEffect` will have a `GameplayTag` on it like `Effect.CanCrit`. The `ExecutionCalculation` checks if the `GameplayEffectSpec` has that `Effect.CanLifesteal` `GameplayTag`. If the `GameplayTag` exists, the `ExecutionCalculation` generates a random number corresponding to the critical hit chance (`Attribute` captured from the `Source`) and adds the critical hit damage (also an `Attribute` captured from the `Source`) if it succeeded. Since I don't predict damage, I don't have to worry about synchronizing the random number generators on the client and server since the `ExecutionCalculation` will only run on the server. If you tried to do this predictively using an `MMC` to do your damage calculation, you would have to get a reference to the `random seed` from the `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 4.7 Non-Stacking Gameplay Effects but Only the Highest Magnitude is Actually Affects the Target
Slow effects in Paragon did not stack. Each slow instance applied and kept track of their lifetimes as normal, but only the greatest magnitude slow effect actually affected the `Character`.

I achieved this effect by using a dummy movespeed `Attribute` that all slow effects modify. The real movespeed `Attribute` is changed inside of `AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf()` and `AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()` by a [dynamically created `Infinite` `GameplayEffect`](#concepts-ge-dynamic) tied to the lifetime of the `GameplayEffect` with the highest magnitude slow or changing it when a new slow comes in with a higher magnitude.

You can think of the `GameplayEffects` changing the dummy movespeed `Attribute` as like a priority queue that is updated every time a slow `GameplayEffect` is added or removed where the highest magnitude slow effect gets copied into a dynamically created `GameplayEffect` that affects the real movespeed.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-paused"></a>
### 4.8 Generate Target Data While Game is Paused
If you need to pause the game while waiting to generate `TargetData` from a `WaitTargetData` `AbilityTask` from your player, I suggest instead of pausing to use `slomo 0`.

**[⬆ Back to Top](#table-of-contents)**

<a name="setup"></a>
## 5. Setting Up a Project Using GAS
Basic steps to set up a project using GAS:
1. Enable GameplayAbilitySystem plugin in the Editor
1. Edit `YourProjectName.Build.cs` to add `"GameplayAbilities", "GameplayTags", "GameplayTasks"` to your `PrivateDependencyModuleNames`
1. Refresh/Regenerate your Visual Studio project files
1. Starting with 4.24, it is now mandatory to call `UAbilitySystemGlobals::InitGlobalData()` to use `TargetData`. The Sample Project does this in `UEngineSubsystem::Initialize()`. See [`InitGlobalData()`](#concepts-asg-initglobaldata) for more information.

That's all that you have to do to enable GAS. From here, add an `ASC` and `AttributeSet` to your `Character` or `PlayerState` and start making `GameplayAbilities` and `GameplayEffects`!

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging"></a>
## 6. Debugging GAS
Often when debugging GAS related issues, you want to know things like:
> * "What are the values of my attributes?"
> * "What gameplay tags do I have?"
> * "What gameplay effects do I currently have?"
> * "What abilities do I have granted, which ones are running, and which ones are blocked from activating?".

GAS comes with two techniques for answering these questions at runtime - [`showdebug abilitysystem`](#debugging-sd) and hooks in the [`GameplayDebugger`](#debugging-gd).

**Tip:** UE4 likes to optimize C++ code which makes it hard to debug some functions. You will encounter this rarely when tracing deep into your code. If setting your Visual Studio solution configuration to `DebugGame Editor` still prevents tracing code or inspecting variables, you can disable all optimizations by wrapping the optimized function with the `PRAGMA_DISABLE_OPTIMIZATION_ACTUAL` and `PRAGMA_ENABLE_OPTIMIZATION_ACTUAL` macros. This cannot be used on the plugin code unless you rebuild the plugin from source. This may or may not work on inline functions depending on what they do and where they are. Be sure to remove the macros when you're done debugging!

```c++
PRAGMA_DISABLE_OPTIMIZATION_ACTUAL
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
PRAGMA_ENABLE_OPTIMIZATION_ACTUAL
```

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
Type `showdebug abilitysystem` in the in-game console. This feature is split into three "pages". All three pages will show the `GameplayTags` that you currently have. Type `AbilitySystem.Debug.NextCategory` into the console to cycle between the pages.

The first page shows the `CurrentValue` of all of your `Attributes`:
![First Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

The second page shows all of the `Duration` and `Infinite` `GameplayEffects` on you, their number of stacks, what `GameplayTags` they give, and what `Modifiers` they give.
![Second Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

The third page shows all of the `GameplayAbilities` that have been granted to you, whether they are currently running, whether they are blocked from activating, and the status of currently running `AbilityTasks`.
![Third Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

While you can cycle between targets with `PageUp` and `PageDown`, the pages will only show data for the `ASC` on your locally controlled `Character`.

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 Gameplay Debugger
GAS adds functionality to the Gameplay Debugger. Access the Gameplay Debugger with the Apostrophe (') key. Enable the Abilities category by pressing 3 on your numpad. The category may be different depending on what plugins you have. If your keyboard doesn't have a numpad like a laptop, then you can change the keybindings in the project settings.

Use the Gameplay Debugger when you want to see the `GameplayTags`, `GameplayEffects`, and `GameplayAbilities` on **other** `Characters`. Unfortunately it does not show the `CurrentValue` of the target's `Attributes`. It will target whatever `Character` is in the center of your screen. Look at a different `Character` and press Apostrophe (') again to switch to inspecting its `ASC`. The currently inspected `Character` has the largest red circle above it.

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS Logging
The GAS source code contains a lot of logging statements produced at varying verbosity levels. You will most likely see these as `ABILITY_LOG()` statements. The default verbosity level is `Display`. Anything higher will not be displayed in the console by default.

To change the verbosity level of a log category, type into your console:

```
log [category] [verbosity]
```

For example, to turn on `ABILITY_LOG()` statements, you would type into your console:
```
log LogAbilitySystem VeryVerbose
```

To reset it back to default, type:
```
log LogAbilitySystem Display
```

To display all log categories, type:
```
log list
```

Notable GAS related logging categories:

| Logging Category          | Default Verbosity Level |
| ------------------------- | ----------------------- |
| LogAbilitySystem          | Display                 |
| LogAbilitySystemComponent | Log                     |
| LogGameplayCueDetails     | Log                     |
| LogGameplayCueTranslator  | Display                 |
| LogGameplayEffectDetails  | Log                     |
| LogGameplayEffects        | Display                 |
| LogGameplayTags           | Log                     |
| LogGameplayTasks          | Log                     |
| VLogAbilitySystem         | Display                 |

See the [Wiki on Logging](https://wiki.unrealengine.com/Logs,_Printing_Messages_To_Yourself_During_Runtime) for more information.

**[⬆ Back to Top](#table-of-contents)**

<a name="acronyms"></a>
## 7. Common GAS Acronymns

| Name                               | Acronyms            |
|----------------------------------- | ------------------- |
| AbilitySystemComponent             | ASC                 |
| AbilityTask                        | AT                  |
| Action RPG Sample Project by Epic  | ARPG, ARPG Sample   |
| CharacterMovementComponent         | CMC                 |
| GameplayAbility                    | GA                  |
| GameplayAbilitySystem              | GAS                 |
| GameplayCue                        | GC                  |
| GameplayEffect                     | GE                  |
| GameplayEffectExecutionCalculation | ExecCalc, Execution |
| GameplayTag                        | Tag, GT             |
| ModiferMagnitudeCalculation        | ModMagCalc, MMC     |

**[⬆ Back to Top](#table-of-contents)**

<a name="resources"></a>
## 8. Other Resources
* [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* Source Code!
   * Especially `GameplayPrediction.h`
* [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/slug/action-rpg)
* [Dave Ratti from Epic's responses to community questions about GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)
* [Unreal Slackers Discord](https://unrealslackers.org/) has a text channel dedicated to GAS `#gameplay-abilities-plugin`
   * Check pinned messages
* [GitHub repository of resources by Dan 'Pan'](https://github.com/Pantong51/GASContent)
* [YouTube Videos by SabreDartStudios](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

**[⬆ Back to Top](#table-of-contents)**

<a name="changelog"></a>
## 9. GAS Changelog

This is a list of notable changes (fixes, changes, and new features) to GAS compiled from the official Unreal Engine upgrade changelog and from undocumented changes that I've encountered. If you've found something that isn't listed here, please make an issue or pull request.

<a name="changelog-4.24"></a>
### 4.24
* Fixed blueprint node `Attribute` variables resetting to `None` on compile
* Need to call `UAbilitySystemGlobals::InitGlobalData()` to use `TargetData` otherwise you will get `ScriptStructCache` errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a `GameplayTag` setter to a blueprint that didn't have the variable previously defined
* `UGameplayAbility::MontageStop()` function now properly uses the `OverrideBlendOutTime` parameter
* Fixed `GameplayTag` query variables on components not being modified when edited
* Added the ability for `GameplayEffectExecutionCalculations` to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
   * Implementation basically enables `GameplayTag`-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
   * To use, an execution has to add a tag to the new member variable `ValidTransientAggregatorIdentifiers`; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted `GameplayTag` source. We no longer reset the source when adding restricted tags to make it easier to add several in a row. 
* `APawn::PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`. Useful because [Mixed Replication Mode](#concepts-asc-rm) expects the owner of the `Pawn` to be the `Controller` if the `ASC` lives on the `Pawn`.

**[⬆ Back to Top](#table-of-contents)**
