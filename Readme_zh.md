

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
>     3.6 [Gameplay Abilities](#concepts-ga)  
>     3.7 [Ability Tasks](#concepts-at)  
>     3.8 [Gameplay Cues](#concepts-gc)  
>     3.9 [Ability System Globals](#concepts-asg)  
>     3.10 [Prediction](#concepts-p)   
   
 


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
| -------------------------- | ------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
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
| -- | ---- |
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

The Sample Project includes a custom Blueprint node that listens for GameplayEffect stack changes. The HUD UMG Widget uses it to update the amount of passive armor stacks that the player has. This AsyncTask will live forever until manually called EndTask(), which we do in the UMG Widget's Destruct event. See AsyncTaskEffectStackChanged.h/cpp.
![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 3.5.6 Granted Abilities
GameplayEffects can grant new GameplayAbilities to ASCs. Only Duration and Infinite GameplayEffects can grant abilities.
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
`GameplayEffects`上携带有多个[GameplayTagContainers](#concepts-gt).一种类别的标签通常由三个`GameplayTagContainer`组成:`Added`,`Removed`和`Combined`.设计者可以编辑`Added`与`Removed` `GameplayTagContainer`,编译蓝图后结果会自动显示在`Combined` `GameplayTagContainer`中.`Added` tags are new tags that this `GameplayEffect` adds that its parents did not previously have. `Removed` tags are tags that parent classes have but this subclass does not have.

| 类别 | 描述 |
| -- | :-- |
| GameplayEffect Asset Tags | `GameplayEffect`拥有的标签. 它们自身不执行任何功能,仅用于描述`GameplayEffect`的目的. |
| Granted Tags | 既存在于`GameplayEffect`上,也会授予目标`ASC`的标签.当`GameplayEffect`移除时它们也会被从`ASC`上移除.只对持续型和永久型`GameplayEffect`有效.|
| Ongoing Tag Requirements	 | 一旦应用后,这些标签将决定`GameplayEffect`是打开还是关闭,`GameplayEffect`被关闭后仍然是处于应用状态(并不一定要被删除).如果`GameplayEffect`因为未能满足`Require Tags`而被关闭,当`Require Tags`被满足时,`GameplayEffect`会被再次打开,并重新应用它的`Modifiers`.只对持续型和永久型`GameplayEffect`有效.|
| Application Tag Requirements | 目标`ASC`上的标签,决定`GameplayEffect`是否可以被应用到目标`ASC`上,如果无法满足要求,`GameplayEffect`是不会被应用到目标`ASC`上的.|
| Remove Gameplay Effects with Tags | 当此`GameplayEffect`成功应用时,目标`ASC`上所有拥有这些标签中的任何一个的`GameplayEffect`都要被移除.|

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga"></a>
### 3.6 Gameplay Abilities

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at"></a>
### 3.7 Ability Tasks

**[⬆ Back to Top](#table-of-contents)**


<a name="concepts-gc"></a>
### 3.8 Gameplay Cues

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>
### 3.9 Ability System Globals

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>
### 3.10 Prediction

**[⬆ Back to Top](#table-of-contents)**

















