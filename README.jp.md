# GASDocumentation

シンプルなマルチプレイヤーサンプルプロジェクトを使用した、 Unreal Engine 4 の GameplayAbilitySystem プラグイン (GAS) についての私の理解。これは公式のドキュメントではなく、このプロジェクトも私も EpicGames と提携していません。私はこの情報の正確性を保証しません。

このドキュメントの目的は、 GAS の主要な概念とクラスを説明し、 GAS での私の経験に基づいていくつかの追加の解説を提供することです。 コミュニティのユーザーの間には GAS の「仲間内の智恵（tribal knowledge）」がたくさんあり、私はここでの私の全てを共有することを目指しています。

サンプルプロジェクトとドキュメントは、 **Unreal Engine 4.26** で最新のものです。 Unreal Engine の古いバージョン用にこのドキュメントのブランチがありますが、それらはサポートされなくなり、バグや古い情報が含まれる可能性があります。

[GASShooter](https://github.com/tranek/GASShooter) は、マルチプレイヤー FPS/TPS 向けの GAS を使用した高度なテクニックを示す姉妹サンプルプロジェクトです。

最良のドキュメントは常にプラグインのソースコードです。

<a name="table-of-contents"></a>

## 目次

> 1. [GameplayAbilitySystem プラグインの紹介](#intro)
> 1. [サンプルプロジェクト](#sp)
> 1. [GAS を使用したプロジェクトの設定](#setup)
> 1. [GAS の概念](#concepts)
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [レプリケーションモード](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [セットアップと初期化](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [Gameplay Tags の変更への対応](#concepts-gt-change)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attribute の定義](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Attribute の変更への対応](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes （派生アトリビュート）](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Set 定義](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Set の設計](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [個々の Attributes を持つサブコンポーネント](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [実行時の AttributeSets の追加と削除](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [アイテム Attributes (武器弾薬)](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [アイテム毎の単純なfloat値](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [アイテム毎の `AttributeSet`](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [アイテム毎の `ASC`](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Attributes の定義](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Attributes の初期化](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [Gameplay Effect の定義](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [Gameplay Effects の適用](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [Gameplay Effects の削除](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Gameplay Effect Modifiers](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [乗算と除算の Modifiers](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Modifiers 上の Gameplay Tags](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Stacking Gameplay Effects （スタック）](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [Granted Abilities （付与）](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [Immunity （耐性）](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation （Modifier の大きさ計算）](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation （実行計算）](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Sending Data to Execution Calculations （実行計算へのデータ送信）](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [Backing Data Attribute Calculation Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [Backing Data Temporary Variable Calculation Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Get the Cooldown Gameplay Effect's Remaining Time （Gameplay Effect のクールダウンの残り時間を取得する）](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Listening for Cooldown Begin and End](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Predicting Cooldowns （クールダウンの予測）](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Changing Active Gameplay Effect Duration （有効な Gameplay Effect の持続時間の変更）](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [Creating Dynamic Gameplay Effects at Runtime （実行時の動的な Gameplay Effect の作成）](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers （コンテナ）](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability の定義](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [Replication Policy （レプリケーションポリシー）](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [Server Respects Remote Ability Cancellation （サーバーの「リモートによる Ability のキャンセル」の尊重）](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [Replicate Input Directly （入力の直接レプリケート）](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [Binding Input to the ASC （ASC に入力をバインド）](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [Binding to Input without Activating Abilities （Abilities を有効化せずに入力をバインド）](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Granting Abilities （付与）](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Activating Abilities （Abilities の有効化）](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [Passive Abilities （パッシブ Abilities）](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Canceling Abilities （Abilities のキャンセル）](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [Getting Active Abilities （有効な Abilities の入手）](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [Instancing Policy （インスタンス化ポリシー）](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution Policy （ネット実行ポリシー）](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [Passing Data to Abilities （Abilities へのデータの受け渡し）](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability Cost and Cooldown （Ability のコストとクールダウン）](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Leveling Up Abilities （Abilities のレベルアップ）](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability Batching （Ability のバッチ処理）](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security Policy （ネットセキュリティポリシー）](#concepts-ga-netsecuritypolicy)  
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task の定義](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [Custom Ability Tasks （Ability Tasks のカスタム）](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Using Ability Tasks （Ability Tasks の使用）](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks （ルートモーションソースを用いた Ability Tasks）](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue の定義](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Triggering Gameplay Cues （Gameplay Cues の発動）](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [Local Gameplay Cues （ローカル Gameplay Cues）](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [Prevent Gameplay Cues from Firing （Gameplay Cues の発火防止）](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue Batching （Gameplay Cue のバッチ処理）](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [Manual RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [Multiple GCs on one GE （１つの GE(Gameplay Effect) で複数の GC(Gameplay Cues)）](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue Events](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue Reliability](#concepts-gc-reliability)  
>    4.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [Prediction （予測）](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [Prediction Key （予測キー）](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [Creating New Prediction Windows in Abilities （Abilities における新しい予測ウィンドウの作成）](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [Predictively Spawning Actors （予測的にスポーンするアクター）](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [Future of Prediction in GAS （GAS における未来予測）](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [Network Prediction Plugin （ネットワーク予測プラグイン）](#concepts-p-npp)  
>    4.11 [Targeting （ターゲティング）](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [Target Data](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [Target Actors](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [Target Data Filters](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability World Reticles （Gameplay Ability ワールド焦点板）](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect Containers Targeting （Gameplay Effect コンテナのターゲティング）](#concepts-targeting-containers)  
> 1. [Commonly Implemented Abilities and Effects （一般的に実装されている Abilities と Effects）](#cae)  
>    5.1 [スタン](#cae-stun)  
>    5.2 [Sprint](#cae-sprint)  
>    5.3 [Aim Down Sights （エイムダウンサイト）](#cae-ads)  
>    5.4 [Lifesteal](#cae-ls)  
>    5.5 [Generating a Random Number on Client and Server （クライアントとサーバーでの乱数生成）](#cae-random)  
>    5.6 [Critical Hits](#cae-crit)  
>    5.7 [Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target （非スタック Gameplay Effects 、ただし、実際にターゲットに影響を与えるのは最大の大きさのもののみの）](#cae-nonstackingge)  
>    5.8 [Generate Target Data While Game is Paused （ゲームの一時停止中にターゲットデータの生成）](#cae-paused)  
>    5.9 [One Button Interaction System](#cae-onebuttoninteractionsystem)  
> 1. [Debugging GAS](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS Logging](#debugging-log)  
> 1. [Optimizations](#optimizations)  
>    7.1 [Ability Batching （Ability のバッチ処理）](#optimizations-abilitybatching)  
>    7.2 [Gameplay Cue Batching （Gameplay Cue のバッチ処理）](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponent レプリケーションモード](#optimizations-ascreplicationmode)  
>    7.4 [Attribute Proxy Replication](#optimizations-attributeproxyreplication)  
>    7.5 [ASC Lazy Loading （ASC 遅延読み込み）](#optimizations-asclazyloading)  
> 1. [Quality of Life Suggestions](#qol)  
>    8.1 [Gameplay Effect Containers （コンテナ）](#qol-gameplayeffectcontainers)  
>    8.2 [Blueprint AsyncTasks to Bind to ASC Delegates （ASC デリゲートにバインドする ブループリント AsyncTasks）](#qol-asynctasksascdelegates)  
> 1. [Troubleshooting](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` errors](#troubleshooting-scriptstructcache)  
>    9.3 [Animation Montages are not replicating to clients](#troubleshooting-replicatinganimmontages)  
>    9.4 [Duplicating Blueprint Actors is setting AttributeSets to nullptr ( Blueprint のアクターを複製すると、AttributeSets が nullptr に設定される)](#troubleshooting-duplicatingblueprintactors)  
> 1. [Common GAS Acronyms （一般的な GAS の頭字語）](#acronyms)  
> 1. [Other Resources](#resources)  
>    11.1 [Q&A With Epic Game's Dave Ratti](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [Community Questions 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [Community Questions 2](#resources-daveratti-community2)  
> 1. [GAS Changelog](#changelog)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)
         
<a name="intro"></a>

## 1. GameplayAbilitySystem プラグインの紹介

[公式ドキュメント](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html) （[日本語版](https://docs.unrealengine.com/ja/InteractiveExperiences/GameplayAbilitySystem/index.html)） から :
>ゲーム アビリティ システム は、 RPG や MOBA タイトルで使用される能力やアトリビュート の構築のための柔軟性の高いフレームワークです。ゲーム内で使用するアクションやパッシブ アビリティ、これらのアクションの結果としてさまざまなアトリビュートを作成または消耗させるステータス エフェクトを構築したり、これらのアクションの使用を制御するための「クールダウン」タイマーあるいはリソース コストの実装をしたり、各レベルでの能力とその効果のレベルを変更したり、パーティクルやサウンドエフェクトなどをアクティベートすることができます。簡単に言えば、このシステムは、近年の RPG や MOBA タイトルに組み込まれているジャンプのように単純なものから、お気に入りのキャラクターのアビリティと同じくらい複雑なゲーム アビリティの設計、実装および効率の良いネットワーク化を容易にします。

GameplayAbilitySystem プラグインは Epic Games によって開発され、 Unreal Engine 4 (UE4) に付属しています。 Paragon や Fortnite などの AAA商用ゲームでバトルテストが行われています。

プラグインは、以下のシングル及びマルチプレイヤーゲームですぐに使用できるソリューションを提供します :
* コストとクールダウンのオプションがあるレベルベースのキャラクターアビリティまたはスキルの実装 ([GameplayAbilities](#concepts-ga))
* Actor に属する数値の `Attributes` を操作する ([Attributes](#concepts-a))
* Actor へのステータス効果の適用 ([GameplayEffects](#concepts-ge))
* Actor への `GameplayTags` の適用 ([GameplayTags](#concepts-gt))
* 視覚効果または効果音の生成 ([GameplayCues](#concepts-gc))
* 上記すべてのレプリケーション

マルチプレイヤーゲームでは、 GAS は次の [client-side prediction （クライアントサイド予測）](#concepts-p) のサポートを提供します。 :
* アビリティの活性化
* アニメーションモンタージュの再生
* `Attributes` への変更
* `GameplayTags` の適用
* `GameplayCues` の生成
* `CharacterMovementComponent` に接続された `RootMotionSource` 関数を介した移動

**GAS は C++ で設定する必要があります** が、 `GameplayAbilities` と `GameplayEffects` は設計者がブループリントで作成できます。

GAS の現在の課題 :
* `GameplayEffect` レイテンシー調整（アビリティのクールダウンを predict （予測）することができないため、遅延時間が長いプレイヤーは遅延時間が短いプレイヤーに比べてクールダウンが短いアビリティの発火頻度が低くなります。）
* `GameplayEffects` の削除の predict （予測）はできません。しかし、反対の効果の `GameplayEffects` を追加することで事実上削除する predict （予測）はできます。 これは常に適切または実現可能であるとは限らず、依然として課題が残っています。 
* 定形テンプレート、マルチプレイヤーの例、ドキュメントが不足しています。願わくばこれがそれに役立ちますように！

**[⬆ Back to Top](#table-of-contents)**

<a name="sp"></a>

## 2. サンプルプロジェクト

このドキュメントには、 GameplayAbilitySystem プラグインは初めてではあるが、 Unreal Engine 4 は利用したことがある人々を対象とした、マルチプレイヤーサードパーソンシューティングのサンプルプロジェクトが含まれています。ユーザー（プロジェクトの利用者）は UE4 の C++、ブループリント、UMG、レプリケーション、及びその他の中級トピックの知識が必要です。このプロジェクトでは、初歩的なサードパーソンシューティングのマルチプレイヤー対応プロジェクトを、プレイヤー/AI に制御されたヒーロー用のクラス `PlayerState` に `AbilitySystemComponent` (`ASC`) を、同様に、 AI に制御されたミニオン用のクラス `Character` に `ASC` を、それぞれセットアップする方法の例を提供します。

このプロジェクトの目標は、このプロジェクトを単純に保ちつつ、 GAS の基本を示し、しばしば求められるアビリティをよくコメントされたコードで示すことです。初心者向けの為、高度なトピック、例えば [投射物の predicting （予測）](#concepts-p-spawn) のようなものは紹介していません。

実演している概念 :
* `ASC` on `PlayerState` vs `Character`
* `Attributes` のレプリケーション
* animation montages のレプリケーション
* `GameplayTags`
* `GameplayAbilities` の内部及び外部からの `GameplayEffects` の適用と削除
* アーマーによって軽減されたダメージを適用してキャラクターのHPを変更する
* `GameplayEffectExecutionCalculations`
* スタン効果
* 死とリスポーン
* サーバー上でアビリティから Actor （投射物）のスポーン
* ローカルプレイヤーの移動速度を「エイムダウンサイト中」と「スプリント中」で Predictively （予測）的に変更
* 常にスタミナを消費するスプリント
* マナを消費してアビリティを放つ
* パッシブアビリティ
* `GameplayEffects` のスタック
* Actor のターゲティング
* ブループリントで作成された `GameplayAbilities`
* C++ で作成された `GameplayAbilities`
* `Actor` `GameplayAbilities` 毎のインスタンス化
* インスタンス化されない `GameplayAbilities` （ジャンプ）
* 静的 `GameplayCues` (FireGun の投射物の衝突パーティクルエフェクト)
* Actor `GameplayCues` (スプリントとスタンのパーティクルエフェクト)

ヒーロークラスは以下のアビリティを持っています :

| アビリティ                 | 入力割当            | Predicted （予測） | C++ / Blueprint | 説明                                                                                                                                                                              |
| -------------------------- | ------------------- | ------------------ | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Jump                       | Space Bar           | Yes                | C++             | ヒーローをジャンプさせる。                                                                                                                                                        |
| Gun                        | Left Mouse Button   | No                 | C++             | ヒーローの銃から投射物を発射する。アニメーションは predicted （予測）されていますが、投射物はされていません。                                                                     |
| Aim Down Sights            | Right Mouse Button  | Yes                | Blueprint       | ボタンを押している間、ヒーローは遅く歩きカメラはズームインし、より正確な射撃ができるようになります。                                                                              |
| Sprint                     | Left Shift          | Yes                | Blueprint       | ボタンを押している間、ヒーローはスタミナを消費しながら速く走ります。                                                                                                              |
| Forward Dash               | Q                   | Yes                | Blueprint       | ヒーローをスタミナを消費して前進させる。                                                                                                                                          |
| Passive Armor Stacks       | Passive             | No                 | Blueprint       | ４秒毎にヒーローは最大４スタックのアーマーを獲得する。ダメージを受けるとアーマーのスタックを１失う。                                                                              |
| Meteor                     | R                   | No                 | Blueprint       | プレイヤーは場所を指定して隕石を降らせて、敵にダメージを与えたりスタンさせたりします。ターゲティングは predicted （予測）されていますが、隕石はスポーンされていません。           |

`GameplayAbilities` が C++ とブループリントのどちらで作成されているのかは問いません。ここではそれぞれの言語でどのように行うかの例として、両者を混ぜたものを使用しています。

ミニオンには事前定義済みの `GameplayAbilities` は付属していません。 赤ミニオンは高いHPリジェネを持ち、青ミニオンは高い初期HPを持ちます。

`GameplayAbility` の命名には、ブループリントで`GameplayAbility` のロジックを作成されたことを示す接尾辞に `_BP` を用いています。接尾辞がないものは C++ でロジックを作成されたという意味です。


**ブループリントアセット名の接頭辞**

| Prefix      | Asset Type          |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ Back to Top](#table-of-contents)**

<a name="setup"></a>

## 3. GAS を使用したプロジェクトの設定

GAS を使用するプロジェクトを設定するための基本的な手順 :
1. エディタで GameplayAbilitySystem プラグインを有効にする
1. `YourProjectName.Build.cs` を編集し、`PrivateDependencyModuleNames` に `"GameplayAbilities", "GameplayTags", "GameplayTasks"` を追加する
1. Visual Studio プロジェクトファイルを更新/再生成する
1. 4.24 以降、 [`TargetData`](#concepts-targeting-data) を利用する為には `UAbilitySystemGlobals::InitGlobalData()` の呼び出しが必要になりました。 サンプルプロジェクトでは `UEngineSubsystem::Initialize()` で行っています。 詳しくは [`InitGlobalData()`](#concepts-asg-initglobaldata) を参照してください。

GAS を有効にするために必要なことはこれだけです。ここからは `Character` または `PlayerState` に [`ASC`](#concepts-asc) と [`AttributeSet`](#concepts-as) を追加して、 [`GameplayAbilities`](#concepts-ga) と [`GameplayEffects`](#concepts-ge) を作り始めましょう！

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts"></a>

## 4. GAS の概念

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction （予測）](#concepts-p)

<a name="concepts-asc"></a>

### 4.1 Ability System Component

`AbilitySystemComponent` (`ASC`) は GAS の心臓部です。 これは `UActorComponent` ([`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)) であり、システムとのすべてのやり取りを処理します。 [`GameplayAbilities`](#concepts-ga) を使用したり、 [`Attributes`](#concepts-a) を所持したり、 [`GameplayEffects`](#concepts-ge) を受信したりすることを希望する `Actor` には、その `Actor` に付加された `ASC` が必要です。 これらのオブジェクトはすべて `ASC` の内部に存在し、管理され、レプリケーションされます（但し `Attributes` のレプリケーションは例外で、 [`AttributeSet`](#concepts-as) によってなされます）。 開発者は期待されてはいますが、サブクラス化は必須ではありません。

`ASC` が付加された `Actor` は、 `ASC` の `OwnerActor` と呼ばれます。 `ASC` の物理表現 `Actor` は、 `AvatarActor` と呼ばれます。 MOBA ゲームのミニオンのようなシンプルな AI のようなケースでは、`OwnerActor` と `AvatarActor` を同じ `Actor` にすることができます。 また、 MOBA ゲームのプレイヤーが操作するヒーローのようなケースでは、`OwnerActor` が `PlayerState` で、`AvatarActor` がヒーローの `Character` クラスであるように、異なる `Actor` にすることもできます。 殆どの `Actors` は自身で `ASC` を持つでしょう。 （MOBA のヒーローのように）`Actor` がリスポーンし、スポーンの前後で `Attributes` や `GameplayEffects` の永続性が必要な場合、 `ASC` の理想的な場所は `PlayerState` となります。

**Note:** `ASC` が `PlayerState` にある場合、 `PlayerState` の `NetUpdateFrequency` の値を大きくする必要があります。 `PlayerState` でのこの値はデフォルトでは非常に小さく、 `Attributes` や `GameplayTags` の変更が行われるまでに遅延や知覚できるラグが発生する可能性があります。 必ず [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency) を有効にしてください。 Fortnite はこれを使用しています。 

`OwnerActor` と `AvatarActor` （異なる `Actors` の場合は両方とも）は、 `IAbilitySystemInterface` を実装する必要があります。 このインターフェイスは `ASC` へのポインタを返す `UAbilitySystemComponent* GetAbilitySystemComponent() const` 関数をオーバーライドする必要があります。 `ASC` は、このインターフェイス関数により探され、システムの内部で相互に作用します。 

`ASC` は現在有効な `GameplayEffects` を `FActiveGameplayEffectsContainer ActiveGameplayEffects` に保持します。

`ASC` は 付与された `Gameplay Abilities` を `FGameplayAbilitySpecContainer ActivatableAbilities` に保持します。 `ActivatableAbilities.Items` の反復処理を行う場合は、必ずループの上に `ABILITYLIST_SCOPE_LOCK();` を追加し、（アビリティの削除による）リストの変更が行われないようにロックしてください。 スコープ内のすべての `ABILITYLIST_SCOPE_LOCK();` は、 `AbilityScopeLockCount` をインクリメントし、スコープから外れるとデクリメントします。 `ABILITYLIST_SCOPE_LOCK();` のスコープ内で、アビリティを削除しようとしてはいけません。（アビリティのクリア関数は内部的に `AbilityScopeLockCount` をチェックして、リストがロックされている場合はアビリティの削除を防いでいます）

<a name="concepts-asc-rm"></a>

### 4.1.1 レプリケーションモード

`ASC` は `GameplayEffects` と `GameplayTags` と `GameplayCues` をレプリケーションするための３つの異なるモード（`Full` `Mixed` `Minimal`）を定義しています。 `Attributes` は `AttributeSet` によってレプリケーションされます。

| レプリケーションモード | いつ使うか                              | 説明                                                                                                                                                                    |
| ---------------------- | --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Full`                 | Single Player                           | すべての `GameplayEffect` はクライアントでレプリケーションされます。                                                                                                    |
| `Mixed`                | Multiplayer, player controlled `Actors` | `GameplayEffects` は Owning Client （所有クライアント）のみにレプリケーションされます。 全員にレプリケーションされるのは `GameplayTags` と `GameplayCues` のみです。    |
| `Minimal`              | Multiplayer, AI controlled `Actors`     | `GameplayEffects` は誰にもレプリケーションされません。 全員にレプリケーションされるのは `GameplayTags` と `GameplayCues` のみです。                                     |

**Note:** `Mixed` レプリケーションモードでは `OwnerActor` の `Owner` が `Controller` であることを想定しています。 `PlayerState` の `Owner` は `Controller` がデフォルトですが、 `Character` では違います。 `Mixed` レプリケーションモードを `OwnerActor` が `PlayerState` でない場合に使用する時は、 `OwnerActor` で `SetOwner()` を有効な `Controller` を指定して呼び出す必要があります。

4.24 以降、 `PossessedBy()` は `Pawn` の所有者を新しい `Controller` に設定するようになりました。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asc-setup"></a>

### 4.1.2 セットアップと初期化

`ASC` は通常、 `OwnerActor` のコンストラクタで構築され、明示的にレプリケーションさせるようにします。 **これは C++ で行う必要があります。**

```c++
AGDPlayerState::AGDPlayerState()
{
	// アビリティシステムコンポーネント を構築し、レプリケーションされるように明示的に設定します。
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

`ASC` は `OwnerActor` と `AvatarActor` を使用してサーバーとクライアントの両方で初期化する必要があります。 `Pawn` の `Controller` が設定（所有後）された後に初期化すべきです。 シングルプレイヤーゲームはサーバーパスについてのみ気をつけるする必要があります。

プレイヤーが制御するキャラクターで `ASC` が `Pawn` 上に存在する場合、通常は `Pawn` の `PossessedBy()` 関数内でサーバー側、`PlayerController` の `AcknowledgePossession()` 関数内でクライアント側の初期化をそれぞれ行います。

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode レプリケーションでは、ASC Owner の所有者に Controller を要求します。
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

プレイヤーが制御するキャラクターで `ASC` が `PlayerState` 上に存在する場合、通常は `Pawn` の `PossessedBy()` 関数内でサーバー側、`Pawn` の `OnRep_PlayerState()` 関数内でクライアント側の初期化をそれぞれ行います。そうすることで、クライアント上に `PlayerState` が存在することが保証されます。

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// サーバー側の ASC を設定します。クライアント側は OnRep_PlayerState() で行います。
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI は PlayerController を持っていないので、念の為にここで初期化をし直すことができます。 PlayerController を既に所持しているヒーローに対して二度目の初期化をしても害はありません。
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// クライアント側の ASC を設定します。サーバー側は PossessedBy() で行います。
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// クライアント側の ASC に Actor Info を初期化します。 サーバー側の ASC は新しい Actor を所有する際に初期化します。
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

もし、エラーメッセージ `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!` が表示された場合、クライアント側の `ASC` の初期化がされていません。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt"></a>

### 4.2 Gameplay Tags

[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html) は `Parent.Child.Grandchild...` のような階層的な名前であり、 `GameplayTagManager` に登録されています。 これらのタグは、オブジェクトの状態の分類及び説明に非常に便利です。 例えば、キャラクターがスタンした場合、スタンの持続中に `GameplayTag` で `State.Debuff.Stun` を付与することができます。

ブーリアンや列挙型を利用していた処理を `GameplayTags` に置き換えられ、オブジェクトが特定の `GameplayTags` が付与されているか論理演算ができることに気づくでしょう。

タグをオブジェクトに付与する際は、通常、対象のオブジェクトが `ASC` を所持していればそれに付与し、 GAS と相互作用できるようにします。 `UAbilitySystemComponent` は `IGameplayTagAssetInterface` を実装することで、所持している `GameplayTags` へのアクセス関数を提供します。

複数の `GameplayTags` は `FGameplayTagContainer` に格納できます。 `GameplayTagContainers` は効率化の魔法が施されているため、 `TArray<FGameplayTag>` よりも `GameplayTagContainer` を使うほうが望ましいです。 タグは標準の `FNames` ですが、プロジェクト設定で `Fast Replication` を有効にしている場合、レプリケーションに効率的な `FGameplayTagContainers` にまとめておくことができます。 `Fast Replication` は サーバーとクライアントが同じ `GameplayTags` のリストを所持していることが求められます。 これは通常問題にはならないはずなので、このオプションを有効にすべきです。 また、 `GameplayTagContainers` はイテレーション処理用に `TArray<FGameplayTag>` を返すこともできます。

`FGameplayTagCountContainer` に格納されている `GameplayTags` は `GameplayTag` のインスタンス数を格納する `TagMap` を所持しています。 `FGameplayTagCountContainer` にはまだ `GameplayTag` が含まれているにもかかわらず `TagMapCount` はゼロかもしれません。 デバッグ中、 `ASC` にまだ `GameplayTag` が残っていると、この現象に遭遇する可能性があります。 `HasTag()` や `HasMatchingTag()` やその他同様の関数は、いずれも `TagMapCount` をチェックし、 `GameplayTag` が存在しない、または `TagMapCount` がゼロの場合は false を返します。

> Translators notes:SentyaAnko  
> このあたり、 UE4 のソース内で該当のコードを見つけることができません。  
> 翻訳が正しくないか、原文がなにか古い情報をもとにしている可能性があります。  
> 具体的には、FGameplayTagCountContainer や FGameplayTags に TagMap や TagMapCount という変数や関数がありません。  
>  
> 以下のように置き換えると正しいように見受けられます。  
> TagMap -> HasMatchingGameplayTag  
> TagMapCount -> GetTagCount()  
> HasTag() -> 該当なし  
> HasMatchingTag() -> HasMatchingGameplayTag()  

`GameplayTags` は `DefaultGameplayTags.ini` にて事前に定義する必要があります。 UE4 エディターはプロジェクト設定にインターフェイスを提供しており、開発者は `DefaultGameplayTags.ini` を手動編集せずに `GameplayTags` を管理できます。 `GameplayTag` エディターは `GameplayTags` の作成、名前変更、参照の検索、及び削除を行うことができます。

![GameplayTag Editor in Project Settings](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

`GameplayTag` の参照検索をすると、エディターでおなじみの `Reference Viewer` グラフが表示され、 `GameplayTag` を参照するすべてのアセットが表示されます。 ただし、そこには `GameplayTag` を参照している C++ クラスは表示されません。

`GameplayTags` の名前変更を行うと、リダイレクタが作成され、元の `GameplayTag` を参照しているアセットが新しい `GameplayTag` にリダイレクトされるようになります。 可能であれば、新しい `GameplayTags` を作成し、すべての参照を新しい `GameplayTags` に手動で変更した後に、古い `GameplayTags` を削除することで、リダイレクタが生成されないようにすることをおすすめします。

`Fast Replication` に加えて、 `GameplayTag` エディターにはよくレプリケーションされる `GameplayTags` を埋める、さらなる最適化オプションがあります。

> Translators notes:SentyaAnko  
> 「よくレプリケーションされる `GameplayTags` を埋める、さらなる最適化オプション」の詳細不明、要確認  

`GameplayTags` は `GameplayEffect` から追加された場合にレプリケーションされます。 `ASC` はレプリケーションされず、手動管理が必要な `LooseGameplayTags` を追加可能にしています。 サンプルプロジェクトでは `State.Dead` に `LooseGameplayTag` を使用することで、 Owning Client （所有クライアント）のヘルスがゼロになった際にすぐ応答できるようにしています。 リスポーンは `TagMapCount` を手動でゼロに戻しています。 `LooseGameplayTags` を操作している場合のみ、 `TagMapCount` の調整を手動で行ってください。 `UAbilitySystemComponent::AddLooseGameplayTag()` と `UAbilitySystemComponent::RemoveLooseGameplayTag()` の関数を使用するほうが、手動で `TagMapCount` を調整するよりも望ましいです。

C++ での `GameplayTag` の参照の取得 :
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

高度な `GameplayTag` の操作、例えば親や子の `GameplayTags` を取得するなどは、 `GameplayTagManager` によって提供される関数を参照してください。 `GameplayTagManager` にアクセスするには、 `GameplayTagManager.h` をインクルードし、 `UGameplayTagManager::Get().FunctionName` を呼び出します。 `GameplayTagManager` は、実際には `GameplayTags` をリレーショナルノード（親や子など）として格納しており、固定文字列の操作や比較よりも高速に処理を行えます。

`GameplayTags` と `GameplayTagContainers` はオプションの `UPROPERTY` 指定子 `Meta = (Categories = "GameplayCue")` を持っており、これはブループリントにて `GameplayCue` の親タグを持つ `GameplayTags` のみ表示するようにフィルタリングします。 これは、 `GameplayTag` または `GameplayTagContainer` の変数が `GameplayCues` にのみ使用されるべきとわかっているときに役に立ちます。

代わりに、 `FGameplayCueTag` という別の構造体があり、こちらは `FGameplayTag` をカプセル化し、ブループリント内の `GameplayTags` を自動的にフィルタリングし、 `GameplayCue` の親タグを持つタグのみ表示します。

関数の `GameplayTag` パラメータをフィルタリングしたい場合は、 `UFUNCTION` 指定子 `Meta = (GameplayTagFilter = "GameplayCue")` を使用します。 関数の `GameplayTagContainer` パラメータはフィルタリングできません。 これを可能にするためにエンジンを編集したい場合は、 `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp` の `SGameplayTagGraphPin::ParseDefaultValueData()` が `FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);` を呼び出し、そして `FilterString` を `SGameplayTagGraphPin::GetListContent()` で `SGameplayTagWidget` に渡す方法を確認してください。 `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp` にある `GameplayTagContainer` 版のこれらの関数はメタフィールドのプロパティをチェックせずにフィルターを渡します。

サンプルプロジェクトでは `GameplayTags` を多用しています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-change"></a>

### 4.2.1 Gameplay Tags の変更への対応

`ASC` は `GameplayTags` が追加または削除されたときのデリゲートを提供します。 デリゲートは `EGameplayTagEventType` を受け取ります。 `EGameplayTagEventType` は 「 `GameplayTag` が新規に追加/完全に削除され時のみ」もしくは「 `GameplayTag` の `TagMapCount` が変化した時」に起動するように指定できます。

> Translators notes:SentyaAnko  
> ドキュメントページへのリンク [GameplayTags](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/EGameplayTagEventType__Type/index.html)  

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

コールバック関数はパラメータとして `GameplayTag` と更新後の `TagCount` を受け取ります。
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a"></a>

### 4.3 Attributes


<a name="concepts-a-definition"></a>

#### 4.3.1 Attribute の定義

`Attributes` は 構造体 [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html) によって定義された float の値です。 これらは、キャラクターのヘルスの量からキャラクターのレベル、ポーションのチャージ数など、何でも表現できます。 もしその値が `Actor` に属するゲームプレイ関連の数値であれば、 `Attribute` の使用を検討するべきです。 `Attributes` は通常、 ASC が変更を [predict （予測）](#concepts-p) できるように [`GameplayEffects`](#concepts-ge) によってのみ変更されるべきです。

`Attributes` は [`AttributeSet`](#concepts-as) によって定義され、またその中に存在します。 `AttributeSet` はレプリケーションのマークがつけられた `Attributes` をレプリケーションする責任があります。 `Attributes` の定義方法については、 [`AttributeSets`](#concepts-as) のセクションを参照してください。

**Tip:** もし `Attribute` をエディタの `Attributes` リストに表示させたくない場合には `UPROPERTY` 指定子 `Meta = (HideInDetailsView)` が使えます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-value"></a>

#### 4.3.2 BaseValue vs CurrentValue

`Attribute` は `BaseValue` と `CurrentValue` の２つの値で構成されます。 `BaseValue` は `Attribute` の永続的な値で、 `CurrentValue` は `BaseValue` に `GameplayEffects` による一時的な変更を加えた値です。 たとえば、 `Character` の `Attribute` 移動速度の `BaseValue` が 600 units/second だとします。 まだ移動速度を変更する `GameplayEffects` が無いため、 `CurrentValue` も 600 u/s のままです。 もし `Character` が一時的に 50 u/s の移動速度バフを取得した場合、 `BaseValue` は 600 u/s のままですが、 `CurrentValue` は 600 + 50 となり、合計で 650 u/s となります。 移動速度バフの期限が切れると、 `CurrentValue` は `BaseValue` である 600 u/s に戻ります。

GAS の初心者はしばしば `BaseValue` を `Attribute` の最大値と混同し、そのように扱おうとします。 これは間違ったアプローチです。 変更されたり、アビリティやUIから参照されたりする `Attributes` の最大値は、個別の `Attributes` として扱われるべきです。 ハードコードされた最大値と最小値のために、最大値と最小値を設定できる `FAttributeMetaData` を持つ `DataTable` を定義する方法がありますが、構造体の上（の行）で Epic によって「 work in progress 」とコメントされています。 詳細については、 `AttributeSet.h`を参照してください。 混乱を避けるために、 「アビリティやUIから参照できる最大値」は「 `Attributes` を個別」で作成し、「`Attributes` のクランプのためだけに使われるハードコードされた最大値と最小値」は「`AttributeSet` の中でハードコードされた float 値として定義する」のをおすすめします。 `Attributes` のクランプについて、 `CurrentValue` の変更については [PreAttributeChange()](#concepts-as-preattributechange) にて、`GameplayEffects` による `BaseValue` の変更については [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute) にて論じています。

`BaseValue` への恒久的な変更は `Instant` な `GameplayEffects` からもたらされ、 `Duration` または `Infinite` な `GameplayEffects` は `CurrentValue` を変更します。
定期的な `GameplayEffects` は インスタントな `GameplayEffects` と同じ様に扱われ、 `BaseValue` を変更します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-meta"></a>

#### 4.3.3 Meta Attributes

いくつかの `Attributes` は `Attributes` との相互作用を目的とした一時的な値のプレースホルダーとして扱われます。 それらは `Meta Attributes` と呼ばれます。 たとえば、私達は通常、ダメージを `Meta Attribute` として定義します。 ヘルス `Attribute` を直接変更する `GameplayEffect` の代わりに、ダメージと呼ばれる `Meta Attribute` をプレースホルダーとして使用します。 このようにすることで、ダメージの値は [`GameplayEffectExecutionCalculation`](#concepts-ge-ec) によるバフやデバフによる変更が可能となり、 さらなる操作が `AttributeSet` で可能となります。 たとえば、現在のシールド `Attribute` の値からダメージの値を減算し、最終的に残った値をヘルス `Attribute` から差し引く、です。 ダメージ `Meta Attribute` は `GameplayEffects` の間で永続性がなく、全てによって上書きされます。 `Meta Attributes` は通常、レプリケーションされません。

`Meta Attributes` はたとえば「ダメージとヒーリングの間で「どのぐらいのダメージを与えたのか」と「このダメージで何をするのか」」といったことの優れた論理的な分離を提供します。 この論理的な分離は `Gameplay Effects` と `Execution Calculations` はどの様にターゲットがダメージを処理するかを知る必要がないことを意味します。 ダメージの例を続けると、 `Gameplay Effect` がダメージ量を決定し、その後 `AttributeSet` はそのダメージ量で何をするかを決めます。 すべてのキャラクターが同じアトリビュートを持つとは限りません。サブクラス化された `AttributeSets` を使用する場合は特にです。 基底となる `AttributeSet` クラスがヘルス `Attribute` しか持たないかもしれませんし、サブクラス化された `AttributeSet` では シールド `Attribute` が追加されているかもしれません。
シールド `Attribute` を持つサブクラス化された `AttributeSet` は、受けたダメージを、基底となる `AttributeSet` とは異なる方法で割り当てます。

`Meta Attributes` は優れたデザインパターンではありますが、必須ではありません。
もしあなたが唯一の `Execution Calculation` をすべてのインスタンスのダメージのために用いていて、かつ、唯一の `Attribute Set` クラスをすべてのキャラクターで共有しているのであれば、`Execution Calculation` の中でダメージをヘルス、シールド、その他に割り当て、`Attributes` を直接変更しても良いでしょう。 柔軟性は犠牲になりますが、あなたにとっては問題ないかもしれません。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-changes"></a>

#### 4.3.4 Attribute の変更への対応

`Attribute` が変更された際、 UI やその他のゲームプレイを更新するためにリッスンするためには、 `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)` を使います。 この関数は `Attribute` が変更された際にいつでも自動的に呼び出されるデリゲートを返し、バインドできます。 このデリゲートはパラメータ `FOnAttributeChangeData` を（`NewValue` と `OldValue` と `FGameplayEffectModCallbackData` を伴って）提供します。 **Note:** `FGameplayEffectModCallbackData` はサーバー上のみで設定されます。

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

サンプルプロジェクトでは `GDPlayerState` 上の `Attribute` の値変更通知デリゲートに、「 HUD の更新」及び「ヘルスがゼロになった際にプレイヤーの死亡の応答」の処理をバインドしています。

これを `ASyncTask` の中にラップするカスタムブループリントノードがサンプルプロジェクトに含まれています。
これはヘルス、マナ、スタミナそれぞれの値の更新のために、 `UI_HUD` UMG ウィジェットで使われています。
この `AsyncTask` は UMG ウィジェットの `Destruct` イベントで行っている、 `EndTask()` を手動で呼び出すまで永続的に存続します。

詳細については、 `AsyncTaskAttributeChanged.h/cpp` を参照してください。

![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-derived"></a>

#### 4.3.5 Derived Attributes （派生アトリビュート）

値の一部または全部が、１つ以上の他の `Attributes` から派生した `Attribute` を作成するには `Infinite` `GameplayEffect` で１つ以上の `Attribute Based` または [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods) を使います。 `Derived Attribute （派生アトリビュート）` は、依存している `Attribute` が更新されると、自動的に更新されます。 

`Derived Attribute （派生アトリビュート）` 上のすべての `Modifiers` の最終的な計算式は、 `Modifier Aggregators` の計算式と同じです。 もし、特定の順序で計算を行う必要がある場合は、 `MMC` の中ですべてを行う必要があります。

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**Note:** もし、 PIE でマルチクライアントでプレイするならば、エディタ設定で `Run Under One Process（１つのプロセス下で実行）` を無効にする必要があります。 さもないと、最初のクライアント以外で独立した `Attributes` が更新された際に `Derived Attributes （派生アトリビュート）` が更新されなくなります。

この例としては、 `TestAttrA` という値を `Attributes` と `TestAttrB` と `TestAttrC` から `TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)` という式で導出する、 `Infinite` `GameplayEffect` があります。 `TestAttrA` の値はいずれかの `Attributes` が更新されるたびに自動的に再計算されます。

![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as"></a>

### 4.4 Attribute Set

<a name="concepts-as-definition"></a>

#### 4.4.1 Attribute Set 定義

`AttributeSet` は `Attributes` の定義、保持、変更の管理をします。 開発者は [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html) をサブクラス化する必要があります。 `OwnerActor` のコンストラクタ内で `AttributeSet` を構築すると自動的に `ASC` に登録されます。 **これは C++ で行う必要があります。**

<a name="concepts-as-design"></a>

#### 4.4.2 Attribute Set の設計

１つの`ASC` には１つ以上の `AttributeSets` を持つことがあります。 `AttributeSets` のメモリオーバーヘッドは無視できる程度のため、使用する `AttributeSets` の数は開発者に委ねるよう組織的意思決定しています。

**[⬆ Back to Top](#table-of-contents)**

１つの巨大で画一的な `AttributeSet` をすべてのゲーム内の `Actor` で共有し、必要なアトリビュートのみを使用し、使用しないアトリビュートを無視することを許容されています。

あるいは、 `Actors` に必要な「 `Attributes` の分類を示す複数の１つ以上の `AttributeSet` 」を選択的に追加する、ということを選ぶこともできます。 たとえば、「ヘルス `Attributes` に関連した `AttributeSet` 」、「マナ `Attributes` に関連した `AttributeSet` 」などをを持つことができます。 MOBA ゲームにおいては、ヒーローはマナを必要としますが、ミニオンには必要ないかもしれません。 そのため、ヒーローはマナ `AttributeSet` が必要であろうし、ミニオンはそうではないということになります。

さらに、 `Actor` がどの `Attributes` を持つかを選択的に選ぶ別の手段として `AttributeSets` をサブクラス化できます。 `Attributes` は内部的に `AttributeSetClassName.AttributeName` として参照されます。 `AttributeSet` をサブクラス化する時、親クラスのすべての `Attributes` は親クラス名が接頭辞に残ります。

１つ以上の `AttributeSet` を所持できますが、１つ以上の `AttributeSet` を同じクラスの `ASC` に持つべきではありません。 もし同一クラスが１つ以上の `AttributeSet` を持つ場合、どの `AttributeSet` を使うべきかわからなくなり、１つだけ選ばれます。

<a name="concepts-as-design-subcomponents"></a>

##### 4.4.2.1 個々の Attributes を持つサブコンポーネント

個々にダメージを与えられるアーマー部位のように、「 `Pawn` に複数のダメージを受け付けるコンポーネントを持つ」というシナリオでは、おすすめするのは、（もし「 `Pawn` が持ちうるダメージを受け付けるコンポーネントの最大数が」わかっているのであれば、）１つの `AttributeSet` に（ DamageableCompHealth0 DamageableCompHealth1 など）複数のヘルス `Attributes` を作成し、ダメージを受け付けるコンポーネントのための論理的な「slots」として表すようにすることです。 ダメージを受け付けるコンポーネントのインスタンスにて、どの `Attribute` にダメージを反映すべきかを知るために `GameplayAbilities` または [`Executions`](#concepts-ge-ec) から読めるスロット番号 `Attribute` を割り当てます。 ダメージを受け付けるコンポーネントが最大数未満もしくはゼロの `Pawns` でも問題ありません。 `AttributeSet` に `Attribute` があるからといって、使わなければならない、という意味ではないからです。 未使用の `Attributes` は僅かな量のメモリーを消費するだけです。

もしあなたのサブコンポーネントがそれぞれに多くの `Attributes` を必要としたり、潜在的にサブコンポーネントの数が際限のない可能性があったり、サブコンポーネントが取り外し可能で他のプレイヤーによって利用可能（例：武器）であったり、またはその他の理由でこのアプローチがあなたにとって機能しない場合、おすすめするのは `Attributes` から切り離して、代わりに昔ながらの float 値をコンポーネントに保存することです。 詳細については、 [アイテム Attributes (武器弾薬)](#concepts-as-design-itemattributes) を参照してください。

<a name="concepts-as-design-addremoveruntime"></a>

##### 4.4.2.2 実行時の AttributeSets の追加と削除

`AttributeSets` は実行時に `ASC` から追加と削除が可能です。 ただし、 `AttributeSets` の削除は危険な場合があります。 たとえば、もし、 `AttributeSet` がサーバー側の前にクライアント側で削除され、 `Attribute` の値が変更がクライアントにレプリケーションされた場合、 `AttributeSet` に `Attribute` が見つからず、その結果ゲームをクラッシュさせます。


インベントリに武器を追加 :
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

インベントリから武器を削除 :
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

<a name="concepts-as-design-itemattributes"></a>

##### 4.4.2.3 アイテム Attributes (武器弾薬)

`Attributes` （武器弾薬、アーマーの耐久性など）を持つ装備可能なアイテムを実装するにはいくつかの方法があります。これらのアプローチはすべて、値の保存をアイテムに直接行います。これは、存続期間中に一人以上のプレイヤーに装備されることができるアイテム群に必要となります。

> 1. アイテムに素の float 値を用いる (**推奨**)
> 1. アイテムの `AttributeSet` を分離する
> 1. アイテムの `ASC` を分離する

<a name="concepts-as-design-itemattributes-plainfloats"></a>

###### 4.4.2.3.1 アイテム毎の単純な float 値

`Attributes` の代わりに、素の float 値をアイテムクラスのインスタンスに保存します。 Fortnite と [GASShooter](https://github.com/tranek/GASShooter) は銃の弾薬をこの方法で処理します。 銃の場合、最大挿弾子（弾倉）サイズ、現在の挿弾子（弾倉）内の弾薬数、予備弾薬などを、銃のインスタンスにレプリケーションされた float 値（ `COND_OwnerOnly` ）として直接保存します。 もし武器が予備弾薬を共有する場合、予備弾薬をキャラクターの共有弾薬 `AttributeSet` の `Attribute` に移動させます （リロードアビリティは `Cost GE` を使用して予備弾薬から 銃の float 値の挿弾子（弾倉）弾薬に引き込むことが可能です）。 現在の挿弾子（弾倉）弾薬には `Attributes` を使用していないため、 `UGameplayAbility` でいくつかの関数をオーバーライドして銃の float 値に対してコストの確認と適用をする必要があります。 アビリティを付与する際に [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec)の `SourceObject` を銃にするということはアビリティを付与した銃にアビリティの中でアクセスできるということを意味します。

自動発砲の間、銃が弾薬量をレプリケーションすることでローカルの弾薬量を壊すのを防ぐには、プレイヤーが `PreReplication()` で `IsFiring` `GameplayTag` を持つ間はレプリケーションを無効にします。 ここでは、本質的に独自の local prediction （ローカル予測）を行っていることになります。

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

利点 :
1. `AttributeSets` を使用することによる制限の回避（下記を参照）

制限 :
1. `GameplayEffect` ワークフローが利用できない （弾薬使用のための `Cost GEs` など）
1. 銃の float 値に対してコストの確認と適用するために、 `UGameplayAbility` の主要関数をオーバーライドする作業が必要

<a name="concepts-as-design-itemattributes-attributeset"></a>

###### 4.4.2.3.2 アイテム毎の `AttributeSet`

[プレイヤーのインベントリに追加する際にプレイヤーの `ASC` に追加されるように](#concepts-as-design-addremoveruntime) アイテムに個別の `AttributeSet` を使うことは機能しますが、いくつかの大きな制限があります。 [GASShooter](https://github.com/tranek/GASShooter) の初期のバージョンでは、この機能を武器弾薬のために使用していました。 武器は、最大挿弾子（弾倉）サイズ、現在の挿弾子（弾倉）内の弾薬数、予備弾薬などの `Attributes` を武器クラスに存在する `AttributeSet` に保存します。 もし武器が予備弾薬を共有する場合、予備弾薬をキャラクターの共有弾薬 `Attribute` に移動させます サーバー上で武器がプレイヤーのインベントリに追加されると、武器の `AttributeSet` はプレイヤーの `ASC::SpawnedAttributes` に追加されます。 サーバーはその後、これらをクライアントにレプリケーションします。 武器がインベントリから削除される際には、 `ASC::SpawnedAttributes` からその武器の `AttributeSet` が削除されます。

`AttributeSet` が `OwnerActor` 以外のもの（武器など）に存在する場合、最初は `AttributeSet` でいくつかのコンパイルエラーが発生するでしょう。 これらの修正は、`AttributeSet` の構築をコンストラクタではなく `BeginPlay()` で行い、武器に `IAbilitySystemInterface` を実装する（武器をプレイヤーのインベントリに追加する時に `ASC` のポインタを設定する）ことです。

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

この [GASShooter の古いバージョン](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735) をチェックすると実際にそれを見ることができます。

利点 :
1. 既存の `GameplayAbility` と `GameplayEffect` ワークフローを利用可能 （弾薬使用のための `Cost GEs` など）
1. 非常に小さなアイテムのセットアップが容易

制限 :
1. すべての武器の種類毎に新しい `AttributeSet` を作成する必要があります。 `Attribute` の変更により `ASCs` の `SpawnedAttributes` 配列内で `AttributeSet` クラスの最初のインスタンスが検索されるため、`ASCs` は機能的に、クラスの `AttributeSet` インスタンスを１つしか持つことができません。
1. 同じ `AttributeSet` クラスのインスタンスの追加は無視されます。 `AttributeSet` クラス毎に `AttributeSet` のインスタンスは１つ、という前述の理由により、プレイヤーインベントリには同じ型の武器を１つしか含めることができません。
1. `AttributeSet` の削除は危険です。 GASShooter では、プレイヤーがロケットで自殺した場合、プレイヤーは直ちにロケットランチャーをインベントリから削除します（`ASC` からの `AttributeSet` を含みます）。 サーバー側でロケットランチャーの弾薬 `Attribute` の変更がレプリケーションされた時には、クライアント側の `ASC` には `AttributeSet` はもはや存在しておらず、ゲームがクラッシュします。

<a name="concepts-as-design-itemattributes-asc"></a>

###### 4.4.2.3.3 アイテム毎の `ASC`

各アイテムにまるごと `AbilitySystemComponent` を配置することは極端なアプローチです。 個人的には見たこともやったこともありません。 実践するには膨大なエンジニアリングが必要となります。

> 所有者が同じでアバターが異なる、別々の AbilitySystemComponents を持つことは可能か（たとえば、所有者に PlayerState が設定されているポーンと武器/アイテム/投射物）？
> 
> 最初に目にする問題は所有するアクターに IGameplayTagAssetInterface と IAbilitySystemInterface の実装することです。
前者は可能かもしれません : すべての ASCs からタグを集約するだけです（ただし、要注意 -HasAllMatchingGameplayTags は ASC 間の集約によってのみ合致します。 その呼び出しを各 ASC に転送し結果をまとめて OR するだけでは不十分です）。しかし、後者はさらにトリッキーです : どの ASC が 権限を持っているのでしょうか？ もしどれかが GE の適用を望んだとして、どれが受け取るべきでしょうか？ おそらくあなたは解決自体は可能でしょうがこちらの側面の問題は最も困難でしょう : 所有者はそれらの下に複数の ASCs を持ちます。
> 
> ポーンと武器の ASCs の分離は、それ自体でも意義があります。 たとえば、武器を説明するタグと所有するポーンの説明するタグを区別することです。 武器に付与されたタグを所有者にも適用し、他に何もしないということは、おそらく理にかなっています（たとえば、アトリビュートと GEs は独立していますが、所有者は前述のように所有タグを集約します）。 これ自体はうまくいくと、私は確信しています。 ですが、複数の ASCs を同じ所有者が持つことは厄介事を招くかもしれません。

*[コミュニティの質問＃6]（https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89）に対する Epic の Dave Ratti による回答*

利点 :
1. 既存の `GameplayAbility` と `GameplayEffect` ワークフローを利用可能 （弾薬使用のための `Cost GEs` など）
1. `AttributeSet` クラスの再利用 （各武器のASCに１つ）

制限 :
1. 見通しが立たないエンジニアリングコスト
1. それでもやれますか？

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-attributes"></a>

#### 4.4.3 Attributes の定義

**`Attributes` は C++ でのみ定義でき、** `AttributeSet ` のヘッダーファイルで行います。 このブロックのマクロをすべての `AttributeSet` ヘッダーファイルの先頭に追加するのをおすすめします。 これはあなたの `Attributes`のゲッターとセッターを自動的に生成します。


```c++
// AttributeSet.h のマクロを使用します。
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```


レプリケーションされたヘルスアトリビュートは次のように定義されます :

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

また、ヘッダーで `OnRep` 関数を定義します :

```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

`AttributeSet` の .cpp ファイルでは `OnRep` 関数で prediction（予測）システムで使用される `GAMEPLAYATTRIBUTE_REPNOTIFY` マクロを記述する必要があります。

```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

最後に、 `Attribute` を `GetLifetimeReplicatedProps` に追加する必要があります :

```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always` は `OnRep` 関数に対し、ローカルの値が既にサーバーから（ prediction （予測）により）報告されている値と等しい場合に発動するように指示します。 デフォルトでは、`OnRep` 関数は、ローカルの値がサーバーから報告されている値と等しい場合には発動されません。

もし `Attribute` が `Meta Attribute`のようにレプリケーションされない場合、 `OnRep` と `GetLifetimeReplicatedProps` の手順は省略できます。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-init"></a>

#### 4.4.4 Attributes の初期化

`Attributes` の初期化する方法は複数あります（`BaseValue` を設定し、その結果として `CurrentValue` に何らかの初期値を設定します）。 Epic はインスタント `GameplayEffect` を使うことをすすめています。 これはサンプルプロジェクトでも同じ様に使用されている方法です。

どの様に `Attributes` の初期化のためのインスタント `GameplayEffect` を作成しているのか、サンプルプロジェクトにある `GE_HeroAttributes` ブループリントを確認してください。 この `GameplayEffect` の適用は C++ で行われます。

`Attributes` を定義する際に `ATTRIBUTE_ACCESSORS` マクロを使用すると、各 `Attribute` の初期化関数が `AttributeSet` に自動的に生成され、 C++ で自由に呼び出すことができます。

```c++
// InitHealth(float InitialValue) はヘルスアトリビュートのために自動生成された関数で、 `ATTRIBUTE_ACCESSORS` マクロにより定義されています。
AttributeSet->InitHealth(100.0f);
```

`Attributes` を初期化するその他の方法については、 `AttributeSet.h` を参照してください。

**Note:** 4.24 より前では、 `FAttributeSetInitterDiscreteLevels` は `FGameplayAttributeData` では機能しませんでした。 `Attributes` が素の float 値であった時に作られ、 `FGameplayAttributeData` が `Plain Old Data` (`POD`) ではないと訴えていました。 これは [4.24](https://issues.unrealengine.com/issue/UE-76557) で修正されています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>

#### 4.4.5 PreAttributeChange()

`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)` は `AttributeSet` の変更に応答するための主要な関数の１つで、`Attribute` の `CurrentValue` 変更が起こる前に発生します。 ここは入力された `CurrentValue` への変更を参照パラメータ `NewValue` を介してクランプするのに理想的な場所です。

たとえば、サンプルプロジェクトにて 移動速度 modifiers のクランプは以下のように行います :

```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// 150 units/s 未満の速度低下及び 1000 units/s を超えるブーストはできません
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```

`GetMoveSpeedAttribute()` 関数は `AttributeSet.h` ([Attributes の定義](#concepts-as-attributes)) に追加したマクロブロックにより作られています。

これは、 `Attribute` のセッター（`AttributeSet.h` ([Attributes の定義](#concepts-as-attributes)) のマクロブロックにより定義される）または [`GameplayEffects`](#concepts-ge) 、いずれを使ったかに関わらず、 `Attributes` が変更された際に発動されます。

**Note:** ここで発生するどのようなクランプも `ASC` の modifier が永続的に変更されることはありません。 modifier のクエリから返される値のみを変更します。 これは [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) や [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) のようなすべての modifiers からの `CurrentValue` の再計算の際にはクランプの実装が再び必要ということを意味します。

**Note:** Epic の `PreAttributeChange()` に対するコメントとして、ゲームプレイイベントのために使うのではなく、代わりに主にクランプに使う、と述べています。 `Attribute` の変更に関するゲームプレイイベントのためのおすすめの場所は `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)` ([Attribute の変更への対応](#concepts-a-changes)) です。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>

#### 4.4.6 PostGameplayEffectExecute()

`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)` は `Attribute` の `BaseValue` が インスタント [`GameplayEffect`](#concepts-ge) により変更された後にだけ呼び出されます。 ここは `GameplayEffect` により変更された際に `Attribute` を更に操作するのに有効な場所です。

たとえばサンプルプロジェクトでは、ここでヘルス `Attribute` から最終的なダメージ `Meta Attribute` を差し引きます。 もしシールド `Attribute` があれば、まずそこからダメージを差し引き、残りをヘルスから引きます。 サンプルプロジェクトはまたこの場所を、ヒットリアクションアニメーションの適用、ダメージ数値の吹き出し表示、キラーへの経験値と報奨金の割り当てに使用しています。 設計上、ダメージ `Meta Attribute` は常にインスタント `GameplayEffect` から発生し、`Attribute` セッターからは発生しません。

マナやスタミナのようにインスタント `GameplayEffects` から `BaseValue` が変更されるだけのその他の `Attributes` も、ここで、それらに対応する最大値 `Attributes` でクランプすることができます。

**Note:** `PostGameplayEffectExecute()` が呼び出された時、 `Attribute` の変更は既に行われていますが、クライアントにレプリケーションされていないため、ここでの値のクランプはクライアントに２つのネットワーク更新を発生させません。 クライアントはクランプ後の更新のみ受け取ります。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>

#### 4.4.7 OnAttributeAggregatorCreated()

`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)` はこのセットの `Attribute` のための `Aggregator` が作られた際に発動されます。 これにより [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html) のカスタムセットアップが可能になります。 `AggregatorEvaluateMetaData` は、適用されたすべての [`Modifiers`](#concepts-ge-mods) に基づいて、`Attribute` の `CurrentValue` を評価する際に `Aggregator` によって使用されます。 デフォルトでは、 `AggregatorEvaluateMetaData` は `Aggregator` だけに（どの `Modifiers` を適用するか判断する目的で）使われます。 例として`MostNegativeMod_AllPositiveMods` では、すべての正の `Modifiers` を許可し、負の `Modifiers` を最も負の効果が強いもののみに制限します。 これは Paragon により使用されたもので、正の移動速度バフが適用されている間は、スローエフェクトの数に関係なく、最も負の効果が強い移動速度のスローエフェクトのみをプレイヤーに適用できるようにしたものです。 `ASC` に依然として存在する適用していない `Modifiers` は、最終的な `CurrentValue` へ集約されないだけです。 それらは後にひとたび条件が変化した際には適用する可能性を持っています。 たとえば最も負の効果が強い `Modifier` が期限切れとなったのであれあば、次に負の効果が強い `Modifier` を（存在すれば）適用することとなります。

最も負の効果が強い唯一の `Modifiers` とすべての正の `Modifiers` を許可するという例で AggregatorEvaluateMetaData を使用 :

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

適用するためのカスタムした `AggregatorEvaluateMetaData` は `FAggregatorEvaluateMetaDataLibrary` に静的変数として追加する必要があります。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge"></a>

### 4.5 Gameplay Effects

<a name="concepts-ge-definition"></a>

#### 4.5.1 Gameplay Effect の定義

[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) (`GE`) はアビリティが自身や他人の [`Attributes`](#concepts-a) や [`GameplayTags`](#concepts-gt) を変化させるための器です。 それらは即時的な `Attribute` の変化（たとえばダメージや回復）を引き起こしたり、長期的なステータスのバフ/デバフ（たとえば移動速度のブーストやスタン）を適用したりすることができます。 `UGameplayEffect` クラスは単一のゲームプレイエフェクトを定義するための **data-only** クラスとなるよう意図されています。 どんな追加ロジックも `GameplayEffects` に加えるべきではありません。 通常、設計者は多くの `UGameplayEffect` のブループリント子クラスを作成します。

`GameplayEffects` は `Attributes` の変更を [`Modifiers`](#concepts-ge-mods) と [`Executions` (`GameplayEffectExecutionCalculation`)](#concepts-ge-ec) を介して行います。

`GameplayEffects` は3種類の duration （期間）があります。 : `Instant` と `Duration` と `Infinite` です。

更に、`GameplayEffects` は  [`GameplayCues`](#concepts-gc) の追加/実行が可能です。 `Instant` の `GameplayEffect` は `GameplayCue` `GameplayTags` の `Execute` を呼び出すのに対し、 `Duration` または `Infinite` の `GameplayEffect` は `GameplayCue` `GameplayTags` の `Add` と `Remove` を呼び出します。

| Duration Type | GameplayCue Event | いつ使用すべきか                                                                                                                                                                                                                            |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`     | Execute           | `Attribute` の `BaseValue` への即時永続変化をしたい場合。 `GameplayTags` は1フレームでさえも適用されません。                                                                                                                                |
| `Duration`    | Add & Remove      | `Attribute` の `CurrentValue` への一時変化と `GameplayTags` の適用（ `GameplayEffect` が期限切れ、もしくは手動で削除された時に削除される）をしたい場合。期限は `UGameplayEffect` クラス/ブループリントで指定されます。                      |
| `Infinite`    | Add & Remove      | `Attribute` の `CurrentValue` への一時変化と `GameplayTags` の適用（ `GameplayEffect` が削除された時に削除される）をしたい場合。 これらはこれら自身では期限切れにはならず、 アビリティもしくは `ASC` によって手動で削除する必要があります。 |

`Duration` と `Infinite` の `GameplayEffects` は `Periodic Effects` を適用するオプションがあり、これは `Modifiers` と `Executions` を `X` 秒毎に適用することができ、 `Period` で定義されます。 `Periodic Effects` は（ `Attribute` の `BaseValue` と `Executing` `GameplayCues` を変更する際は） `Instant` `GameplayEffects` として扱われます。 これらはダメージオーバータイム（DOT）（継続ダメージ）タイプのエフェクトの際に有用です。 **Note:** `Periodic Effects` は [predicted （予測）](#concepts-p) ができません。

`Duration` と `Infinite` の `GameplayEffects` の適用後に、もしそれらの `Ongoing Tag Requirements` が ([Gameplay Effect Tags](#concepts-ge-tags)) を満たしていない/満たしているならば、一時的にオフ/オンを切り替えることができます。 `GameplayEffect` をオフにすると、その `Modifiers` のエフェクトは削除され、 `GameplayTags` が適用されますが `GameplayEffect` は削除されません。 `GameplayEffect` をオンに戻すと `Modifiers` と `GameplayTags` が再適用されます。

もしあなたが手動で（`Duration` または `Infinite` の `GameplayEffect` の） `Modifiers` を再計算する必要がある場合（たとえば `Attributes` から得られないデータを使用する `MMC` があるとします）、あなたは `UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()` を使用して `UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)` を（既に使用しているのと同じ level で）呼び出すことができます。 バッキング `Attributes` に基づく `Modifiers` は、これらのバッキング `Attributes` が更新されると自動的に更新されます。 `Modifiers` を更新するための `SetActiveGameplayEffectLevel()` の主要な関数は以下のとおりです :

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// プライベート関数 でなければレベルを現在の値に設定する必要なしにこれら３つの関数を呼び出すことができました。
UpdateAllAggregatorModMagnitudes(Effect);
```

通常、 `GameplayEffects` はインスタンス化されません。 アビリティまたは `ASC` が `GameplayEffect` を適用したい場合、 `GameplayEffect` の `ClassDefaultObject` から [`GameplayEffectSpec`](#concepts-ge-spec) を作ります。 正常に適用された `GameplayEffectSpecs` は `FActiveGameplayEffect` と呼ばれる新しい構造体に追加されます。 これは `ASC` が `ActiveGameplayEffects` と呼ばれる特別なコンテナ構造体で追跡するためのものです。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-applying"></a>

#### 4.5.2 Gameplay Effects の適用

`GameplayEffects` は [`GameplayAbilities`](#concepts-ga) 上の関数や `ASC` 上の関数から様々な方法で適用できますが、通常は `ApplyGameplayEffectTo` の形式を取ります。 これらの異なる関数は本質的には利便性のための関数で、結局は `Target` 上の `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()` が呼び出されます。

`GameplayAbility` の外で `GameplayEffects` を適用する（たとえば投射物）には、 `Target` の `ASC` の取得した上でいずれかの関数を使い `ApplyGameplayEffectToSelf` を呼び出す必要があります。

 デリゲートへバインドすることで、 `Duration` または `Infinite` の `GameplayEffects` が `ASC` に適用されるタイミングをリッスンできます。 :
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
コールバック関数 :
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

サーバーは、レプリケーションモードによらず、常にこの関数を呼び出します。 autonomous proxy は `Full` と `Mixed` のレプリケーションモードでレプリケーションされた `GameplayEffects` に対してのみこれを呼び出します。 Simulated proxies は `Full` [レプリケーションモード](#concepts-asc-rm) でのみこれを呼び出します。

> Translators notes:SentyaAnko  
>> autonomous proxy  
>> Simulated proxies  
>  は、 EngineTypes.h の ENetRole 関連の話。
>  あるいは、 NetworkPredictionTypes.h の EReplicationProxyTarget ？おそらく前者。
>  EngineTypes.h より :
>> ```c++
>> /** The network role of an actor on a local/remote network context */
>> UENUM()
>> enum ENetRole
>> {
>> 	/** No role at all. */
>> 	ROLE_None,
>> 	/** Locally simulated proxy of this actor. */
>> 	ROLE_SimulatedProxy,
>> 	/** Locally autonomous proxy of this actor. */
>> 	ROLE_AutonomousProxy,
>> 	/** Authoritative control over the actor. */
>> 	ROLE_Authority,
>> 	ROLE_MAX,
>> };
>  NetworkPredictionTypes.h より :
>> ```c++
>> UENUM()
>> enum class EReplicationProxyTarget: uint8
>> {
>> 	ServerRPC,			// Client -> Server
>> 	AutonomousProxy,	// Owning/Controlling client
>> 	SimulatedProxy,		// Non owning client
>> 	Replay,				// Replay net driver
>> 	Debug,				// Replication target that is disabled in shipping
>> };
>> ```

**[⬆ Back to Top](#table-of-contents)**

#### 4.5.3 Gameplay Effects の削除

<a name="concepts-ga-removing"></a>

`GameplayEffects` は [`GameplayAbilities`](#concepts-ga) 上の関数や `ASC` 上の関数から様々な方法で削除できますが、通常は `RemoveActiveGameplayEffect` の形式を取ります。 これらの異なる関数は本質的には利便性のための関数で、結局は `Target` 上の `FActiveGameplayEffectsContainer::RemoveActiveEffects()` が呼び出されます。

`GameplayAbility` の外で `GameplayEffects` を削除するには、 `Target` の `ASC` の取得した上でいずれかの関数を使い `RemoveActiveGameplayEffect` を呼び出す必要があります。

デリゲートへバインドすることで、 `Duration` または `Infinite` の `GameplayEffects` が `ASC` に削除されるタイミングをリッスンできます。 :
```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
コールバック関数 :
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

サーバーは、レプリケーションモードによらず、常にこの関数を呼び出します。 autonomous proxy は `Full` と `Mixed` のレプリケーションモードでレプリケーションされた `GameplayEffects` に対してのみこれを呼び出します。 Simulated proxies は `Full` [レプリケーションモード](#concepts-asc-rm) でのみこれを呼び出します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods"></a>

#### 4.5.4 Gameplay Effect Modifiers

`Modifiers` は `Attribute` を変更し、 `Attribute` を [predictively （予測）的](#concepts-p) に変更をする唯一の方法です。 `GameplayEffect` は0個以上の `Modifiers` を所持することができます。 各 `Modifier` は指定された操作を介して１つの `Attribute` のみを変更する責任があります。

| Operation  | 説明                                                                                                                |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add`      | 結果を `Modifier` で指定された `Attribute` に加算します。 減算には負の値を用います。                                |
| `Multiply` | 結果を `Modifier` で指定された `Attribute` に乗算します。                                                           |
| `Divide`   | 結果を `Modifier` で指定された `Attribute` に対して除算します。                                                     |
| `Override` | `Modifier` で指定された `Attribute` を結果で上書きします。                                                          |

`Attribute` の `CurrentValue` は `BaseValue` に付け加えられたすべての `Modifiers` の集計結果です。 `Modifiers` の集計方法は `GameplayEffectAggregator.cpp` の `FAggregatorModChannel::EvaluateWithBase` で以下のように定義されています。

```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

すべての `Override` `Modifiers` は最終的な値を上書きし、最後に適用された `Modifier` が優先されます。

**Note:** パーセンテージベースの変更を行う場合、必ず加算の後に `Multiply` オペレーションが行われるようにしてください。

**Note:** [Prediction （予測）](#concepts-p) はパーセンテージの変更に問題があります。

`Modifiers` には４つのタイプがあります。 : `Scalable Float` 、 `Attribute Based` 、 `Custom Calculation Class` 、 そして `Set By Caller` です。 それらは全て、何らかの float 値を生成し、オペレーションに基づいて `Modifier` の指定された `Attribute` の変更に使用されます。

| `Modifier` Type            | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloats` は行を値、列をレベルとするデータテーブルを指定できる構造体です。 Scalable Floats はアビリティの現在のレベルを基に指定されたテーブルの行の値を自動的に読み取ります（または異なるレベル。もし [`GameplayEffectSpec`](#concepts-ge-spec)が上書きされている場合）。 この値は係数によってさらに操作することが可能です。 もし存在しないデータテーブル/行が指定された場合、値は1として扱われるため、係数を使用することですべてのレベルで１つの値をハードコーディングすることができます。 ![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `Attribute Based`          | `Attribute Based` `Modifiers` は `Source` （ `GameplayEffectSpec` を生成した人）または `Target` （ `GameplayEffectSpec` を受け取った人）のバッキング `Attribute` の `CurrentValue` または `BaseValue` を取得し、係数で修正し、更に前後の係数を追加します。 `Snapshotting` は `GameplayEffectSpec` が作られた際に バッキング `Attribute` がキャプチャされることを意味し、一方で no snapshotting は `GameplayEffectSpec` が適用された時に `Attribute` がキャプチャされることを意味します。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `Custom Calculation Class` | `Custom Calculation Class` は複雑な `Modifiers` に対し、最も柔軟性を提供します。 この `Modifier` は [`ModifierMagnitudeCalculation`](#concepts-ge-mmc) クラスを受け取り、結果の float 値を更に操作でき、係数で修正し、更に前後の係数を追加します。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `Set By Caller`            | `SetByCaller` `Modifiers` は実行時に、アビリティまたは `GameplayEffectSpec` で `GameplayEffectSpec` を作成した人によって、 `GameplayEffect` の外部で設定される値です。 たとえば、「アビリティをチャージするためにどのぐらい長くプレイヤーがボタンを押し続けたか」に基づいてダメージを設定したい場合に `SetByCaller` を使います。 `SetByCallers` は本質的に `TMap<FGameplayTag, float>` であり、 `GameplayEffectSpec` 上に存在します。 `Modifier` は提供された `GameplayTag` に紐付いた `SetByCaller` の値を探すように `Aggregator` に指示しているだけです。 `Modifiers` に使われる `SetByCallers` はコンセプトにより `GameplayTag` バージョンのみ使用できます。 `FName` バージョンはここでは使用できません。 `Modifier` が `SetByCaller` に設定されているが、正しい `GameplayTag` を持つ `SetByCaller` が `GameplayEffectSpec` に存在しない場合、ゲームはランタイムエラーをスローし、0を返します。 これにより、 `Divide` オペレーションの際に問題が発生する可能性があります。 `SetByCallers` の使用方法の詳細については、 [`SetByCallers`](#concepts-ge-spec-setbycaller) を参照してください。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>

##### 4.5.4.1 乗算と除算の Modifiers

デフォルトでは、すべての `Multiply` と `Divide` の `Modifiers` は、 `Attribute` の `BaseValue` に乗算または除算する前に、互いに加算されます。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```

*`GameplayEffectAggregator.cpp` より*

`Multiply` と `Divide` 両方の `Modifiers` は `Bias` 値 `1` をこの式においては持っています（ `Addition` の `Bias` 値は `0` です）。したがって、下記のようになります :

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```
この式は、いくつかの予期せぬ結果を導きます。 まず、この式は、すべての modifiers 同士を足してから、 `BaseValue` への乗算または除算を行います。 ほとんどの人は乗算と除算を一緒に行うことを期待するでしょう。 たとえば、もし２つの `1.5` の値を持つ `Multiply` modifiers があるならば、ほとんどの人は `BaseValue` に `1.5 x 1.5 = 2.25` が乗算されることを期待するでしょう。 その代わり、これは `1.5` 同士を加算して、 `BaseValue` に `2` (`50% 増加 + 別の 50% 増加 = 100% 増加`) として乗算します。 これは `GameplayPrediction.h` の例で、 `500` のベース速度に `10%` の速度バフを乗せると `550` となります。 別の `10%` のスピードバフを加えると `600` となります。

次に、この式は、使用できる数値に関して、いくつかのドキュメント化されていないルールが存在します。 これは Paragon を念頭に設計されているためです。

`Multiply` と `Divide` の、乗算加算式の規則 :
* `(「1より小さい」値が１つだけ) かつ (「1以上2未満の」値は任意の数)`
* `もしくは (「2以上の」値が１つ)`

式内の `Bias` は基本的に `[1, 2)` の範囲の数値の整数桁を減算します。 最初の `Modifier` の `Bias` は開始時の `Sum` 値（ループの前に `Bias` 値を設定）から減算します。 これが、それ自体はどんな値でも動作し、１つの値が `1より小さく` 複数の値が `1以上2未満` で動作する理由です。

`Multiply` を使ったいくつかの実例 :  
Multipliers: `0.5`  
`1 + (0.5 - 1) = 0.5` 、正しい

Multipliers: `0.5, 0.5`  
`1 + (0.5 - 1) + (0.5 - 1) = 0` 、`1` を誤って期待しましたか？ 複数の `1` 未満の値は、multipliers を追加する意味がありません。 Paragon は [`Multiply` `Modifiers` の最大の負の値](#cae-nonstackingge) のみを使用するように設計されています。 そのため、 `BaseValue` に乗算される `1` 未満の値は最大で１つだけです。

Multipliers: `1.1, 0.5`  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6` 、正しい

Multipliers: `5, 5`  
`1 + (5 - 1) + (5 - 1) = 9` 、`10` を誤って期待しました。 常に `Modifiers の合計 - Modifiers の数 + 1` になります。

多くのゲームでは、 `BaseValue` に適用する前に `Modify` と `Divide` `Modifiers` の乗算と除算を一緒に行いたいと思うでしょう。 これを成し遂げるには `FAggregatorModChannel::EvaluateWithBase()` の **エンジンコードの変更** が必要となります。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>

##### 4.5.4.2 Modifiers 上の Gameplay Tags

`SourceTags` と `TargetTags` はそれぞれの [Modifier](#concepts-ge-mods) に対して設定できます。それらは `GameplayEffect` の [`Application Tag requirements`](#concepts-ge-tags) と同じ様に動作します。つまり、タグはエフェクトが適用される時にのみ考慮されます。 すなわち、 periodic で infinite （定期的で無期限）のエフェクトがある場合、考慮されるのは、 periodic （定期的）な実行毎では **なく** 、最初の効果の適用時のみとなります。

`Attribute Based` Modifiers は `SourceTagFilter` と `TargetTagFilter` 設定することもできます。 `Attribute Based` Modifier のソースとなるアトリビュートの強さを決める時、これらのフィルターは特定の Modifiers をそのアトリビュートから除外するために使用されます。 source または target にフィルターのすべてのタグを持っていない Modifiers は除外されます。

この意味の詳細 :
source ASC と target ASC のタグは `GameplayEffects` にキャプチャされます。 source ASC の tags は `GameplayEffectSpec` が構築された時にキャプチャされ、 target ASC の tagsは エフェクトの実行時にキャプチャされます。 infinite または duration な effect の Modifier が 適用するのに「適格（適している）」（つまり、その Aggregator が適している）か判断する時に、これらのフィルターが設定されているならば、キャプチャされたタグがフィルターと比較されます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-stacking"></a>

#### 4.5.5 Stacking Gameplay Effects （スタック）

デフォルトでは、 `GameplayEffects` は新しい `GameplayEffectSpec` のインスタンスを（以前存在していた `GameplayEffectSpec` のインスタンスについて認知も配慮もせず）、申込みがあり次第適用します。 `GameplayEffects` はスタック設定することが可能で、 `GameplayEffectSpec` の新しいインスタンスを追加する代わりに、現存の `GameplayEffectSpec`スタックカウントを変更します。スタックは `Duration` と `Infinite` の `GameplayEffects` に対してのみ機能します。

２つのスタックタイプがあります。 : `Aggregate by Source` と `Aggregate by Target` です。

| スタックタイプ         | Description                                                                                                                          |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `Aggregate by Source`  | ターゲット上に、ソース `ASC` 毎に分割されたインスタンスが存在します。 各ソースは X 個のスタックを適用できます。                      |
| `Aggregate by Target`  | ターゲット上に、ソースに関係なく唯一のインスタンスが存在します。 各ソースは共有スタックの上限までスタックに積むことができます。      |

スタック はまた、expiration 、 duration refresh 、period reset に関するポリシーがあります。 `GameplayEffect` ブループリントには役に立つホバーツールチップがあります。

サンプルプロジェクトは `GameplayEffect` のスタックの変更をリッスンするカスタムブループリントノードを含んでいます。 HUD UMG ウィジェットはこれを使ってプレイヤーが所持しているパッシブアーマーのスタックの量を更新しています。 この `AsyncTask` は（UMG ウィジェットの `Destruct` イベント内で我々が行っているように）手動で `EndTask()` が呼び出されるまで永久に存在し続けます。 詳細については、 `AsyncTaskEffectStackChanged.h/cpp` を参照してください。

![GameplayEffect スタックの変更のリッスン BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ga"></a>

#### 4.5.6 Granted Abilities （付与）

`GameplayEffects` は新しい [`GameplayAbilities`](#concepts-ga) を `ASCs` に付与できます。 `Duration` と `Infinite` の `GameplayEffects` だけがアビリティを付与することができます。

一般的な使用例は、強制して他のプレイヤーになにかしたい時（たとえばノックバックやプルで移動させる）です。 `GameplayEffect` を適用し、彼らに望ましい行動をさせる、自動的に有効化するアビリティを付与します。 （どの様にして付与された際にアビリティを自動的に有効化するのかは [Passive Abilities](#concepts-ga-activating-passive) を参照してください）

設計者は `GameplayEffect` が付与するアビリティはどれか、 付与するレベル、 [入力をバインド](#concepts-ga-input) 、そして付与されたアビリティの削除ポリシーを選択できます。

| 削除ポリシー               | 説明                                                                                                                                                                         |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cancel Ability Immediately | 付与されたアビリティは、付与された `GameplayEffect` がターゲットから削除されたら、直ちにキャンセルと削除されます。                                                           |
| Remove Ability on End      | 付与されたアビリティは終了することを許可され、その後ターゲットから削除されます。                                                                                             |
| Do Nothing                 | 付与されたアビリティは、ターゲットから付与した `GameplayEffect` が削除されても、影響を受けません 。 後に手動で削除されるまで、ターゲットはアビリティを永続的に持ち続けます。 |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-tags"></a>

#### 4.5.7 Gameplay Effect Tags

`GameplayEffects` は複数の [`GameplayTagContainers`](#concepts-gt) を運びます。 設計者はカテゴリーごとに `Added` と `Removed` の `GameplayTagContainers` を編集し、コンパイル後、結果が `Combined` `GameplayTagContainer` に表示されます。 `Added` タグは親が事前に持っていなかった、この `GameplayEffect` が新しく追加するタグです。 `Removed` タグは 親クラスが持っているが、このサブクラスでは削除するタグです。

| カテゴリ                          | 説明                                                                                                                                                                                                                                                                                                                                                                                          |
| --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Gameplay Effect Asset Tags        | `GameplayEffect` が所持しているタグ。 それらは自身では何の機能も果たさず、 `GameplayEffect` を説明する目的のみを果たします。                                                                                                                                                                                                                                                                  |
| Granted Tags                      | `GameplayEffect` 上に存在するが、 `GameplayEffect` が与えられた `ASC` にも与えられるタグ。 `GameplayEffect` が削除される際に `ASC` からも削除されます。 `Duration` と `Infinite` の `GameplayEffects` のみで機能します。                                                                                                                                                                      |
| Ongoing Tag Requirements          | 一度適用されると、これらのタグは `GameplayEffect` がオンかオフかを決定します。 `GameplayEffect` をオフにしても適用されたままとなります。 もし「 Ongoing Tag Requirements が失敗したため `GameplayEffect` がオフ」であるが、 requirements が満たされた場合、 `GameplayEffect` は再びオンになり、 modifiers を再適用します。 `Duration` と `Infinite` の `GameplayEffects` でのみ機能します。 |
| Application Tag Requirements      | `GameplayEffect` がターゲットに適用できるかを決定するターゲット上のタグ。 これらの要件が満たされていない場合、 `GameplayEffect` は適用されません。                                                                                                                                                                                                                                            |
| Remove Gameplay Effects with Tags | この `GameplayEffect` が 正常に適用された時、`Asset Tags` または `Granted Tags` にこれらのタグが含まれているターゲット上の `GameplayEffects` はターゲットから削除されます。                                                                                                                                                                                                                   |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-immunity"></a>

#### 4.5.8 Immunity （耐性）

`GameplayEffects` は耐性を付与でき、 [`GameplayTags`](#concepts-gt) に基づいて他の `GameplayEffects` の適用を効果的に防ぎます。 耐性は `Application Tag Requirements` など、他の手段でも効果的に達成できますが、このシステムは耐性が原因で `GameplayEffects` が防がれた際のデリゲート `UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate` を提供します。

`GrantedApplicationImmunityTags` はソースの `ASC` （ソースアビリティの `AbilityTags` のタグがある場合はそれを含む）に指定されたタグのいずれかが含まれているかどうかを確認します。 これは、特定の「キャラまたはソース」のタグに基づいて、すべての `GameplayEffects` からの耐性を提供する方法です。

`Granted Application Immunity Query` は入力された `GameplayEffectSpec` を、その適用を防ぐまたは許可するクエリのいずれかと一致するかを確認します。

クエリに関しては `GameplayEffect` ブループリントに役に立つホバーツールチップがあります。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec"></a>

#### 4.5.9 Gameplay Effect Spec

 [`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`) は `GameplayEffects` のインスタンス化と考えることができます。 それらは、「それらが示す `GameplayEffect` クラスへの参照、それが作成されたレベル、作成した人」を保持します。 これらは実行時の適用前に自由に作成され、変更されます。 実行に先駆けて設計者によって作成される `GameplayEffects` とは異なります。 `GameplayEffect` を適用する時、 `GameplayEffectSpec` は `GameplayEffect` から作られ、実際にターゲットに適用されます。

> Translators notes:Suggestion:SentyaAnko  
> This translation is likely to be incorrect.  
>> These can be freely created and modified at runtime before application unlike `GameplayEffects` which should be created by designers prior to runtime.

`GameplayEffectSpecs` は `GameplayEffects` から作られます。 `BlueprintCallable` である `UAbilitySystemComponent::MakeOutgoingSpec()` を使用することで行われます。 `GameplayEffectSpecs` はすぐに適用される必要がありません。 それはアビリティによって作られた投射物に `GameplayEffectSpec` を渡すのが一般的です。 後で投射物が命中したターゲットにそれを適用できます。 `GameplayEffectSpecs` が正常に適用された時、 `FActiveGameplayEffect` と呼ばれる新しい構造体を返します。

注目すべき `GameplayEffectSpec` の内容 :
* この `GameplayEffect` が作成された `GameplayEffect` クラス。
* この `GameplayEffectSpec` のレベル。 大抵は `GameplayEffectSpec` を作成したアビリティと同じレベルだが、異なることもあります。
* `GameplayEffectSpec` の duration （期間）。デフォルトでは、 `GameplayEffect` の duration （期間）ですが、異なることもあります。
* 定期的なエフェクトのための `GameplayEffectSpec` の period （期間）。 デフォルトでは `GameplayEffect` の period （期間）ですが、異なることもあります。
* `GameplayEffectSpec` の現在のスタックカウント。 スタックの制限は `GameplayEffect` でされています。
* [`GameplayEffectContextHandle`](#concepts-ge-context) は誰が `GameplayEffectSpec` を作成したかを教えてくれます。
* スナップショットのために `GameplayEffectSpec` が作成された際にキャプチャされた `Attributes` 。
* `GameplayEffect` が付与する `GameplayTags` に加えて、 `GameplayEffectSpec` がターゲットに付与する `DynamicGrantedTags` 。
* `GameplayEffect` が持つ `AssetTags` に加えて、 `GameplayEffectSpec` が持つ `DynamicAssetTags` 。
* `SetByCaller` `TMaps` 。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>

##### 4.5.9.1 SetByCallers

`SetByCallers` は `GameplayEffectSpec` が `GameplayTag` または `FName` に関連付けられた float 値を持ち運ぶのを可能にします。
それらはそれぞれの `TMaps` に保存されます : `GameplayEffectSpec` 上 の`TMap<FGameplayTag, float>` と `TMap<FName, float>` 。
これらは `GameplayEffect` の `Modifiers` のとして、または float を運ぶ一般的な手法として使用できます。
アビリティ内で生成された数字データを `SetByCallers` を介して [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) や [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) に渡すのが一般的です。

| `SetByCaller` Use | 注釈                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`       | `GameplayEffect` クラスで事前に定義されている必要があります。 `GameplayTag` バージョンのみ使用可能です。 もし１つが `GameplayEffect` クラス上に定義されているが、 `GameplayEffectSpec` と一致するタグと float 値のペアを持っていない場合、ゲームは `GameplayEffectSpec` の適用時にランタイムエラーを起こし、0を返します。 これは `Divide` 操作の潜在的な問題です。 詳細については、 [`Modifiers`](#concepts-ge-mods) を参照してください。 |
| 他の場所で        | どこにも事前定義の必要はありません。 `GameplayEffectSpec` 上に存在しない `SetByCaller` を読み取ると、 オプションの警告とともに、開発者によって定義されたデフォルトの値を返すことができます。                                                                                                                                                                                                                                           |

ブループリントで `SetByCaller` の値の割り当てるには、あなたが必要としているバージョン（ `GameplayTag` または `FName` ）のブループリントノードを使います。

![SetByCaller の割り当て](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

ブループリントで `SetByCaller` の値を読み取るには、ブループリントライブラリでカスタムノードを作成する必要があります。

`SetByCaller` の値を C++ で割り当てるには、 あなたが必要としているバージョン（ `GameplayTag` または `FName` ）の関数を使います。

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```


`SetByCaller` の値を C++ で読み取るには、 あなたが必要としているバージョンの関数（ `GameplayTag` または `FName` ）を使います。

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

`GameplayTag` バージョンを使用すること（ `FName` を使用するよりも）をおすすめします。 これによりブループリント内でスペルエラーを防止できます。 また `GameplayTags` は `FNames` よりもネットワーク経由で送信するのにより効率的です。 （ `GameplayEffectSpec` がレプリケーションされる際に `TMaps` もレプリケーションされるためです。）

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-context"></a>

#### 4.5.10 Gameplay Effect Context

[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html) 構造体は `GameplayEffectSpec` の instigator （発生の原因になった人）と [`TargetData`](#concepts-targeting-data) に関する情報を保持しています。 これはまた、（ [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) / [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 、 [`AttributeSets`](#concepts-as) 、 [`GameplayCues`](#concepts-gc) などの場所の間で）任意のデータを渡すため、サブクラス化するにも最も良い構造体です。

`GameplayEffectContext` をサブクラス化するには :

1. `FGameplayEffectContext` のサブクラスにする
1. `FGameplayEffectContext::GetScriptStruct()` をオーバーライドする
1. `FGameplayEffectContext::Duplicate()` をオーバーライドする
1. `FGameplayEffectContext::NetSerialize()` をオーバーライドする（もし新しいデータをレプリケーションする必要がある場合）
1. あなたのサブクラスのための `TStructOpsTypeTraits` を実装する（親構造体である `FGameplayEffectContext` が持っているように）
1. あなたの [`AbilitySystemGlobals`](#concepts-asg) クラスの `AllocGameplayEffectContext()` をオーバーライドし、あなたのサブクラスの新しいオブジェクトを返すようにする

[GASShooter](https://github.com/tranek/GASShooter) はサブクラス `GameplayEffectContext` を（ `GameplayCues` でアクセスできる `TargetData` を追加するために）使用しています。 具体的に言うと、一人以上の敵に当てることができるショットガン用です。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mmc"></a>

#### 4.5.11 Modifier Magnitude Calculation （Modifier の大きさ計算）

[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagCalc` または `MMC`) は `GameplayEffects` の [`Modifiers`](#concepts-ge-mods) として使用される、強力なクラスです。 それらは [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) と同じ様に機能しますが、それが強力ということではなく、最も重要なのは [predicted （予測）](#concepts-p) です。 それらの唯一の目的は `CalculateBaseMagnitude_Implementation()` から float 値を返すことです。 サブクラス化し、この関数をオーバーライドできます。 ブループリントと C++ で可能です。

`MMCs` はどんな duration （ `Instant` 、 `Duration` 、 `Infinite` または `Periodic` ）の `GameplayEffects` でも使用できます。

`MMCs` の強みは、（ `GameplayEffectSpec` にフルアクセスして `GameplayTags` と `SetByCallers`を読み取り）`GameplayEffect` の `Source` または `Target` 上にある任意の数の `Attributes` の値をキャプチャする能力にあります。 `Attributes` はスナップショットをすることもしないこともできます。 スナップショットされた `Attributes` は `GameplayEffectSpec` が作成された際に キャプチャされるのに対し、スナップショットされない `Attributes` は `GameplayEffectSpec` が適用された際にキャプチャされ、さらに `Infinite` と `Duration` の `GameplayEffects` で `Attribute` が変更された際には自動的に更新されます。 `Attributes` のキャプチャはその `CurrentValue` の再計算を、存在している `ASC` の mods からされます。 この再計算は `AbilitySet` の [`PreAttributeChange()`](#concepts-as-preattributechange) が実行されま**せん**ので、どのようなクランプもここで再び行う必要があります。

| Snapshot | Source or Target | `GameplayEffectSpec` でのキャプチャ | `Infinite` と `Duration` の `GE` で `Attribute` が変更された際の自動更新 |
| -------- | ---------------- | ----------------------------------- | ------------------------------------------------------------------------ |
| Yes      | Source           | 作成時                              | No                                                                       |
| Yes      | Target           | 適用時                              | No                                                                       |
| No       | Source           | 適用時                              | Yes                                                                      |
| No       | Target           | 適用時                              | Yes                                                                      |

`MMC` から返却された float 値は `GameplayEffect` の `Modifier` で、係数と事前と事後の定数の追加によりさらに変更する事が可能です。

例としては `Target` のマナ `Attribute` をキャプチャし毒エフェクトでそれを減らす `MMC` で、「どのぐらいのマナを `Target` が持っているか」と「 `Target` が持つ可能性があるタグ」に応じて減少量が変化します :

```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef は ヘッダで右のように定義されています。 FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef は ヘッダで右のように定義されています。 FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// どのバフを使用するかに影響するため、タグをソースとターゲットから収集します
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
		// 対象が半分以上のマナを持つ場合、効果を倍にする
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// 対象が PoisonMana に弱い場合、効果を倍にする
		Reduction *= 2;
	}
	
	return Reduction;
}
```

もしあなたが `MMC` のコンストラクタに `RelevantAttributesToCapture` に `FGameplayEffectAttributeCaptureDefinition` を追加しておらず、かつ `Attributes` をキャプチャしようとした場合、キャプチャしようとすると Spec が見つからないとエラーが発生します。 もし `Attributes` をキャプチャする必要がない場合、 `RelevantAttributesToCapture` に何も追加する必要はありません。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec"></a>

#### 4.5.12 Gameplay Effect Execution Calculation （実行計算）

[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html) （ `ExecutionCalculation` 、 `Execution` （この用語はプラグインのソースコードにてよく見られます）、 または `ExecCalc`）は `GameplayEffects` が `ASC` に変更を加えるための最も強力な方法です 。 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) のように、これらは `Attributes` をキャプチャでき、オプションでスナップショットを作成できます。 `MMCs` とは異なり、これらは１つ以上の `Attribute` を変更でき、基本的にプログラマーが望めば何でもできます。 この強力さと柔軟性の欠点は [predicted （予測）](#concepts-p) できないところと、 C++ で実装しなければならないところです。

`ExecutionCalculations` は `Instant` と `Periodic` の `GameplayEffects` の場合のみ使用することができます。「Execute」という単語が含まれているものは全て、通常は前述の２つのタイプの `GameplayEffects` を指し示します。

Snapshotting は `Attribute` を `GameplayEffectSpec` が作られた時にキャプチャするのに対し、 not snapshotting は `Attribute` を `GameplayEffectSpec` が適用された際にキャプチャします。 `Attributes` のキャプチャはその `CurrentValue` の再計算を、存在している `ASC` の mods からされます。 この再計算は `AbilitySet` の [`PreAttributeChange()`](#concepts-as-preattributechange) が実行されま**せん**ので、どのようなクランプもここで再び行う必要があります。

| Snapshot | Source or Target | `GameplayEffectSpec` でのキャプチャ |
| -------- | ---------------- | ----------------------------------- |
| Yes      | Source           | 作成時                              |
| Yes      | Target           | 適用時                              |
| No       | Source           | 適用時                              |
| No       | Target           | 適用時                              |

`Attribute` のキャプチャの設定は、 Epic の ActionRPG Sample Project によって設定されたパターンに従って、「保持する構造体を定義」し、「`Attributes` をキャプチャする方法を定義」し、「構造体のコンストラクタ内でコピーを１つ作成」します。 すべての `ExecCalc` に対し、同様の構造体を持つことになります。 **Note:** 各構造体は同一の namespace を共有するため、一意の名前が必要です。 構造体に同じ名前を使うと、 `Attributes` をキャプチャする際に誤った動作が発生します（殆どの場合、間違った `Attributes` の値がキャプチャされます）。

`Local Predicted （ローカル予測）` の為、 `Server Only` と `Server Initiated` [`GameplayAbilities`](#concepts-ga) はサーバー側でのみ `ExecCalc` が呼び出されます。

受けたダメージの計算（ `Source` と `Target` の多くのアトリビュートを読み取り複雑な式に基づく）は、最も一般的な `ExecCalc` の例です。 同梱のサンプルプロジェクトにはダメージ計算するための単純な `ExecCalc` （ `GameplayEffectSpec` の [`SetByCaller`](#concepts-ge-spec-setbycaller) からダメージ値を読み取り、`Target` からキャプチャされた アーマー `Attribute` に基づいて値を軽減する）が含まれます。 詳細については、 `GDDamageExecCalculation.cpp/.h` を参照してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>

##### 4.5.12.1 Sending Data to Execution Calculations （実行計算へのデータ送信）

`Attributes` のキャプチャに加え、`ExecutionCalculation` にデータを送るには、いくつかの方法があります。

<a name="concepts-ge-ec-senddata-setbycaller"></a>

###### 4.5.12.1.1 SetByCaller

[`GameplayEffectSpec` に設定された `SetByCallers`](#concepts-ge-spec-setbycaller) は `ExecutionCalculation` 内で直接読み取れます。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>

###### 4.5.12.1.2 Backing Data Attribute Calculation Modifier

`GameplayEffect` にハードコーディングされた値を望む場合、（キャプチャされた `Attributes` の１つをバッキングデータとして使用する） `CalculationModifier` を使うことで渡すことができます。

このスクリーンショットの例では、キャプチャされたダメージ `Attribute` に50を加算しています。 これを `Override` に設定することで、ハードコードされた値だけを取得することもできます。

![Backing Data Attribute Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

`ExecutionCalculation` は `Attribute` をキャプチャした際にこの値を読み取ります。

```c++
float Damage = 0.0f;
// ExecutionCalculation の下の CalculationModifier として ダメージ GE に設定されたオプションダメージ値をキャプチャします
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>

###### 4.5.12.1.3 Backing Data Temporary Variable Calculation Modifier

`GameplayEffect` にハードコーディングされた値を望む場合、（ C++ で呼び出される `Temporary Variable` または `Transient Aggregator` を使用する） `CalculationModifier` を使うことで渡すことができます。 `Temporary Variable` は `GameplayTag` に関連付けられています。

このスクリーンショットの例では、 `Data.Damage` の `GameplayTag` を使用することで `Temporary Variable` に50を加算しています。

![Backing Data Temporary Variable Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

バッキング `Temporary Variables` を `ExecutionCalculation` のコンストラクタに追加します :

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation` はこの値を `Attribute` のキャプチャ関数と同様の特別なキャプチャ関数を使用して読み取ります。

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>

###### 4.5.12.1.4 Gameplay Effect Context

カスタムされた [`GameplayEffectSpec` 上の `GameplayEffectContext`](#concepts-ge-context) を介して `ExecutionCalculation` へデータを送ることができます。

`ExecutionCalculation` 内で `FGameplayEffectCustomExecutionParameters` から `EffectContext` にアクセスできます。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

`GameplayEffectSpec` または `EffectContext` でなにか変更が必要な場合 :

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

`ExecutionCalculation` 内で `GameplayEffectSpec` を変更するならば、注意が必要です。
詳細については、 `GetOwningSpecForPreExecuteMod()` のコメントを参照してください。

```c++

/** 非 const アクセスです。特にアトリビュートのキャプチャ後に Spec を変更する場合、これに注意してください */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-car"></a>

#### 4.5.13 Custom Application Requirement

[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html) (`CAR`) クラスは（ `GameplayEffect` での単純な `GameplayTag` のチェックと比較して） `GameplayEffect` 適用できるかどうかの高度な制御を設計者に与えます。 ブループリントで `CanApplyGameplayEffect()` をオーバーライドする、もしくは、 C++ で `CanApplyGameplayEffect_Implementation()` をオーバーライドすることで、それらは実装できます。

`CARs` をいつ使用するかの例 :
* `Target` が一定量の `Attribute` を持つことを必要としている
* `Target` が一定数の `GameplayEffect` のスタックを持つことを必要としている

`CARs` はもっと高度なこともできます。 「 `GameplayEffect` のインスタンスが既に `Target` 上に存在するかのチェック」や「 新しいインスタンスを適用する代わり （ `CanApplyGameplayEffect()` に false を返す）として、既存のインスタンスの [duration （期間）の変更](#concepts-ge-duration) 」などです。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cost"></a>

#### 4.5.14 Cost Gameplay Effect

[`GameplayAbilities`](#concepts-ga) はオプションの `GameplayEffect` があり、それはアビリティのコストとして使うように特別にデザインされています。 コストとは `GameplayAbility` を有効にするために`ASC` が必要とする `Attribute` の量のことです。 もし `GA` が `Cost GE` を消費する余裕がない場合、それを有効にできません。 この `Cost GE` は（ `Attributes` を減じる１つ以上の `Modifiers` を持つ） `Instant` な `GameplayEffect` である必要があります。 デフォルトでは、 `Cost GEs` は predicted （予測）されるものと意図されており、その特性を維持するために `ExecutionCalculations` を使わないことをおすすめします。 複雑なコスト計算を行う場合は、 `MMCs` は完全に容認でき、推奨されます。

最初のうちは殆どの場合、コストを持つ `GA` 毎にユニークな `Cost GE` を１つ持つことになるでしょう。 より高度なテクニックは、１つの `Cost GE` を複数の `GAs` で再利用し、`Cost GE` で生成された `GameplayEffectSpec` を `GA` の固有データ （コストの値は `GA` で定義される）で変更することです。 **これは `Instanced` アビリティでのみ動作します。**

`Cost GE` を再利用するための２つのテクニック :

1. **`MMC` を使用すること。** これが最も簡単な方法です。 `GameplayEffectSpec` から取得できる `GameplayAbility` インスタンスからコストの値を読み取る [`MMC`](#concepts-ge-mmc) を作成します。

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

この例では、コストの値は `GameplayAbility` の子クラスに追加した、 `FScalableFloat` です。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **`UGameplayAbility::GetCostGameplayEffect()` をオーバーライドします。** この関数をオーバーライドし、 `GameplayAbility` 上にあるコストの値を読み取る [`GameplayEffect` を実行時に生成](#concepts-ge-dynamic) します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>

#### 4.5.15 Cooldown Gameplay Effect

[`GameplayAbilities`](#concepts-ga) はオプションの `GameplayEffect` があり、それはアビリティのクールダウンとして使うように特別にデザインされています。
Cooldowns は、アビリティを有効化された後、再び有効化を可能にする期間を測定します。 もし `GA` がまだクールダウンの場合、それを有効化できません。 この `Cooldown GE` は（ `Modifiers` を持たず、「 `GameplayAbility` 毎、またはアビリティスロット毎（ゲームにクールダウンを共有する、交換可能なアビリティがスロットに割り当てられている場合）にユニークな `GameplayTag` 」 を `GameplayEffect` の `GrantedTags` ("`Cooldown Tag`") の中で持つ） `Duration` な `GameplayEffect` である必要があります。 `GA` は実際には `Cooldown Tag` の存在をチェックします（ `Cooldown GE` の存在ではなく）。 デフォルトでは、 `Cooldown GEs` は predicted （予測）されるものと意図されており、その特性を維持するために `ExecutionCalculations` を使わないことをおすすめします。 複雑なクールダウン計算を行う場合は、 `MMCs` は完全に容認でき、推奨されます。

最初のうちは殆どの場合、クールダウンを持つ `GA` 毎にユニークな `Cooldown GE` を１つ持つことになるでしょう。 より高度なテクニックは、１つの `Cooldown GE` を複数の `GAs` で再利用し、`Cooldown GE` で生成された `GameplayEffectSpec` を `GA` の固有データ（クールダウンの duration （期間） と `Cooldown Tag` は `GA` で定義される）で変更することです。 **これは `Instanced` アビリティでのみ動作します。**

`Cooldown GE` を再利用するための２つのテクニック :

1. **[`SetByCaller`](#concepts-ge-spec-setbycaller) を使用すること。** これが最も簡単な方法です。 共有する `Cooldown GE` の duration （期間）を `GameplayTag` を使って `SetByCaller` に設定します。 `GameplayAbility` のサブクラスで、「duration （期間）の float / `FScalableFloat` 」と、「一意の `Cooldown Tag` の `FGameplayTagContainer` 」と、「 `Cooldown Tag` と `Cooldown GE` のタグを結合した結果を返すために使用する一時的な `FGameplayTagContainer` 」を定義します。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// GetCooldownTags() ポインタを返すのに使う一時的なコンテナ。
// CooldownTags と Cooldown GE の cooldown タグの結合した結果となります。
UPROPERTY()
FGameplayTagContainer TempCooldownTags;
```

次に `UGameplayAbility::GetCooldownTags()` をオーバーライドし、 `Cooldown Tags` と既存の `Cooldown GE` タグの結合した結果を返します。

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

最後に、 `UGameplayAbility::ApplyCooldown()` をオーバーライドして、`Cooldown Tags` を導入し、 クールダウン `GameplayEffectSpec` に `SetByCaller` を追加します。

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

この画像では、クールダウンの duration （期間） `Modifier` が `SetByCaller` に、 `Data Tag` は `Data.Cooldown` に設定されています。 上記のコードでは、 `Data.Cooldown` は `OurSetByCallerTag` にあたります。

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)


2. **[`MMC`](#concepts-ge-mmc) を使用する。** これは上記と同じ設定です（「`Cooldown GE` 上の duration （期間） としての `SetByCaller` 」と「 `ApplyCooldown` 」の設定除いて）。 代わりに、 duration （期間） を `Custom Calculation Class` に設定し、新しく作成する `MMC` を指定します。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// GetCooldownTags() ポインタを返すのに使う一時的なコンテナ。
// CooldownTags と Cooldown GE の cooldown タグの結合した結果となります。
UPROPERTY()
FGameplayTagContainer TempCooldownTags;
```
次に `UGameplayAbility::GetCooldownTags()` をオーバーライドし、 `Cooldown Tags` と既存の `Cooldown GE` タグの結合した結果を返します。

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

最後に、 `UGameplayAbility::ApplyCooldown()` をオーバーライドして、`Cooldown Tags` を導入し、 クールダウン `GameplayEffectSpec` に `SetByCaller` を追加します。

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

##### 4.5.15.1 Get the Cooldown Gameplay Effect's Remaining Time （Gameplay Effect のクールダウンの残り時間を取得する）

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
**Note:** クライアントのクールダウンの残り時間をクエリするには、レプリケーションされた `GameplayEffects` を受信できる必要があります。 これは、 `ASC` の [レプリケーションモード](#concepts-asc-rm) によって異なります。

<a name="concepts-ge-cooldown-listen"></a>

##### 4.5.15.2 Listening for Cooldown Begin and End

クールダウンの開始をリッスンするには、 `AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf` にバインドすることで `Cooldown GE` が適用された際、または、 `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)` にバインドすることで `Cooldown Tag` が追加された際の、どちらかで応答できます。 適用した `GameplayEffectSpec` へのアクセスもできるため、 `Cooldown GE` が追加された時にリッスンするのをおすすめします。 これにより `Cooldown GE` が locally predicted （ローカル予測） されたものなのか、 サーバーに修正されたものなのか、判断することができます。

クールダウンの終了をリッスンするには、 `AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()` にバインドすることで `Cooldown GE` が削除された際、または `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)` にバインドすることで `Cooldown Tag` が削除された際の、どちらかで応答できます。 `Cooldown Tag` が削除された際にリッスンするのをおすすめします。なぜなら、サーバーに修正された `Cooldown GE` が渡されると、 locally predicted （ローカル予測） されたものが削除され、そのことにより `OnAnyGameplayEffectRemovedDelegate()` が発火される（まだクールダウン中にも関わらず）ためです。 predicted （予測）された `Cooldown GE` を削除され、サーバーに修正された `Cooldown GE` が適用されている間、 `Cooldown Tag` は変更されません。

**Note:** クライアント上で追加または削除される `GameplayEffect` をリッスンするには、レプリケーションされた `GameplayEffects` を受信できる必要があります。 これは、 `ASC` の [レプリケーションモード](#concepts-asc-rm) によって異なります。

サンプルプロジェクトは `GameplayEffect` のクールダウンの開始と終了をリッスンするカスタムブループリントノードを含んでいます。 HUD UMG ウィジェットはこれを使ってメテオのクールダウンの残り時間の量を更新しています。 詳細については、 `AsyncTaskEffectCooldownChanged.h/cpp` を参照してください。


![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>

##### 4.5.15.3 Predicting Cooldowns （クールダウンの予測）

クールダウンは現在、実際には predicted （予測）できません。 クールダウンタイマーの UI を（locally predicted （ローカル予測）された `Cooldown GE` が適用された際に）開始できますが `GameplayAbility` の実際のクールダウンはサーバーのクールダウンの残り時間に紐付けられています。 プレイヤーのレイテンシによっては、 locally predicted （ローカル予測）されたクールダウンは期限切れとなる可能性がありますが、`GameplayAbility` はサーバーではまだクールダウン中なので、サーバーのクールダウンの期限切れまで `GameplayAbility` の即時の再有効化を防ぐことができます。

サンプルプロジェクトは locally predicted （ローカル予測）されたクールダウンの開始時にメテオのアビリティの UI アイコンをグレーアウトし、サーバーに修正された `Cooldown GE` が渡されたらクールダウンタイマーを開始することでこれを処理しています。

これによるゲームプレイの影響は、大きいレイテンシを持つプレイヤーは（小さいレイテンシを持つプレイヤーよりも）低いレートの銃弾の発射クールダウンのアビリティとなり、不利になります。Fortnite は武器に、クールダウン `GameplayEffects` を使用しないカスタムブックキーピングをもたせ、これを回避しています。

真の predicted （予測）されたクールダウンを可能にすること（プレイヤーは、サーバー側がまだクールダウン中でもローカルクールダウンが期限切れになると `GameplayAbility` を有効化できます。）は、 Epic がいつか [future iteration of GAS](#concepts-p-future) で実装したいと考えていることです。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-duration"></a>

#### 4.5.16 Changing Active Gameplay Effect Duration （有効な Gameplay Effect の持続時間の変更）

「 `Cooldown GE` 」 または 「 `Duration` の `GameplayEffect` 」 の残り時間を変更するには「 `GameplayEffectSpec` の 「 `Duration` の変更」」と「その「 `StartServerWorldTime` と `CachedStartServerWorldTime` と `StartWorldTime` 」 の更新」と「 `CheckDuration()` を使用して duration （期間）のチェックの再実行」が必要です。 これをサーバー上で行い、 `FActiveGameplayEffect` にダーティをマークすると、変更がクライアントにレプリケーションされます。
**Note:** これは `const_cast` 伴い、また、 Epic の意図した durations （期間）を変更する方法ではない可能性がありますが、これまでのところは上手く機能しているように見受けられます。

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

#### 4.5.17 Creating Dynamic Gameplay Effects at Runtime （実行時の動的な Gameplay Effect の作成）

実行時に動的に `GameplayEffects` を生成することは高度なトピックです。 あまりに頻繁にこれを行うべきではありません。

`Instant` の `GameplayEffects` だけが実行時に生成可能です（ C++ を使い自前で作り上げることで）。 `Duration` と `Infinite` の `GameplayEffects` 実行時に動的に生成することができません。 なぜならレプリケーション時に存在しない `GameplayEffect` クラスの定義を探すためです。この機能を実現するには、代わりに、エディタで通常行うように `GameplayEffect` クラスの原型を生成するべきです。次に `GameplayEffectSpec` インスタンスを、実行時に、必要に応じてカスタマイズします。

実行時に生成された `Instant` の `GameplayEffects` は [local predicted （ローカル予測）](#concepts-p) された `GameplayAbility` の中からも呼び出すことができます。 ただし、動的な生成がどのような副作用をもたらすかはまだ分かっていません。

##### 例

サンプルプロジェクトでは、 `AttributeSet` により致命打を受けた際に、キャラクターのキラーに対してゴールドと経験値を送り返すものを作成しています。

```c++
// 報奨金を与えるための Gameplay Effect の動的なインスタンスの生成
// ダイナミックな instant Gameplay Effect を作成して、賞金を獲得します
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
二番目の例では、 local predicted （ローカル予測） された `GameplayAbility` の中での、実行時の `GameplayEffect` の生成を示します。 自身のリスクで使用してください（コード内のコメントを参照）！

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// 実行時の GE 生成。
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // ランタイム GE では instant のみ機能します。

		// MyAttribute を 42 でオーバーライドする、シンプルな拡縮可能な float の modifier の追加。
		// 実際のワールドへの適用は TriggerEventData を介して渡された情報を消費する
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// GE の適用。

		// ここで GESpec を生成します（GE class のデフォルトオブジェクトから GESpecs を生成する ASC の挙動を回避するため）。
		// ここでは動的な GE を使用しているため、 GESpec がベースとなる GameplayEffect クラスを伴って生成することになり、
		// modifiers を失うことになります。
		// 注意 : この 「 hack 」 が行われた際の欠点は不明です！
		// spec は GE オブジェクトが GarbageCollector に収集されるのを防ぎます（GE は spec 上の UPROPERTY の為）。
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // 「 new 」 。ライフタイムはハンドル内のシェアードポインタによって管理される。
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```
**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-containers"></a>

#### 4.5.18 Gameplay Effect Containers （コンテナ）

Epic の [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) では `FGameplayEffectContainer` と呼ばれる構造体を実装しています。 これらはバニラ（提供されたままの） GAS ではありませんが、 `GameplayEffects` と [`TargetData`](#concepts-targeting-data) を含めるのに非常に便利です。 これは `GameplayEffects` から `GameplayEffectSpecs` を生成したり `GameplayEffectContext` にデフォルト値を設定したりするような骨が折れる作業の一部を自動化します。 `GameplayAbility` で `GameplayEffectContainer` を生成し、それをスポーンされた投射物に渡すことは非常に簡単で素直です。 同梱のサンプルプロジェクト内で `GameplayEffectContainers` を実装しないことを選択しました（バニラ（提供されたままの） GAS でそれら無しでどの様に機能するのかを示すため）が、それらを調べて、プロジェクトに追加するのを検討することを強くおすすめします。

`GameplayEffectContainers` の内部の `GESpecs` にアクセスし（ `SetByCallers` の追加のような）何かをするためには、 `FGameplayEffectContainer` を分解し、 `GESpecs` の配列内のインデックスによって `GESpec` の参照にアクセスします。 これには、アクセスしたい `GESpec` のインデックスを事前に知っていることが必要です。

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers` には [targeting （ターゲティング）](#concepts-targeting-containers) のオプションの効率的な方法も含まれています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga"></a>

### 4.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>

#### 4.6.1 Gameplay Ability の定義

[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`) はゲーム内でアクターが行うことができるアクションやスキルです。 １つ以上の `GameplayAbility` を一度に有効化できます。たとえば sprint や銃の射撃です。 ブループリントまたは C++ で作成できます。

`GameplayAbilities` の例 :
* Jumping
* Sprinting
* Shooting a gun
* Passively blocking an attack every X number of seconds
* Using a potion
* Opening a door
* Collecting a resource
* Constructing a building

`GameplayAbilities` で実装してはいけないこと :
* 基本的に移動の入力
* UI とのいくつかの相互作用 - ストアからアイテムを購入するために `GameplayAbility` の使用

これらはルールではなく、私の推奨事項です。あなたの設計と実装とは異なる場合があります。

`GameplayAbilities` は「アビリティの変更量を修正」したり「 `GameplayAbility` の機能を変更」したりするための、レベルを持つデフォルトの機能があります。

`GameplayAbilities` は [`Net Execution Policy`](#concepts-ga-net) 次第で Owning Client （所有クライアント）とサーバー、どちらもまたはいずれかで実行されます（ simulated proxies を除いて）。 `Net Execution Policy` は `GameplayAbility` が locally [predicted](#concepts-p) （ローカル予測）されるかどうかを決定します。 それらは [オプションのコストとクールダウン `GameplayEffects`](#concepts-ga-commit) のデフォルトの振る舞いを含みます。 `GameplayAbilities` は [`AbilityTasks`](#concepts-at) を時間経過で発生するアクションのために使います（イベント待ち、アビリティの変更待ち、プレイヤーの目標決定待ち、`Root Motion Source` による `Character` の移動等のような）。 **シミュレートされたクライアントは `GameplayAbilities` を実行しません。** 代わりに、サーバーがアビリティを実行した時、視覚的に再生する必要があるもの（アニメーションモンタージュ等）は、 simulated proxies 上では、（音声やパーティクルのような装飾用の） `AbilityTasks` または [`GameplayCues`](#concepts-gc) を介してレプリケーションもしくは RPC されます。

すべての `GameplayAbilities` は、ゲームロジックでオーバーライドされた `ActivateAbility()` 関数を持ちます。 `GameplayAbility` が完了またはキャンセルされた時に実行される追加のロジックを `EndAbility()` で追加できます。

単純な `GameplayAbility` のフローチャート :
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

もっと複雑な `GameplayAbility` のフローチャート :
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

複雑なアビリティは互いに影響（有効化、キャンセルなど）し合う複数の `GameplayAbilities` を使うことで実装することができます。

<a name="concepts-ga-definition-reppolicy"></a>

##### 4.6.1.1 Replication Policy （レプリケーションポリシー）

このオプションを使用しないでください。 この名称は誤解を招くもので、あなたには必要ないものです。 [`GameplayAbilitySpecs`](#concepts-ga-spec) はデフォルトでサーバーから Owning Client （所有クライアント）にレプリケーションされます。 上で述べたように、 **`GameplayAbilities` は simulated proxies では実行されません。** それらは `AbilityTasks` と `GameplayCues` を使うことで、 simulated proxies に視覚的な変更をレプリケーションまたは RPC します。 Epic のDave Ratti は [将来この機能を削除する](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) ことを望んでいると述べています。

<a name="concepts-ga-definition-remotecancel"></a>

##### 4.6.1.2 Server Respects Remote Ability Cancellation （サーバーの「リモートによる Ability のキャンセル」の尊重）

このオプションは、多くの場合、問題を引き起こします。 これはクライアントの `GameplayAbility` キャンセルまたは自然完了のいずれかによって終了した場合、サーバーのバージョンを（完了していようがいまいがに依らず）強制的に終了することを意味します。 後者の問題はとりわけ大きいレイテンシのプレイヤーに使用される locally predicted （ローカル予測） された `GameplayAbilities` にとって重要です。 一般的には、このオプションは無効にすることをおすすめします。

<a name="concepts-ga-definition-repinputdirectly"></a>

##### 4.6.1.3 Replicate Input Directly （入力の直接レプリケート）

このオプションの設定は常に press と release のイベントをサーバーにレプリケーションします。 Epic はこれを使用しないことをおすすめしています。 その代わりは、（既存の入力関連の [`AbilityTasks`](#concepts-at) に組み込まれた） `Generic Replicated Events` を頼りにすることです（もしあなたが [`ASC` に入力をバインド](#concepts-ga-input) する場合）。

Epic のコメント :
```c++
/** 入力状態の直接レプリケーション。 これらは bReplicateInputDirectly が true の場合にアビリティから呼ばれ、一般的に使用に適しません。（代わりに、 Generic Replicated Events を使用するのが好ましいです。） */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-input"></a>

#### 4.6.2 Binding Input to the ASC （ASC に入力をバインド）

`ASC` は入力アクションを直接それにバインドすることを可能にし、それらの入力を `GameplayAbilities` に付与した際に割り当てます。 `GameplayAbilities` に割り当てらた入力アクションは、押された際に `GameplayTag` の要件が満たされていれば、自動的にこれらの `GameplayAbilities` を有効にします。 割り当てられた入力アクションは、入力に応答する組み込みの `AbilityTasks` を使うことを求められます。

`GameplayAbilities` の有効化のために入力アクションを割り当てることに加えて、 `ASC` は一般的な `Confirm` や `Cancel` 等の入力も受け入れます。 これらの特別な入力は、（ [`Target Actors`](#concepts-targeting-actors) などを確認したり、それらのキャンセルしたりするために、） `AbilityTasks` に使用されます。

`ASC` に入力をバインドするには、最初に、入力アクション名を 1byte に変換するための enum を作る必要があります。 その enum の名前はプロジェクトセッティング内の入力アクションで使われている名前と正確に一致する必要があります。 `DisplayName` は問題ではありません。

サンプルプロジェクトより :
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

もし `ASC` が `Character` にある場合、 `SetupPlayerInputComponent()` が、 `ASC` にバインドするための機能を含んでいます :
```c++
// Bind to AbilitySystemComponent
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"), FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

`ASC` が `PlayerState` にある場合、 `SetupPlayerInputComponent()` には潜在的に race condition （競争状態）があり、 `PlayerState` がまだクライアントへレプリケーションされていない可能性があります。 したがって、`SetupPlayerInputComponent()` と `OnRep_PlayerState()` で入力をバインドすることを試みるのをおすすめします。 `OnRep_PlayerState()` だけでは十分ではありません。 なぜなら「 `PlayerController` がクライアントに（ `InputComponent` を作成する） `ClientRestart()` を呼ぶ様に伝えるより先に `PlayerState` がレプリケーションした際」、 `Actor` の `InputComponent` が null になる可能性があるからです。 サンプルプロジェクトでは、「 bool 値でプロセスをゲーティングしたうえで、両方の場所でバインドを試み、実際には入力を一度だけバインドする」ということを実践してみせています。

**Note:** サンプルプロジェクトでは、 enum の `Confirm` と `Cancel` はプロジェクトセッティング内の入力アクションで使われている名前（ `ConfirmTarget` と `CancelTarget` ）と一致していませんが、それらの間のマッピングを `BindAbilityActivationToInputComponent()` で提供しています。 これらはマッピングを提供している特別なもので、一致するのが必須ではないですが、一致していても問題ありません。 enum 内の他のすべての入力は、プロジェクトセッティング内の入力アクションで使われている名前と一致している必要があります。

１つの入力によってのみ有効化される `GameplayAbilities` （常に同じ「スロット」に存在する、 MOBA のような）には、私は私が入力を定義できる `UGameplayAbility` のサブクラスに変数を追加するのを好みます。 アビリティを付与する時、これを `ClassDefaultObject` から読み取ることができます。

<a name="concepts-ga-input-noactivate"></a>

##### 4.6.2.1 Binding to Input without Activating Abilities （Abilities を有効化せずに入力をバインド）

入力が押された際に `GameplayAbilities` を自動的に有効化するのを望まないが、それでもやはり `AbilityTasks` を使ってそれらを入力にバインドしたいならば、新しい bool の変数（ デフォルトで `true` の `bActivateOnInput` ）を `UGameplayAbility` のサブクラスに追加し、 `UAbilitySystemComponent::AbilityLocalInputPressed()` をオーバーライドする事ができます。

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// もし InputID が GenericConfirm/Cancel でオーバーロードされ GenericConfim/Cancel コールバックがバインドされているならば、入力を 消費する
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// InputPressed イベントを呼び出す。 これはここではレプリケーションされない。 もし誰かがリッスンしているならば、それらが InputPressed イベントをサーバーにレプリケーションする可能性がある。
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// アビリティが有効ではない。なので、有効化を試みる。
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-granting"></a>

#### 4.6.3 Granting Abilities （付与）

`GameplayAbility` を `ASC` に付与すると、それが `ActivatableAbilities` の `ASC` のリストに追加され、 [`GameplayTag` の要件](#concepts-ga-tags) を満たしているならば `GameplayAbility` の有効化を自由に行うことを許可されます。

サーバーで `GameplayAbilities` を付与すると、 [`GameplayAbilitySpec`](#concepts-ga-spec) を Owning Client （所有クライアント）に、自動的にレプリケーションします。 他のクライアント / simulated proxies は `GameplayAbilitySpec` を受け取りません。

サンプルプロジェクトでは、 `Character` クラスに、ゲーム開始時に読み取り、付与する `TArray<TSubclassOf<UGDGameplayAbility>>` を格納します :
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// アビリティを付与する、サーバー上でのみ
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

これらの `GameplayAbilities` を付与する時、「 `UGameplayAbility` クラス、アビリティレベル、バインドされている入力、および「 `SourceObject` または この `GameplayAbility` をこの `ASC` に与えた人」」を基に `GameplayAbilitySpecs` を作成します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating"></a>

#### 4.6.4 Activating Abilities （Abilities の有効化）

もし `GameplayAbility` が入力アクションを割り当てられたら、入力が押され、 `GameplayTag` の要件を満たしているのであれば、自動的に有効化されるでしょう。 これは `GameplayAbility` を有効化するための方法として、常に望ましいとは限りません。 `ASC` は `GameplayAbilities` を有効化する方法をその他に４つ提供します : `GameplayTag` によって、 `GameplayAbility` クラス、 `GameplayAbilitySpec` ハンドル、そして イベントによって。 イベントによる `GameplayAbility` の有効化は [イベントとともにペイロードデータを渡す](#concepts-ga-data) ことを許可します。

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec);
```

イベントによる `GameplayAbility` の有効化は、 `GameplayAbility` が `GameplayAbility` で設定された `Triggers` を所持している必要があります。 `GameplayTag` を割り当て、 `GameplayEvent` のオプションを選択します。 イベントを送信するためには、関数 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)` を使用します。 イベントで `GameplayAbility` をアクティブ化することはペイロードデータを渡すことを許可します。

`GameplayAbility` の `Triggers` は `GameplayTag` の追加と削除の際の `GameplayAbility` の有効化も許可します。

**Note:** ブループリントのイベントから `GameplayAbility` を有効化する時、 `ActivateAbilityFromEvent` ノードを使用しなければなりません。 標準の `ActivateAbility` ノードはグラフに **存在させられません** 。 `ActivateAbility` ノードが存在すると、常に `ActivateAbilityFromEvent` を介して呼び出されます。

**Note:** パッシブアビリティのような、常に実行している `GameplayAbility` では無い限り、 `GameplayAbility` を終了させる際には `EndAbility()` を呼び出すのを忘れてはいけません。

**locally predicted （ローカル予測）** された `GameplayAbilities` の有効化シーケンス :
1. **Owning Client （所有クライアント）** が `TryActivateAbility()` を呼び出す
1. `InternalTryActivateAbility()` を呼び出す
1. `CanActivateAbility()` を呼び出し、「 `GameplayTag` の要件を満たしているか」、「 `ASC` がコストを支払えるか」、「 `GameplayAbility` がクールダウン中ではないか」、そして「現在有効な他のインスタンスがないか」を返す
1. `CallServerTryActivateAbility()` を呼び出し、生成された `Prediction Key` を渡す
1. `CallActivateAbility()` を呼び出す
1. `PreActivate()` を呼び出す、 Epic はこれを「 boilerplate init stuff （お決まりの初期化要素）」と呼んでいる
1. `ActivateAbility()` を呼び出し、最終的にアビリティが有効化される

**サーバー** が `CallServerTryActivateAbility()` を受け取る
1. `ServerTryActivateAbility()` を呼び出す
1. `InternalServerTryActivateAbility()` を呼び出す
1. `InternalTryActivateAbility()` を呼び出す
1. `CanActivateAbility()` を呼び出し、「 `GameplayTag` の要件を満たしているか」、「 `ASC` がコストを支払えるか」、「 `GameplayAbility` がクールダウン中ではないか」、そして「現在有効な他のインスタンスがないか」を返す
1. もし「サーバーによって有効化が確認されたことを `ActivationInfo` を更新するように指示」し「 `OnConfirmDelegate` デリゲートでブロードキャストする」ことに成功したら、 `ClientActivateAbilitySucceed()` を呼び出す。 これは入力確認と同じではありません。
	> Translators notes:SentyaAnko  
	> Todo:Check here
	> This translation is likely to be incorrect.  
	> I need to check the implementation of function ```UAbilitySystemComponent::InternalTryActivateAbility()```.
	>> 1. Calls `ClientActivateAbilitySucceed()` if successful telling it to update its `ActivationInfo` that its activation was confirmed by the server and broadcasting the `OnConfirmDelegate` delegate. This is not the same as input confirmation.
1. `CallActivateAbility()` を呼び出す
1. `PreActivate()` を呼び出す、 Epic はこれを「 boilerplate init stuff （お決まりの初期化要素）」と呼んでいる
1. `ActivateAbility()` を呼び出す、最終的にアビリティが有効化される

サーバーが有効化に失敗した場合はいつでも、 `ClientActivateAbilityFailed()` が呼び出され、クライアントの `GameplayAbility` を直ち終了し、 predicted （予測）された変更を元に戻します。

<a name="concepts-ga-activating-passive"></a>

##### 4.6.4.1 Passive Abilities （パッシブ Abilities）

（自動的に有効化し継続的に実行する）パッシブの `GameplayAbilities` の実装するには、（「 `GameplayAbility` が付与されて、かつ `AvatarActor` がセットされた」際に自動的に呼ばれる） `UGameplayAbility::OnAvatarSet()` をオーバーライドし、 `TryActivateAbility()` を呼び出します。

`UGameplayAbility` のカスタムクラスに `bool` 値を追加し、付与された際に `GameplayAbility` を有効化するかどうかを指定できるようにすることをおすすめします。 サンプルプロジェクトでは、パッシブのアーマースタックのアビリティのためにこれを行っています。

パッシブの `GameplayAbilities` は通常、 `Server Only` の [`Net Execution Policy`](#concepts-ga-net) となっています。

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

Epic はこの関数を「パッシブアビリティを開始し、 `BeginPlay` でやるようなことを行う」のに正しい場所と説明しています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>

#### 4.6.5 Canceling Abilities （Abilities のキャンセル）

`GameplayAbility` を内部からキャンセルするには、 `CancelAbility()` を呼び出します。 これは `EndAbility()` を呼び出し、 `WasCancelled` パラメータを true に設定します。

`GameplayAbility` を外部からキャンセルするために、 `ASC` はいくつかの関数を提供しています :

```c++
/** 指定されたアビリティ CDO (ClassDefaultObject) をキャンセルします。 */
void CancelAbility(UGameplayAbility* Ability);	

/** 渡された spec handle で示されたアビリティをキャンセルします。 もし handle が reactivated abilities の中に見つからなかったら、何も起きません。 */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** 指定されたタグを持つすべてのアビリティをキャンセルします。 Ignore インスタンスはキャンセルされません。 */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** タグに関係なくすべてのアビリティをキャンセルします。 Ignore インスタンスはキャンセルされません。 */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** すべてのアビリティをキャンセルし、残りのインスタンス化されたアビリティもすべてキルします。 */
virtual void DestroyActiveState();
```

**Note:** `CancelAllAbilities` は `Non-Instanced` `GameplayAbilities` がある場合、正常に動作していない様に見えることがわかりました。 `Non-Instanced` `GameplayAbility` がヒットし、諦めたようです。 `CancelAbilities` は `Non-Instanced` `GameplayAbilities` をより適切に処理できるので、サンプルプロジェクトではそれを利用しています（ジャンプ が non-instanced `GameplayAbility` です）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>

#### 4.6.6 Getting Active Abilities （有効な Abilities の入手）

初心者はしばしば「どのように有効なアビリティを取得できますか？」と尋ねます（おそらく変数を設定するかキャンセルするために）。 １つ以上の `GameplayAbility` を一度に有効化できますので、ただ１つの「有効なアビリティ」というものはありません。 代わりに、 `ASC` の `ActivatableAbilities` （ `ASC` が所有する付与された `GameplayAbilities` ）を検索し、探している [`Asset` または `Granted` `GameplayTag`](#concepts-ga-tags) に一致するものを見つける必要があります。

`UAbilitySystemComponent::GetActivatableAbilities()` は反復処理のために `TArray<FGameplayAbilitySpec>` を返します。

`ASC` は手動で `GameplayAbilitySpecs` のリストを反復処理をする代わりに、検索を支援するためのパラメータとして `GameplayTagContainer` を受け取る別のヘルパー関数も持っています。 `bOnlyAbilitiesThatSatisfyTagRequirements` パラメータは `GameplayTag` の要件を満たし、今すぐ有効化できる `GameplayAbilitySpecs` のみを返します。 たとえば、２つの通常攻撃の `GameplayAbilities` をもっていて、一方は武器を使いもう一方は素手を使うものがある場合、武器が装備されているかに応じて、 `GameplayTag` の要件を設定して、正しい方を有効化します。 詳細については、この関数に関する Epic のコメントを参照してください。

```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

目的の `FGameplayAbilitySpec` を探しだしたら `IsActive()` を呼び出すことができます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-instancing"></a>

#### 4.6.7 Instancing Policy （インスタンス化ポリシー）

`GameplayAbility` の `Instancing Policy` は `GameplayAbility` がアクティブ化された時に、インスタンス化されるかどうか、どの様にインスタンス化されるかを決定します。

| `Instancing Policy`     | 説明                                                                                        | いつ使用するかの例                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ----------------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Instanced Per Actor     | 各 `ASC` は有効化の際に再利用される `GameplayAbility` の唯一のインスタンスを持つ。          | おそらく最も利用される `Instancing Policy` です。 どんなアビリティにも利用でき、有効化の持続性を提供します。 設計者は、有効化で必要となるどんな変数も、各有効化の間で、手動でリセットする責任があります。                                                                                                                                                                                                                             |
| Instanced Per Execution | `GameplayAbility` が有効化するたびに、新しい `GameplayAbility` のインスタンスが作成される。 | これらの `GameplayAbilities` の利点は、有効化のたびに変数がリセットされることです。 これらは有効化のたびに新しい `GameplayAbilities` をスポーンするため、 `Instanced Per Actor` より悪い効率を提供します。 サンプルプロジェクトでは全く使用していません。                                                                                                                                                                             |
| Non-Instanced           | `GameplayAbility` は `ClassDefaultObject` 上で動作する。インスタンスは作成されない。        | これは３つの中で最も効率が良いですが、これを使用してできることが最も限定的です。 `Non-Instanced` な `GameplayAbilities` は状態の保存ができません。つまり、動的な変数も、 `AbilityTask` デリゲートへのバインドも利用できません。 これを使う最も良い場所は、 MOBA や RTS のミニオンの通常攻撃のような、頻繁に使われるシンプルなアビリティです。 サンプルプロジェクトでは、ジャンプの `GameplayAbility` が `Non-Instanced` となっています。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-net"></a>

#### 4.6.8 Net Execution Policy

`GameplayAbility` の `Net Execution Policy` は誰がどのような順番で `GameplayAbility` を実行するかを決定します。

| `Net Execution Policy` | 説明                                                                                                                                                                                                                                                                                    |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           | `GameplayAbility` は Owning Client （所有クライアント）のみで実行されます。 これは局所的な装飾用の変更のみを行うアビリティに役立つ可能性があります。 シングルプレイヤーゲームでは `Server Only` を使用する必要があります。                                                              |
| `Local Predicted`      | `Local Predicted` の `GameplayAbilities` の有効化は、まず Owning Client （所有クライアント）でされ、その後サーバーでされます。 サーバー版はクライアントが誤って predicted （予測）したものをすべて修正します。 詳細については、 [Prediction （予測）](#concepts-p) を参照してください。 |
| `Server Only`          | `GameplayAbility` はサーバーのみで実行されます。 パッシブな `GameplayAbilities` は 通常 `Server Only` を使用します。 シングルプレイヤーゲームではこれを使う必要があります。                                                                                                             |
| `Server Initiated`     | `Server Initiated` の `GameplayAbilities` の有効化は、まずサーバでされ、その後 Owning Client （所有クライアント）でされます。 個人的にはあまり使ったことがありません。                                                                                                                  |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-tags"></a>

#### 4.6.9 Ability Tags

`GameplayAbilities` にはロジックが組み込まれた `GameplayTagContainers` が付属しています。 これらの `GameplayTags` はレプリケーションできません。

| `GameplayTag Container`     | 説明                                                                                                                                                                                                                    |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayAbility` が所有する `GameplayTags` です。 これらは `GameplayAbility` を説明するための単なる `GameplayTags` です。                                                                                              |
| `Cancel Abilities with Tag` | この `GameplayAbility` が有効化される際に、これらの `GameplayTags` が `Ability Tags` に含まれている他の `GameplayAbilities` はキャンセルされます。                                                                      |
| `Block Abilities with Tag`  | この `GameplayAbility` が有効な間、これらの `GameplayTags` が `Ability Tags` に含まれている他の `GameplayAbilities` は有効化を阻害されます。                                                                            |
| `Activation Owned Tags`     | この `GameplayAbility` が有効な間、これらの `GameplayTags` は `GameplayAbility` の所有者に与えられます。 これらはレプリケーションされないことに注意が必要です。                                                         |
| `Activation Required Tags`  | この `GameplayAbility` は、所有者がこれらの `GameplayTags` を **すべて** 持っている場合のみ有効化できます。                                                                                                             |
| `Activation Blocked Tags`   | この `GameplayAbility` は、所有者がこれらの `GameplayTags` の **いずれか** を持っている場合、有効化できません。                                                                                                         |
| `Source Required Tags`      | この `GameplayAbility` は、 `Source` がこれらの `GameplayTags` を **すべて** 持っている場合のみ有効化できます。 `Source` `GameplayTags` は、 `GameplayAbility` がイベントに依ってトリガされた場合のみ設定されます。     |
| `Source Blocked Tags`       | この `GameplayAbility` は、 `Source` がこれらの `GameplayTags` の **いずれか** を持っている場合、有効化できません。 `Source` `GameplayTags` は、 `GameplayAbility` がイベントに依ってトリガされた場合のみ設定されます。 |
| `Target Required Tags`      | この `GameplayAbility` は、 `Target` がこれらの `GameplayTags` を **すべて** 持っている場合のみ有効化できます。 `Target` `GameplayTags` は、 `GameplayAbility` がイベントに依ってトリガされた場合のみ設定されます。     |
| `Target Blocked Tags`       | この `GameplayAbility` は、 `Target` がこれらの `GameplayTags` の **いずれか** を持っている場合、有効化できません。 `Target` `GameplayTags` は、 `GameplayAbility` がイベントに依ってトリガされた場合のみ設定されます。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-spec"></a>

#### 4.6.10 Gameplay Ability Spec

`GameplayAbilitySpec` は、「 `GameplayAbility` が付与された後に `ASC` 上に存在」し、「有効化可能な `GameplayAbility` （ `GameplayAbility` クラス、レベル、バインドされている入力、そして `GameplayAbility` クラスから分離された実行時の状態） を定義」しています。

`GameplayAbility` がサーバー上で付与された場合、サーバーはその `GameplayAbilitySpec` を Owning Client （所有クライアント）にレプリケーションし、そちらで有効化できるようにします。

`GameplayAbilitySpec` の有効化は、 `GameplayAbility` の `Instancing Policy` に従い、（ `Non-Instanced` `GameplayAbilities` の場合を除き）インスタンスを作成します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-data"></a>

#### 4.6.11 Passing Data to Abilities （Abilities へのデータの受け渡し）

`GameplayAbilities` の一般的なパラダイムは `Activate->Generate Data->Apply->End （有効化->データ生成->適用->終了）` です。 場合によっては既存のデータに基づいて行う必要があります。 GAS は外部データを `GameplayAbilities` に取り込むためのいくつかのオプションを提供します :

| Method                                          | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ----------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Activate `GameplayAbility` by Event             | ペイロードデータを含めたイベントによる `GameplayAbility` の有効化。 イベントのペイロードは local predicted （ローカル予測）された `GameplayAbilities` のためにクライアントからサーバーにレプリケーションされます。 既存の変数に適合しない任意のデータには、２つの `Optional Object` か、 [`TargetData`](#concepts-targeting-data) 変数を使用します。 この方法の欠点は、入力のバインドによるアビリティの有効化を妨げることです。 イベントごとに `GameplayAbility` を有効化するには `GameplayAbility` 内で `Triggers` の設定が必要です。 `GameplayTag` の割り当てと `GameplayTag` のオプションの選択をします。 イベントを送信するには、関数 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)` を使用します。 |
| Use `WaitGameplayEvent` `AbilityTask`           | `WaitGameplayEvent` `AbilityTask` を使用して、有効化後にペイロードデータを含むイベントをリッスンするように`GameplayAbility` に指示します。 イベントのペイロードとそれを送信するプロセスは、「 Activate `GameplayAbility` by Event 」と同じです。 この方法の欠点は、イベントが `AbilityTask` によりレプリケーションされず、`Local Only` と `Server Only` の `GameplayAbilities` でのみ使う必要があることです。 イベントペイロードをレプリケーションする独自の `AbilityTask` を作成できる可能性があります。                                                                                                                                                                                                                                                                         |
| Use `TargetData`                                | カスタムの `TargetData` 構造体は、クライアントとサーバー間で任意のデータを送る良い方法です。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Store Data on the `OwnerActor` or `AvatarActor` | `OwnerActor` 、 `AvatarActor` 、 または参照が取得可能なその他のオブジェクトに保存された、レプリケーションされた変数を使用します。 このメソッドは最も柔軟性があり、入力のバインドにより有効化された `GameplayAbilities` で機能します。 ただし、データを使用する際に、レプリケーションにより同期されている保証がありません。 あなたは事前に確認すべきです。つまり、レプリケーションされている変数を設定し即座に `GameplayAbility` を有効化した場合、レシーバーで発生しうる潜在的なパケットロスのため、処理順序の保証がありません。                                                                                                                                                                                                                                                    |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-commit"></a>

#### 4.6.12 Ability Cost and Cooldown

`GameplayAbilities` にはオプションのコストとクールダウンのための機能が付属しています。 コストとは、 `Instant` `GameplayEffect` ([`Cost GE`](#concepts-ge-cost)) で実装された、 `GameplayAbility` を有効化するために `ASC` が所持する必要がある、予め決められた `Attributes` の量です。 クールダウンとは、 `Duration` `GameplayEffect` ([`Cooldown GE`](#concepts-ge-cooldown)) で実装された、期限が切れるまで `GameplayAbility` の再有効化を防ぐタイマーです。

`GameplayAbility` が `UGameplayAbility::Activate()` を呼ぶ前に、 `UGameplayAbility::CanActivateAbility()` を呼びます。 この関数は所有する `ASC` がコストを支払えるか(`UGameplayAbility::CheckCost()`) を調べ、 `GameplayAbility` がクールダウン中ではない (`UGameplayAbility::CheckCooldown()`) ことを確認します。

`GameplayAbility` が `Activate()`を呼び出した後、（ `UGameplayAbility::CommitCost()` と `UGameplayAbility::CommitCooldown()` を呼び出す `UGameplayAbility::CommitAbility()` を使用して、）コストとクールダウンをいつでもオプションでコミットできます。 設計者は同時にコミットされる必要がない場合は、 `CommitCost()` または `CommitCooldown()` を個別に呼び出すことを選択できます。 コストとクールダウンのコミットは、もう一度 `CheckCost()` と `CheckCooldown()` を呼び出し、それらに関連して`GameplayAbility` を失敗させる最後のチャンスです。 所有している `ASC` の `Attributes` は `GameplayAbility` が有効化された後に変更されている可能性が潜在的にあり、コミット時のコストを満たせない可能性があります。 コストとクールダウンのコミットは、 [prediction key](#concepts-p-key) がコミット時に有効ならば、 [locally predicted （ローカル予測）](#concepts-p) できます。

実装の詳細については [`CostGE`](#concepts-ge-cost) と [`CooldownGE`](#concepts-ge-cooldown) を参照してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-leveling"></a>

#### 4.6.13 Leveling Up Abilities

アビリティのレベルアップの２つの一般的な方法 :

| Level Up Method                            | 説明                                                                                                                                                                                                                                                   |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Ungrant and Regrant at the New Level       | `GameplayAbility` を `ASC` から付与解除（削除）し、サーバー上の次のレベルで再付与する。 この方法はその時点で有効だった `GameplayAbility` がある場合、それを終了させます。                                                                              |
| Increase the `GameplayAbilitySpec's` Level | サーバーで、 `GameplayAbilitySpec` を探し、そのレベルを増やし、ダーティとしてマークし、 Owning Client （所有クライアント）にレプリケーションされるようにする。 この方法はその時点で有効だった `GameplayAbility` がある場合でも、それを終了させません。 |

この２つの方法の主な違いは、レベルアップ時に有効な `GameplayAbilities` をキャンセルするかどうかです。 `GameplayAbilities` に応じて、両方の方法を使用する可能性があります。 `UGameplayAbility` サブクラスに `bool` 値を追加し、どちらの方法を取るかを指定することをおすすめします。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-sets"></a>

#### 4.6.14 Ability Sets

`GameplayAbilitySets` は、「入力のバインドを保持するための便利な `UDataAsset` クラス」であり、「 `GameplayAbilities` を付与するロジックを持つ、キャラクターのスタートアップの `GameplayAbilities` のリスト」です。 サブクラスは 追加のロジックやプロパティを含めることもできます。 Paragon はヒーロー毎に `GameplayAbilitySet` を持っており、そこに与えられたすべての `GameplayAbilities` を含めていました。

私は、少なくともこれまで見てきたものを考えると、このクラスは不要と見出しています。 サンプルプロジェクトでは、 `GDCharacterBase` とそのサブクラスの中で、 `GameplayAbilitySets` のすべての機能を扱っています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-batching"></a>

#### 4.6.15 Ability Batching （Ability のバッチ処理）

従来の `Gameplay Ability` のライフサイクルには、 クライアントからサーバーへの RPC が少なくとも２つか３つ必要とします。

1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()` (Optional)
1. `ServerEndAbility()`

もし `GameplayAbility` が、フレーム内で唯一の小さなグループでこれらすべてのアクションを行う場合、このワークフローを（２つか３つの RPC すべてを１つの RPC にバッチ処理（統合）することで）最適化できます。 `GAS` はこの RPC の最適化を `Ability Batching` と呼びます。 `Ability Batching` を使用できる一般的な例は、ヒットスキャンガンです。 ヒットスキャンガンは、有効化し、ライントレースを実行し、 [`TargetData`](#concepts-targeting-data) をサーバーに送信し、アビリティを終了します（これらすべてをフレーム内で唯一の小さなグループでおこないます）。 [GASShooter](https://github.com/tranek/GASShooter) のサンプルプロジェクトではヒットスキャンガンのこの手法を実践してみせています。

セミオート銃は最良のシナリオで、 `CallServerTryActivateAbility()` と `ServerSetReplicatedTargetData()` （銃弾の命中結果） と `ServerEndAbility()` の３つの RPC の代わりに１つの RPC にバッチ処理できます。

フルオート/バースト銃は、最初の銃弾の `CallServerTryActivateAbility()` と `ServerSetReplicatedTargetData()` の２つの RPC の代わりに１つの RPC にバッチ処理できます。 後続の各銃弾は独自の `ServerSetReplicatedTargetData()` RPC です。 最後に、銃の発砲が停止した時に、個別の RPC として `ServerEndAbility()` が送られます。 これは最悪のシナリオで、最初の銃弾の２つの代わりに１つの RPC しか節約できません。 このシナリオは、[`Gameplay Event`](#concepts-ga-data) を介してアビリティの有効化することで実装することもできます。 その場合、銃弾の `TargetData` が `EventPayload` と共にクライアントからサーバーへ送信されます。 後述のアプローチの欠点は、 `TargetData` の生成をアビリティの外部でしなければならない点です（反対に、バッチ処理のアプローチでは `TargetData` の生成はアビリティの内部でされます）。

`Ability Batching` はデフォルトでは無効になっています（ [`ASC`](#concepts-asc) 上で）。 `Ability Batching` を有効にするには、 `ShouldDoServerAbilityRPCBatch()` をオーバーライドし、 true を返すようにします :

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

これで `Ability Batching` が有効になりましたが、バッチ処理したいアビリティの有効化の前に、 `FScopedServerAbilityRPCBatcher` 構造体を事前に作成する必要があります。 この特別な構造体は、そのスコープ内で続くすべてのアビリティのバッチ処理を試みます。 ひとたび `FScopedServerAbilityRPCBatcher` のスコープから外れると、すべてのアビリティの有効化はバッチ処理の試みをされなくなります。 `FScopedServerAbilityRPCBatcher` はバッチ処理可能な各関数の中に所持している特別なコードで動作しており、 RPC の送信からの呼び出しに割り込み、代わりにバッチ処理用の構造体にメッセージをパックします。 `FScopedServerAbilityRPCBatcher` のスコープから外れると自動的に、このバッチ処理用の構造体が `UAbilitySystemComponent::EndServerAbilityRPCBatch()` によってサーバーにRPCで送られます。 サーバーは `UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)` でバッチ処理の RPC を受信します。 `BatchInfo` パラメータは、「アビリティが終了すべきか」、「有効化された時に入力が押されたか」のフラグと、 `TargetData` （含まれていれば）が包含されています。 これは、バッチ処理が適切に動作しているかを確認するためにブレークポイントを設定するのに良い関数です。 あるいは、 cvar （コンソール変数）の `AbilitySystem.ServerRPCBatching.Log 1` を使用することで、アビリティのバッチング処理の特別なログを有効にすることができます。

このメカニズムは C++ でのみ実行でき、 `FGameplayAbilitySpecHandle` によってのみアビリティの有効化ができます。

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooter では、セミオートとフルオートの銃に、同じバッチ処理された `GameplayAbility` を再利用し、またそれは直接 `EndAbility()` を呼び出しません（「プレイヤーの入力」と「現在の発砲モードに基づいてバッチ処理されたアビリティの呼び出し」を管理するローカルオンリーのアビリティによって、アビリティの外部で処理されます）。 すべての RPC は `FScopedServerAbilityRPCBatcher` のスコープの内側で発生する必要があるため、私は `EndAbilityImmediately` パラメータを提供することでこのアビリティが 「 `EndAbility()` の呼び出しをバッチ処理すべき（セミオート）」か、「 `EndAbility()` の呼び出しをバッチ処理しないべき（フルオート）で `EndAbility()` を後に RPC を独自に発生させる」かをローカルオンリーでの制御/管理を明示できるようにしています。

GASShooter はブループリントノードを公開し、（前述のローカルオンリーのアビリティが、バッチ処理されたアビリティを発動するのに使用する）アビリティのバッチ処理を可能にしています。

![Activate Batched Ability](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>

#### 4.6.15 Net Security Policy （ネットセキュリティポリシー）

`GameplayAbility` の `NetSecurityPolicy` はアビリティの実行をネットワーク上のどこで行うべきかを決定します。 クライアントの「制限されたアビリティの実行の企て」からの保護を提供します。

| `NetSecurityPolicy`     | 説明                                                                                                                                                                  |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | セキュリティ要件はありません。 クライアントまたはサーバーは、このアビリティの実行と終了を自由に発動できます。                                                         |
| `ServerOnlyExecution`   | このアビリティの実行を要求しているクライアントはサーバーにより無視されます。 クライアントは引き続き、サーバーにこのアビリティのキャンセルまたは終了の要求ができます。 |
| `ServerOnlyTermination` | このアビリティのキャンセルまたは停止を要求しているクライアントはサーバーにより無視されます。 クライアントは引き続き、アビリティの実行の要求ができます。               |
| `ServerOnly`            | サーバーがアビリティの実行と終了の両方を制御します。 リクエストを行ったクライアントは無視されます。                                                                   |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at"></a>

### 4.7 Ability Tasks

<a name="concepts-at-definition"></a>

### 4.7.1 Ability Task の定義

`GameplayAbilities` は１フレームでのみ実行されます。 これだけではあまりに柔軟性がありません。 「時間の経過とともに発生する」または「後のある時点で発火されたデリゲートからの応答が必要になる」アクションを実行するために、 `AbilityTasks` と呼ばれる潜伏的なアクションを使用します。

GAS はすぐに利用できる多くの `AbilityTasks` が付属しています :
* `RootMotionSource` を伴ったキャラクターの移動のためのタスク
* アニメーションモンタージュを再生するためのタスク
* `Attribute` の変更に対応するためのタスク
* `GameplayEffect` の変更に対応するためのタスク
* プレイヤーの入力に対応するためのタスク
* その他色々

`UAbilityTask` のコンストラクタはハードコードされたゲーム全体で最大 1000 の同時発生の `AbilityTasks` の実行を同時に実施します。
RTS ゲームのような、数百のキャラクターがワールドに同時に保持されるされるようなゲーム用の `GameplayAbilities` を設計する際には、このことに留意する必要があります。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-definition"></a>

### 4.7.2 Custom Ability Tasks （Ability Tasks のカスタム）

多くの場合、独自のカスタム `AbilityTasks` (in C++) を作ることになります。 サンプルプロジェクトには２つのカスタム `AbilityTasks` が付属しています :
1. `PlayMontageAndWaitForEvent` はデフォルトの `PlayMontageAndWait` と `WaitGameplayEvent` `AbilityTasks` の組み合わせです。 これにより、アニメーションモンタージュに gameplay events を `AnimNotifies` からそれを開始した `GameplayAbility` に送り返すのを可能にします。 これを使うことにより、アニメーションモンタージュ中の特定の時間にアクションが発動できます。
1. `WaitReceiveDamage` は `OwnerActor` がダメージを受けるのをリッスンします。 パッシブのアーマースタックの `GameplayAbility` はヒーローがダメージのインスタンスをけ取ると、アーマーのスタックをひとつ削除します。

`AbilityTasks` は以下で構成されています :
* `AbilityTask` の新しいインスタンスを作成する静的関数
* `AbilityTask` がその目的を達成した際にブロードキャストされるデリゲート
* メインジョブを開始したり、外部のデリゲートにバインドしたり、その他色々するための `Activate()` 関数
* バインドされた外部デリゲートを含むクリーンアップ用の `OnDestroy()` 関数
* バインドされた外部デリゲートのコールバック関数
* メンバ変数と内部ヘルパー関数

**Note:** `AbilityTasks` は１つのタイプの出力デリゲートのみを定義できます。 パラメータを使用していようがいまいがに関わらず、すべての出力デリゲートはこのタイプである必要があります。 未使用のデリゲートのパラメータはデフォルトの値を渡します。

`AbilityTasks` は所有している `GameplayAbility` を実行しているクライアントまたはサーバーのみで実行されます ; しかしながら、`AbilityTasks` はシミュレートされたクライアント上で実行するようにも設定できます。 そのためには `AbilityTask` のコンストラクタ内で `bSimulatedTask = true;` を設定し、 `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);` をオーバーライドし、レプリケーションするすべてのメンバ変数の設定を行います。 これはまれなシチュエーションでのみ役立ちます。 たとえば移動の `AbilityTasks` のように、すべての移動の更新をレプリケーションするのではなく、その代わりに移動の `AbilityTask` 全体をシミュレートしたい場合などです。 すべての `RootMotionSource` `AbilityTasks` はこれを行います。 例として `AbilityTask_MoveToLocation.h/.cpp` を参照してください。

`AbilityTasks` は `Tick` を利用できます。 そのためには `AbilityTask` のコンストラクタで `bTickingTask = true;` を設定し、 `virtual void TickTask(float DeltaTime);` をオーバーライドします。 これはフレーム間でスムースに値を lerp する必要がある場合に役立ちます。 例として `AbilityTask_MoveToLocation.h/.cpp` を参照してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-using"></a>

### 4.7.3 Using Ability Tasks

C++ での `AbilityTask` の作成と有効化を行う方法（ `GDGA_FireGun.cpp` より） :

```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```
ブループリントでは、 `AbilityTask` のために作成したブループリントノードを使用するだけです。 `ReadyForActivate()` を呼び出す必要はありません。 `Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp` によって自動的に呼び出されます。 `AbilityTask` クラスに存在すれば、`K2Node_LatentGameplayTaskCall` も 自動的に `BeginSpawningActor()` と `FinishSpawningActor()` を呼び出します（ `AbilityTask_WaitTargetData` を参照してください）。 何度もの繰り返しになりますが、 `K2Node_LatentGameplayTaskCall` の自動実行の魔法はブループリントの時のみ行われます。 C++ では、手動で `ReadyForActivation()` と `BeginSpawningActor()` と `FinishSpawningActor()` を呼び出す必要があります。

![Blueprint WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

`AbilityTask` を手動でキャンセルするには、ブループリント（ `Async Task Proxy` と呼ばれます ）または C++ から `AbilityTask` オブジェクトで `EndTask()` を呼び出すだけです。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-rms"></a>

### 4.7.4 Root Motion Source Ability Tasks （ルートモーションソースを用いた Ability Tasks）

GAS には、 `CharacterMovementComponent` にフックされた `Root Motion Sources` を使用して（ノックバック、複雑なジャンプ、プル、ダッシュなどの）`Characters` を時間の経過とともに移動させるための、 `AbilityTasks` が付属しています。

**Note:** `RootMotionSource` `AbilityTasks` の Predicting （予測）はエンジンバージョンが 4.19 及び 4.25 以降で動作します。 エンジンバージョンが 4.20-4.24 では Prediction （予測） にバグがあります ; しかしながら、 `AbilityTasks` は、マルチプレイヤーではマイナーなネット修正を加えれば引き続き動作しますし、シングルプレイヤーでは完全に動作します。 [prediction fix （予測の修正）](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c) を 4.25 から 4.20-4.24 のカスタムエンジンへのチェリーピックが可能です。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc"></a>

### 4.8 Gameplay Cues

<a name="concepts-gc-definition"></a>

#### 4.8.1 Gameplay Cue の定義

`GameplayCues` (`GC`) はサウンドエフェクト、パーティクルエフェクト、カメラシェイク、等々のような、ゲームプレイに影響しない事柄を実行します。 `GameplayCues` は例によってレプリケーション（明示的に `Executed` または `Added` または `Removed` がローカルにない場合に限り）と predicted （予測）をされます。

「 **必須の親の名前である `GameplayCue.`** と一致する `GameplayTag` 」と、「イベントタイプ（ `Execute` または `Add` または `Remove` ）」を、 `ASC` を介して `GameplayCueManager` に送ることによって、`GameplayCues` は発動されます。 `GameplayCueNotify` のオブジェクトと `IGameplayCueInterface` を実装しているその他の `Actors` は、`GameplayCue` の `GameplayTag` (`GameplayCueTag`) に基づいてこれらのイベントを購読できます。

**Note:** 何度もの繰り返しになりますが、`GameplayCue` の `GameplayTags` は `GameplayCue` という親の `GameplayTag` で始まる必要があります。 したがって、たとえば、有効な `GameplayCue` の `GameplayTag` は `GameplayCue.A.B.C` のようになります。

２つの `GameplayCueNotifies` クラスがあります。 `Static` と `Actor` です。 それらはそれらが発動可能な、異なるイベントと異なる種類の `GameplayEffects` に応答します。 対応するイベントをロジックでオーバーライドします。

| `GameplayCue` Class                                                                                                                  | Event             | `GameplayEffect` Type    | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` or `Periodic`  | Static の `GameplayCueNotifies` は `ClassDefaultObject` （インスタンスが存在しないことを意味します） で動作し、ヒットインパクトのような一回限りのエフェクトに最適です。                                                                                                                                                                                                                                                                                                                                                                                             |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` or `Remove` | `Duration` or `Infinite` | Actor の `GameplayCueNotifies` は、 `Added` の場合、新しいインスタンスをスポーンします。 インスタンス化されているため、それらは `Removed` されるまで、時間の経過とともにアクションを実行できます。 これらは「バッキング `Duration` または `Infinite` `GameplayEffect` が削除されるか手動で削除を呼ばれた際」に削除される、ループするサウンドやパーティクルエフェクトに適しています。 これらには、同じエフェクトの複数の適用でサウンドやパーティクルを一度だけ開始するように、同時にどのぐらい `Added` するのを許容するかを管理するオプションも付属しています。 |

`GameplayCueNotifies` は技術的には全てのイベントに応答できますが、これは通常我々がこれらをどの様に使用しているかです。

**Note:** `GameplayCueNotify_Actor` を使用している時、 `Auto Destroy on Remove` を有効にしてください。 さもないと、その後の `Add` の呼び出しで、`GameplayCueTag` が機能しなくなります。

`Full` 以外の `ASC` [Replication Mode](#concepts-asc-rm) を使用すると、 `Add` と `Remove` `GC` イベントがサーバープイレイヤー（リッスンサーバー）で二回発火します - 一回は `GE` への適用の際で、もう一回は「 Minimal 」 `NetMultiCast` からクライアントへの適用の際です。 ただし、 `WhileActive` イベントが発火するのは一度だけです。 クライアントでは、全てのイベントは一度だけ発火します。

サンプルプロジェクトには、スタンとスプリントのエフェクト用の `GameplayCueNotify_Actor` が含まれます。 また、FireGun の投射のインパクト用の `GameplayCueNotify_Static` もあります。 これらの `GCs` は、 `GE` を介したレプリケーションの代わりに [triggering them locally](#concepts-gc-local) でさらに最適化できます。 私はサンプルプロジェクトではそれらを使用する初心者の方法を示すことを選びました。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-trigger"></a>

#### 4.8.2 Triggering Gameplay Cues

`GameplayEffect` の内側から、（適用が成功した（タグや immunity （耐性）によって阻害されなかった ）時、）発動すべきすべての `GameplayCues` の `GameplayTags` が詰め込まれます。

![GameplayCue Triggered from a GameplayEffect](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` は、 `GameplayCues` を `Execute` または `Add` または `Remove` するためのブループリントノードを提供します。

![GameplayCue Triggered from a GameplayAbility](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

C++ では、 `ASC` の関数を直接呼び出す（もしくは `ASC` のサブクラスでブループリントに公開する）ことができます :

```c++
/** GameplayCues は 独自に提供することも可能です。 これらは（命中結果等々を受け渡すために）オプションのエフェクトコンテキストを受け取ります。 */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 永続的な gameplay cue の追加 */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 永続的な gameplay cue の削除 */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** 自身によって追加されたすべての GameplayCue （つまりは、 GameplayEffect の一部ではないもの）の削除。 */
void RemoveAllGameplayCues();
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-local"></a>

#### 4.8.3 Local Gameplay Cues （ローカル Gameplay Cues）

`GameplayAbilities` と `ASC` から `GameplayCues` を発火するための公開された関数は、デフォルトでレプリケーションされます。 各 `GameplayCue` イベントは RPC をマルチキャストします。 このことは膨大な RPCs を発生させます。 GAS はまた、ネットの更新のたびに最大で２つの同じ `GameplayCue` の RPCs も実施します。 可能な限りローカルの `GameplayCues` を使用することで、これを回避しています。 ローカルの `GameplayCues` は `Execute` または `Add` または `Remove` のみを個々のクライアント上で行えます。

ローカルの `GameplayCues` を使用できるシナリオ :
* 投射物のインパクト
* 近接衝突のインパクト
* アニメーションモンタージュから発火された `GameplayCues`

あなたの `ASC` サブクラスに追加する必要があるローカルの `GameplayCue` の関数 :

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
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

もし `GameplayCue` がローカルで `Added` されたのであれば、ローカルで `Removed` される必要があります。 もしレプリケーションを介して `Added` されたのであれば、レプリケーションを介して `Removed` される必要があります。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-parameters"></a>

#### 4.8.4 Gameplay Cue Parameters

`GameplayCues` は`GameplayCue` への追加の情報を包含した `FGameplayCueParameters` 構造体をパラメータとして受信します。 もし `GameplayAbility` または `ASC` 上の関数から `GameplayCue` を手動で発動したのであれば、 `GameplayCue` に渡される `GameplayCueParameters` 構造体を手動で詰め込む必要があります。 もし `GameplayCue` が `GameplayEffect` から発動したのであれば、以下に続く変数は `GameplayCueParameters` 構造体に自動的に詰め込まれます :
* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude （`GameplayEffect` が 「 `GameplayCue` タグコンテナ上にあるドロップダウンにある magnitude の `Attribute` 」と「その `Attribute` に影響を与える対応する `Modifier` 」を持っている場合）

`GameplayCueParameters` 構造体の `SourceObject` 変数は、 `GameplayCue` を手動で発動した際に、 `GameplayCue` に任意のデータを渡すのに潜在的に良い場所です。

**Note:** `Instigator` のような、このパラメータ用の構造体のいくつかの変数は、既に `EffectContext` に存在している可能性があります。 `EffectContext` はワールド内の `GameplayCue` をスポーンさせる座標ために `FHitResult` を含めることもできます。 `EffectContext` のサブクラス化は、（特に `GameplayEffect` に発動される）もっと多くのデータを `GameplayCues` に渡すのに潜在的に良い方法です。

詳細については、 `GameplayCueParameters` 構造体の設定をする [`UAbilitySystemGlobals`](#concepts-asg) の下記の３つの関数を参照してください。 仮想関数なので、それらをオーバーライドすることでより多くの情報を自動設定することができます。

```c++
/** GameplayCue パラメータの初期化 */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-manager"></a>

#### 4.8.5 Gameplay Cue Manager

デフォルトでは、 `GameplayCueManager` はゲームのディレクトリ全体から `GameplayCueNotifies` を走査し、プレイ時にそれをメモリにロードします。 `DefaultGame.ini` で設定することで、 `GameplayCueManager` が取り込むパスを変更できます。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

`GameplayCueManager` がすべての `GameplayCueNotifies` を取り込み検索することを望んでいます ; しかしながら、プレイ時に１つ残らず非同期でロードすることは望ましくありません。 これにより、レベルで使用されているかに関わらず、すべての `GameplayCueNotify` と、それらが参照しているすべてのサウンドとパーティクルをメモリに配置します。 Paragon のような大規模のゲームでは、これはメモリ上の数百メガバイトの不要のアセットであり、起動時にヒッチングとゲームのフリーズの原因になります。

起動時にすべての `GameplayCue` を非同期にロードする代案は、ゲーム中にそれらが発動された `GameplayCues` のみを非同期にロードすることです。 これは「不要なメモリの使用」と、「すべての `GameplayCue` の非同期のロードの間のゲームの激しいフリーズの可能性」を軽減しますが、それと引き換えにプレイの間ずっと、特定の `GameplayCue` が初回に発動した際のエフェクトの遅延の可能性が発生します。 この潜在的な遅延は SSDs では存在しません。 HDD ではテストしていません。 もしこのオプションを UE Editor で使用するならば、僅かなヒッチまたはフリーズが GameplayCues の初回のロード中に発生する可能性があります（エディタがパーティクルシステムのコンパイルが必要な場合）。 ビルドではパーティクルシステムがすでにコンパイルされているため、問題になりません。

まず `UGameplayCueManager` をサブクラス化し、 `UGameplayCueManager` サブクラスを使用するように `DefaultGame.ini` で `AbilitySystemGlobals` クラスに指示します。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```
`UGameplayCueManager` のサブクラスで `ShouldAsyncLoadRuntimeObjectLibraries()` をオーバーライドします。

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-prevention"></a>

#### 4.8.6 Prevent Gameplay Cues from Firing （Gameplay Cues の発火防止）

時には `GameplayCues` を発火させたくないこともあります。 たとえば、攻撃をブロックした際、ダメージ `GameplayEffect` に付随した命中のインパクト再生したくなかったり、あるいは代わりにカスタムなものを再生したいことがあります。 これは、 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) の内側で、 `OutExecutionOutput.MarkGameplayCuesHandledManually()` を呼び出した後、 `Target` または `Source` の `ASC` に `GameplayCue` イベントを手動で送ることで実行できます。

もし、特定の `ASC` で、いかなる `GameplayCues` も発火させたくないならば、 `AbilitySystemComponent->bSuppressGameplayCues = true;` を設定することができます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-batching"></a>

#### 4.8.7 Gameplay Cue Batching （Gameplay Cue のバッチ処理）

`GameplayCue` が発動される度に信頼性の低い NetMulticast RPC が発生します。 複数の `GCs` を同時に発火する状況では、それらを１つの RPC に凝縮したり、より小さなデータを送ることで帯域幅を抑えたりするための最適化方法がいくつかあります。

<a name="concepts-gc-batching-manualrpc"></a>

##### 4.8.7.1 Manual RPC

８つの散弾を発射するショットガンがあるとします。 これは、８つのトレースとインパクトの `GameplayCues` となります。 [GASShooter](https://github.com/tranek/GASShooter) は、すべてのトレース情報を [`EffectContext`](#concepts-ge-ec) に [`TargetData`](#concepts-targeting-data) として蓄えることでそれらを１つの RPC に結合する怠惰な取り組み方をしています。 これにより RPCs を８つから１つに減らしますが、引き続きその１つの RPC (~500 bytes) により、大量のデータをネットワークを介し送信します。さらなる最適化の方法は１つの RPC で効率的にエンコードした命中座標をカスタム構造体で送信するか、あるいは受信側でインパクトの座標を再作成/概算するための乱数のシード値を与えることです。 次に、クライアントはこのカスタム構造体を展開し、 [ローカル実行される `GameplayCues`](#concepts-gc-local) へ戻します。

どの様にしてこれが機能するか :
1. `FScopedGameplayCueSendContext` を宣言する。 これによりスコープから外れるまで、 `UGameplayCueManager::FlushPendingCues()` が抑制されます。 これは `FScopedGameplayCueSendContext` がスコープから外れるまで、すべての `GameplayCues` はキューに追加されることを意味します。
1. `UGameplayCueManager::FlushPendingCues()` をオーバーライドして、（カスタム `GameplayTag` に基づきバッチ処理できる） `GameplayCues` をカスタム構造体にマージし、クライアントに RPC します。
1. クライアントはそのカスタム構造体を受け取り、ローカル実行される `GameplayCues` に展開します。

この方法は `GameplayCueParameters` が提供するものに適さない、特定のパラメーターが `GameplayCues` で必要で、`EffectContext` にそれらを追加したくない場合にも利用できます。 たとえば、ダメージ値、クリティカルのインジケータ、壊れたシールドのインジケータ、致命的な命中のインジケータ、等々です。

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>

##### 4.8.7.2 Multiple GCs on one GE （１つの GE(Gameplay Effect) で複数の GC(Gameplay Cues)）

`GameplayEffect` 上にある全ての `GameplayCues` は既に１つの RPC で送信されています。 デフォルトでは、 `UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()` は `ASC` の `Replication Mode` に関係なく、信頼性の低い NetMulticast ですべての `GameplayEffectSpec` （`FGameplayEffectSpecForRPC` に変換されますが）を送ります。 これは、 `GameplayEffectSpec` に入っているものによっては、多くの帯域幅になる可能性があります。 これについては cvar （コンソール変数）の `AbilitySystem.AlwaysConvertGESpecToGCParams 1` を設定することで、最適化できる可能性があります。 これにより、すべての `FGameplayEffectSpecForRPC` の代わりに、 `GameplayEffectSpecs` を `FGameplayCueParameter` 構造体に変換し、それらが RPC されます。 これにより、帯域幅を抑えられる可能性がありますが、情報も減らします（ `GESpec` が `GameplayCueParameters` に変換される方法や、`GCs` が知る必要がある情報に左右されます）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-events"></a>

#### 4.8.8 Gameplay Cue Events

`GameplayCues` は特定の `EGameplayCueEvents` に反応します :

| `EGameplayCueEvent` | 説明                                                                                                                                                                                                                                                                                                                                                              |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | `GameplayCue` が有効化（追加）された時に呼び出されます。                                                                                                                                                                                                                                                                                                          |
| `WhileActive`       | `GameplayCue` が有効な時に呼び出されます、実際に適用されていなくとも（進行中の参加など）。 これは `Tick` ではありません！ これは `OnActive` のように、`GameplayCueNotify_Actor` が追加されるか関連するようになった時に一度だけ呼び出されます。 `Tick()` が必要ならば、 `GameplayCueNotify_Actor` の `Tick()` を使用してください。結局の所、それは `AActor` です。 |
| `Removed`           | `GameplayCue` が削除された時に呼び出されます。 このイベントに応答するブループリントの `GameplayCue` 関数は `OnRemove` です。                                                                                                                                                                                                                                      |
| `Executed`          | `GameplayCue` が実行された時に呼び出されます : インスタントエフェクトまたは定期的な `Tick()` です。 このイベントに応答するブループリントの `GameplayCue` 関数は `OnExecute` です。                                                                                                                                                                                |

`GameplayCue` の中で、 `GameplayCue` が開始する際に発生するものには何にでも `OnActive` を使用します（遅れた参加者が失敗しても良しとするならば）。 `GameplayCue` の中で、 遅れた参加者に見せる必要がある継続的な効果は、 `WhileActive` を使用します。 たとえば、 MOBA のタワー構造物が爆発する `GameplayCue` がある場合、`OnActive` に最初の爆発のパーティクルシステムと爆発音を配置し、`WhileActive` に残りの進行中の炎のパーティクルと音を配置します。 このシナリオでは、遅れた参加者にとっては `OnActive` で最初の爆発を再生し直すのは意味がありませんが、 `WhileActive` から爆発が発生した後の地面の持続的なループする炎のエフェクトを彼らに見せたいと思うでしょう。 `OnRemove` は `OnActive` と `WhileActive` で追加されたものすべてをクリーンアップする必要があります。 `WhileActive` は Actor が `GameplayCueNotify_Actor` の関連範囲に入る度に呼び出されます。 `OnRemove` は Actor が `GameplayCueNotify_Actor` の関連範囲から離れる度に呼び出されます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-reliability"></a>

#### 4.8.9 Gameplay Cue Reliability

一般に、 `GameplayCues` は信頼性が低いとみなされるべきで、したがって、ゲームプレイに直接影響を与えるものには適しません。

**実行された `GameplayCues`:** これらの `GameplayCues` は信頼性の低いマルチキャストを介して適用されるので、常に信頼性がありません。

**`GameplayEffects` から適用された `GameplayCues`:**
* Autonomous proxy は確実に `OnActive` と `WhileActive` と `OnRemove` を受信します。  
`FActiveGameplayEffectsContainer::NetDeltaSerialize()` は `UAbilitySystemComponent::HandleDeferredGameplayCues()` を呼び出して `OnActive` と `WhileActive` を呼び出します。 `FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` は `OnRemoved` を呼び出します。
* Simulated proxies は確実に `WhileActive` と `OnRemove` を受信します。  
`UAbilitySystemComponent::MinimalReplicationGameplayCues` のレプリケーションは `WhileActive` と `OnRemove` を呼び出します。 `OnActive` イベントは信頼性の低いマルチキャストによって呼び出されます。

**`GameplayEffect` 無しで適用された `GameplayCues`:**
* Autonomous proxy は確実に `OnRemove` を受信します。  
`OnActive` と `WhileActive` イベントは信頼性の低いマルチキャストによって呼び出されます。
* Simulated proxies は確実に `WhileActive` と `OnRemove` を受信します。  
`UAbilitySystemComponent::MinimalReplicationGameplayCues` のレプリケーションは `WhileActive` と `OnRemove` を呼び出します。 `OnActive` イベントは信頼性の低いマルチキャストによって呼び出されます。

`GameplayCue` の何かを「信頼できる」ようにする必要がある場合は、 `GameplayEffect` からそれを適用し、 FX の追加に `WhileActive` を、 FX の削除に `OnRemove` を使います。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>

### 4.9 Ability System Globals

[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) クラスは GAS に関するグローバル情報を保持しています。 これらの変数の殆どは `DefaultGame.ini` で設定できます。 一般的に、このクラスとやり取りする必要はありませんが、その存在を知っておくべきです。 [`GameplayCueManager`](#concepts-gc-manager) や [`GameplayEffectContext`](#concepts-ge-context) のようなものをサブクラス化する必要がある場合は、 `AbilitySystemGlobals` を介して行う必要があります。

`AbilitySystemGlobals` をサブクラス化するには、 `DefaultGame.ini` でクラス名を設定します。
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>

#### 4.9.1 InitGlobalData()

UE 4.24 から、 [`TargetData`](#concepts-targeting-data) を使用するためには、 `UAbilitySystemGlobals::InitGlobalData()` の呼び出しが必要になりました。 さもないと、 `ScriptStructCache` に関係したエラーを受け取り、クライアントはサーバーから切断されるでしょう。 この関数はプロジェクトで一度だけ呼び出される必要があります。 Fortnite では、 AssetManager クラスの 開始初期化読み込み関数で呼び出しており、 Paragon では `UEngine::Init()` で呼び出しています。 サンプルプロジェクトで示されているように、 `UEngineSubsystem::Initialize()` に配置するのが適切であることがわかりました。 `TargetData` の問題を回避するための、プロジェクトにコピーする必要があるこの定形コードを検討します。

もし `AbilitySystemGlobals` の `GlobalAttributeSetDefaultsTableNames` を使用している時にクラッシュが発生した場合、 Fortnite のように `UEngineSubsystem::Initialize()` の中の代わりに、 `AssetManager` の中、あるいは `GameInstance` の中で `UAbilitySystemGlobals::InitGlobalData()` を後で呼び出す必要があるかもしれません。 このクラッシュは、「 `Subsystems` が作成された順序」と、「 `GlobalAttributeDefaultsTables` が `UAbilitySystemGlobals::InitGlobalData()` でデリゲートにバインドするために `EditorSubsystem` が読み込まれていることを要求すること」が原因である可能性があります。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>

### 4.10 Prediction （予測）

GAS はクライアントサイドの prediction （予測）のサポートする機能を備えています : しかしながら、すべてを predict （予測）するわけではありません。 GAS のクライアントサイドの prediction （予測）は、クライアントはサーバーの「 `GameplayAbility` の有効化と `GameplayEffects` の適用」の許可を待つ必要がないことを意味します。 （それは）サーバーが（それに）これをする許可を与えることを 「 predict （予測）」 でき、 `GameplayEffects` を適用するであろうターゲットを predict （予測）する事ができます。 次に、クライアントが有効化した後、サーバーは `GameplayAbility` のネットワークの遅延時間を実行し、クライアントの predictions （予測）が正しいかそうでないかをクライアントに伝えます。 もしクライアントが predictions （予測）のいずれかで間違っていた場合、サーバーと一致するように「 mispredictions （予測ミス）」変更を「ロールバック」します。

> Translators notes:Suggestion:SentyaAnko  
> This translation is likely to be incorrect.  
>> It can "predict" the server giving it permission to do this and predict the targets that it would apply `GameplayEffects` to.  
>> The server then runs the `GameplayAbility` network latency-time after the client activates and tells the client if he was correct or not in his predictions.  

GAS 関連の prediction （予測）の最も信頼の置けるソースは、プラグインのソースコードにある `GameplayPrediction.h` です。

Epic の考え方は、「やり過ごすことができる」ものだけを predict （予測）することです。 たとえば、Paragon と Fortnite はダメージを predict （予測） していません。 殆どの場合、彼らは [`ExecutionCalculations`](#concepts-ge-ec) を使用して、とにかく predicted （予測）できないダメージを与えてきます。 これは、ダメージのような特定の事柄を predict （予測）しようと試みることができないと言っているのではありません。 もしあなたがそれをして、あなたにとって上手く動いているのであれば、ぜひそうしてください、それは素晴らしいことです。

> ... 我々は「すべてを predict （予測）する : シームレスに、自動的に」という解決策に全面的に取り組んでいるわけではありません。 我々は今でも、プレイヤーの prediction （予測）は最小に留めるのが一番良いと感じています（意図 : やり過ごすことができる最低限のものを predict （予測）する）。

*the new [Network Prediction Plugin （ネットワーク予測プラグイン）](#concepts-p-npp) の Epic の Dave Ratti のコメント*

**predicted （予測）されるもの :**
> * アビリティの有効化
> *	発動されたイベント
> *	GameplayEffect の適用 :
>    * アトリビュートの変更 (例外 : Executions は現在 predict （予測）されず、 attribute modifiers のみ)
>    * GameplayTag の変更
> * Gameplay Cue イベント（「 predictive （予測） gameplay effect の内側」と「それ自体」の両方から）
> * モンタージュ
> * 移動 （ UE4 に組み込まれている UCharacterMovement ）

**predicted （予測）されないもの :**
> * GameplayEffect の削除
> * GameplayEffect の定期的な効果 (dots ticking)

*`GameplayPrediction.h` より*

`GameplayEffect` の適用を predict （予測）することはできますが、 `GameplayEffect` の削除を predict （予測）することはできません。 この制限を回避する一つの方法は、 `GameplayEffect` を削除したい時に逆の効果を predict （予測）することです。 40% の移動速度の低下を predict （予測）するとします。 40% の移動速度の上昇を適用することで、 predictively （予測的）にそれを削除できます。 次に両方の `GameplayEffects` を同時に削除します。 これはすべてのシナリオに適しているわけではなく、 `GameplayEffect` の削除の predicting （予測）をするためのサポートは引き続き必要です。 Epic の Dave Ratti は [GAS の将来のイテレーション](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) に追加したいという希望を表明しています。

`GameplayEffects` の削除を predict （予測）できないため、 `GameplayAbility` のクールダウンの完全な predict （予測）はできず、それらには逆の効果の `GameplayEffect` を使う回避策もありません。 サーバーがレプリケーションした `Cooldown GE` はクライアントにも存在し、これを迂回するどんな試み（たとえば `Minimal` レプリケーションモード）もサーバーによって拒否されます。 これはつまり、大きいレイテンシを持つクライアントは、サーバーにクールダウンを実行するように指示してから、サーバーの `Cooldown GE` の削除を受信するまでの時間がかかるということです。 これは大きいレイテンシを持つプレイヤーは、小さいレイテンシを持つプレイヤーに比べて発砲レートが低くなり、小さいレイテンシを持つプレイヤーに対して不利になることを意味します。 Fortnite は、 `Cooldown GEs` の代わりにカスタムブックキーピングを使用することで、この問題を回避しています。

ダメージの predicting （予測）に関しては、ほとんどの人が GAS を使い始める際に最初に試すことの１つであるにもかかわらず、私は個人的にはおすすめしません。 私は特に死亡の predict （予測）を試すのをおすすめしません。 ダメージを predict （予測）することは可能ですが、それを行うのは困難です。 もしダメージの適用を mispredict （予測ミス）した場合、プレイヤーが、敵のヘルスが跳ね上がるのを見ることになります。 これは、死亡の predict （予測）を試そうとした場合に、特に厄介で苛立たしいです。 もし、あなたが `キャラクター` の死亡を mispredict （予測ミス）をしたことで、ラグドールを停止するためだけに開始し、そしてサーバーがそれを修正した時には、あなたを撃ち続けているとしましょう。

**Note:** （`Cost GEs` のような） `Attributes` の変更をする `Instant` な `GameplayEffects` はあなた自身にはシームレスに predicted （予測）できますが、他のキャラクターへの `Instant` な `Attribute` の変更の predicting （予測）は、それらの `Attributes` に短時間の異常または「ブリップ（値の急変）」を見せるでしょう。 Predicted （予測）された `Instant` `GameplayEffects` は、実際には `Infinite` `GameplayEffects` のように取り扱われるので、もし mispredicted （予測ミス）の場合はロールバック可能です。 サーバーの `GameplayEffect` が適用されたら、２つの同じ `GameplayEffect` が存在する可能性があり、その結果、 `Modifier` が二回適用されるか、または短時間の間全く適用されなくなりまります。 最終的には自身によって修正されますが、プレイヤーにはブリップ（値の急変）が目立ってしまうこともあります。

GAS の prediction （予測）の実装が解決しようとしている問題 :
> 1. 「これができるか？」 prediction （予測）のための基本的なプロトコル。
> 2. 「元に戻す」 prediction （予測）が失敗したときに副作用を元に戻す方法。
> 3. 「やり直し」 predicted locally （ローカル予測）したが、サーバーからもレプリケーションされる副作用を再再生する方法。
> 4. 「完全性」 すべての副作用を /本当に/ predicted （予測）したかを確認する方法。
> 5. 「依存関係」 依存関係の prediction （予測）と predicted （予測）されたイベントのチェーンを管理する方法。
> 6. 「上書き」 サーバーによってレプリケーション/所有されている以外の状態を predictively （予測）的に上書きする方法。

*`GameplayPrediction.h` より*

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-key"></a>

#### 4.10.1 Prediction Key （予測キー）

GAS の prediction （予測）は、クライアントが `GameplayAbility` を有効化する際に生成する、一つの整数識別子である `Prediction Key` の概念に基づいて機能します。

クライアント側 :
* クライアントは `GameplayAbility` を有効化した際に prediction （予測）キーを生成します。 これが `Activation Prediction Key` です。
* クライアントは `CallServerTryActivateAbility()` を使用して、この prediction （予測）キーをサーバーに送ります。
* クライアントはこの prediction （予測）キーを、 prediction （予測）キーが有効な間に適用するすべての `GameplayEffects` に追加します。
* クライアントの prediction （予測）キーがスコープから外れます。同じ `GameplayAbility` でさらに predicted （予測）されるエフェクトは、新しい [スコープ付き Prediction （予測）ウィンドウ](#concepts-p-windows) が必要です。

サーバー側 :
* サーバーはクライアントから prediction （予測）キー受け取ります。
* サーバーはこの prediction （予測）キーを、適用されるすべての `GameplayEffects` に追加します。
* サーバーは prediction （予測）キーをクライアントにレプリケーションします。


クライアント側 :
* クライアントは、サーバーから レプリケーションされた `GameplayEffects` を、それらを適用するために使用される prediction （予測）キーとともに受け取ります。 もし、レプリケーションされた `GameplayEffects` のいずれかが、クライアントが同じ prediction （予測）キーで適用した `GameplayEffects` と一致する場合、それらは正常に predicted （予測）されています。 それらは、クライアントが predicted （予測）した一方を削除するまで、一時的に `GameplayEffect` の２つのコピーがターゲット上に存在します。
* クライアントはサーバーから返された prediction （予測）キーを受け取ります。 これが `レプリケーションされた Prediction （予測）キー` です。 この prediction （予測）キーは今は古いとしてマークされています。
* クライアントは今は失効したレプリケーションされた Prediction （予測）キーで作られた **すべての** `GameplayEffects` を削除します。 サーバーでレプリケーションされた `GameplayEffects` は存続します。 クライアントが追加し、サーバーから一致するレプリケーションされたバージョンを受信しなかったすべての `GameplayEffects` は mispredicted （予測ミス）です。

Prediction （予測）キーは、アクティベーション prediction （予測）キーからの `Activation` で始まる、 `GameplayAbilities` の命令「 window 」のアトミックグループの間は、有効であることを保証します。 これは、１フレームの間だけ有効であると考えることができます。 潜伏的なアクションである `AbilityTasks` からのいかなるコールバックは `AbilityTask` に、新しい [スコープ付き Prediction （予測）ウィンドウ](#concepts-p-windows) を生成する組み込みの同期ポイントが無い限り、もはや有効な prediction （予測）キーを持ちません。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-windows"></a>

#### 4.10.2 Creating New Prediction Windows in Abilities （Abilities における新しい予測ウィンドウの作成）

`AbilityTasks` から呼び出されるコールバックでより多くのアクションを predict （予測）するには、新しい「スコープ付き Prediction （予測）キー」を使用して新しい「スコープ付き Prediction （予測）ウィンドウ」を作成する必要があります。 これはしばしばクライアントとサーバーの同期ポイントとも呼ばれます。 いくつかの `AbilityTasks` は（入力と関連付けられているすべてのものと同様に）、新しいスコープ付き Prediction （予測）ウィンドウを作成するための組み込み機能があります。つまり、 `AbilityTasks` のコールバックにあるアトミックコードには、使用する有効なスコープ付き Prediction （予測）キーがあります。 `WaitDelay` タスクのような他のタスクは、コールバック用の新しいスコープ付き Prediction （予測）ウィンドウを作成するための組み込みコードがありません。 もしアクションの predict （予測）が `WaitDelay` のように、新しいスコープ付き Prediction （予測）ウィンドウを作成するための組み込みコードを持っていない `AbilityTask` の後で必要ならば、 `WaitNetSync` `AbilityTask` を `OnlyServerWait` のオプションを指定して使用して、手動で行う必要があります。 クライアントが `OnlyServerWait` の `WaitNetSync` にヒットすると、 `GameplayAbility` の アクティベーション prediction （予測）キーに基づき新しいスコープ付き Prediction （予測）キーが生成され、サーバーに RPCs され、適用されるすべての新しい `GameplayEffects` に追加します。 サーバーが `OnlyServerWait` の `WaitNetSync` にヒットすると、続行する前に、クライアントから新しいスコープ付き Prediction （予測）キーを受信するまでするまで待機します。 このスコープ付き Prediction （予測）キーはアクティベーション prediction （予測）キーと同じ様に振る舞います - `GameplayEffects` に適用され、クライアントにレプリケーションされて失効したとマークされます。 このスコープ付き Prediction （予測）キーはスコープから外れるまで有効です。つまり、スコープ付き Prediction （予測）ウィンドウが閉じます。 繰り返しになりますが、（潜伏的なものがない）アトミック操作のみ新しいスコープ付き Prediction （予測）キーが使用できます。

スコープ付き Prediction （予測）ウィンドウは、必要な数だけ作成できます。

もし、独自の `AbilityTasks` の機能のために同期ポイントを追加したいならば、入力用のものが `WaitNetSync` `AbilityTask` のコードをそれらの中に、本質的にどの様に導入されているかを見てください。

**Note:** `WaitNetSync` を使うと、サーバーの `GameplayAbility` がクライアントからの連絡があるまで、実行の継続をブロックします。 これは、悪意あるユーザーに、ゲームをハックし、意図的に新しいスコープ付き Prediction （予測）キーの送信を遅延するように悪用される可能性があります。 Epic は `WaitNetSync` を控えめに使用しているとはいえ、もしこれが懸念されるのならば、（クライアント無しで自動的に続行する遅延を使った）新しいバージョンの `AbilityTask` を構築することをおすすめします。

サンプルプロジェクトでは、スプリントの `GameplayAbility` で `WaitNetSync` を使い、スタミナコストを適用するたびに新しいスコープ付き Prediction （予測）ウィンドウを作成することで、predict （予測）できるようにしています。 理想的には、コストとクールダウンの適用する際に、有効な Prediction （予測）キーが欲しいところです。

もし、 Owning Client （所有クライアント）で二回実行されている predicted （予測）された `GameplayEffect` がある場合、 Prediction （予測）キーは失効しており、「やり直し」の問題が発生しています。 これは、新しいスコープ付き Prediction （予測）キーを作成するために `GameplayEffect` を適用する直前に `WaitNetSync` `AbilityTask` を `OnlyServerWait` を指定して配置することで、通常は解決できます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-spawn"></a>

#### 4.10.3 Predictively Spawning Actors

クライアントで predictively （予測）的に `Actors` をスポーンすることは高度なトピックです。 GAS はこれをそのまま処理する機能を提供していません（ `SpawnActor` `AbilityTask` はサーバー上で `Actor` をスポーンさせるだけです）。 鍵となる発想はクライアントとサーバーの両方でレプリケーションされた `Actor` をスポーンさせることです。

もし `Actor` が 装飾用もしくはなんのゲームプレイ用途も提供しないならば、単純な解決方法は `Actor` の `IsNetRelevantFor()` 関数をオーバーライドし、サーバーが、 Owning Client （所有クライアント）にレプリケーションするのを制限することです。  Owning Client （所有クライアント）トはローカルでスポーンしたバージョンをもつことになり、サーバーと他のクライアントはサーバーのレプリケーションバージョンを持つことになります。

```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

もし、ダメージの predict （予測）が必要な投射物のように、スポーンした `Actor` がゲームプレイに影響を及ぼすならば、このドキュメントの範囲外の高度なロジックが必要です。 Epic Games の GitHub にある、 UnrealTournament が、どのように predictively （予測）的に投射物をスポーンしているかを見てください。 そこでは、「サーバーのレプリケーションされた投射物」と同期した、「 Owning Client （所有クライアント）のみでスポーンされているダミーの投射物」があります。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-future"></a>

#### 4.10.4 Future of Prediction in GAS （GAS における未来予測）

`GameplayPrediction.h` によると、将来、「 `GameplayEffect` の削除」と、「周期的な `GameplayEffects` 」の predicting （予測）する機能が追加される可能性があるとのことです。

Epic の Dave Ratti は、レイテンシーの高いプレイヤーはレイテンシーの低いプレイヤーに対して不利になる、クールダウンを predicting （予測）するための `レイテンシーの調停` 問題の修正に [関心を示しています](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 。

Epic による新しい [`Network Prediction` plugin](#concepts-p-npp) は `CharacterMovementComponent` が *かつて* そうだったように、 GAS との完全な相互運用可能であることが期待されています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-npp"></a>

#### 4.10.5 Network Prediction Plugin （ネットワーク予測プラグイン）

Epic は近頃、`CharacterMovementComponent` を新しい `Network Prediction` プラグインに置き換える取り組みを開始しました。 このプラグインはまだ非常に初期段階ですが、 Unreal Engine の GitHub で非常に早い段階でアクセスし利用できます。 エンジンのどの将来のバージョンでそれが実験的なベータデビューを果たすのかを語るのは時期尚早です。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting"></a>

### 4.11 Targeting

<a name="concepts-targeting-data"></a>

#### 4.11.1 Target Data

[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) は、ネットワークを介して渡されることを意図した、ターゲットのデータのための一般的な構造体です。 `TargetData` は一般的に、 `AActor`/`UObject` の参照や、 `FHitResults` や、その他の一般的な座標/向き/起点情報を保持しています。 しかしながら、[`GameplayAbilities` でクライアントとサーバー間でデータを渡す](#concepts-ga-data) ための簡単な手段として、本質的に必要なものを内部に配置するために、サブクラス化できます。 基本の構造体 `FGameplayAbilityTargetData` は直接使うためのものではなく、サブクラス化するためのものです。 `GAS` には `FGameplayAbilityTargetData` をサブクラス化したいくつかのすばらしい構造体が付属しており、それらは `GameplayAbilityTargetTypes.h`にあります。

`TargetData` は一般的に、 [`Target Actors`](#concepts-targeting-actors) に生成されるか **手動で作成され** 、[`EffectContext`](#concepts-ge-context) を介して [`AbilityTasks`](##concepts-at) と [`GameplayEffects`](#concepts-ge) に消費されます。 `EffectContext` にある結果として、 [`Executions`](#concepts-ge-ec) と [`MMCs`](#concepts-ge-mmc) と [`GameplayCues`](#concepts-gc) と [`AttributeSet`](#concepts-as) のバックエンドの関数では `TargetData` にアクセスできます。

通常は、 `FGameplayAbilityTargetData` を直接渡さず、かわりに [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html) を使います。 これは内部に `FGameplayAbilityTargetData` へのポインターの TArray を所持しています。 これは、 `TargetData` のポリモーフィズムのサポートを提供する中間の構造体です。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-actors"></a>

#### 4.11.2 Target Actors

`GameplayAbilities` は、 `WaitTargetData` `AbilityTask` を伴って [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html) を視覚化のためにスポーンし、ワールドからのターゲット情報をキャプチャします。 `TargetActors` はオプションで [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles) を使用することで、現在のターゲットを表示できます。 確認のうえ、ターゲット情報は `GameplayEffects` で渡すことができる [`TargetData`](#concepts-targeting-data) として情報を返されます。


`TargetActors` は `AActor` に基づいており、ターゲットとする **場所** と **方法** を示すために、あらゆる種類の可視コンポーネントを持つことができます（スタティックメッシュやデカールのような）。 スタティックメッシュは、キャラクターが構築するオブジェクトの配置の視覚化に使用することができます。 デカールは地面に影響を与える範囲を示すために使用することができます。 サンプルプロジェクトでは、 [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html) を地面のデカールとして使用することで、メテオアビリティのダメージの影響を与える範囲を示しています。 また、何も表示しなくても構いません。 たとえば、 [GASShooter](https://github.com/tranek/GASShooter) で使用されている、（即時にターゲットまでの直線をトレースする）ヒットスキャンガンには何かを表示することに意味がありません。

これらは、「基本のトレースまたはオーバーラップを使いターゲットの情報をキャプチャ」し、「 `TargetActor` の実装に基づいて、結果を `FHitResults` または `AActor` の配列として `TargetData` に変換」します。 `WaitTargetData` `AbilityTask` は、ターゲットが `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` パラメータを通じて、いつ確認されるかを決定します。 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant` を使用 **しない** 場合、 `TargetActor` は通常、トレースとオーバーアラップを `Tick()` で行い、実装に従い `FHitResult` の座標を更新します。 トレースとオーバーアラップを `Tick()` で行いますが、レプリケーションされておらず、一般的には１つ以上（もっとあってもよいのですが）の `TargetActor` が一度に実行することはないので、通常はひどいことにはなりません。 それが `Tick()` を使用しており、複雑な `TargetActors` ではそこで多くのこと（たとえばGASShooter における ロケットランチャーのセカンダリーアビリティ）を行う可能性があることを、承知しておいてください。 `Tick()` でトレースするとクライアントの応答性が非常に良いですが、パフォーマンスヒットが大きすぎる場合は、 `TargetActor` tick の頻度を下げることを検討すべきです。 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant` のケースでは、 `TargetActor` はすぐにスポーンされ、 `TargetData` を生成し、破棄します。 `Tick()` は呼び出されません。

| `EGameplayTargetingConfirmation::Type` | いつターゲットが確認されるか                                                                                                                                                                                                                                                                                                                                                   |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`                              | ターゲティングは（特別なロジックやユーザーの入力によって「発火」するタイミングを決められることなく）即座に起こります。                                                                                                                                                                                                                                                         |
| `UserConfirmed`                        | ターゲティングは「 [アビリティが `確認` 入力にバインドされている](#concepts-ga-input) 時にユーザーがターゲティングを確認する」か、「 `UAbilitySystemComponent::TargetConfirm()` を呼び出す」ことで起こります。 `TargetActor` は、バインドされた `キャンセル` 入力に応答するか `UAbilitySystemComponent::TargetCancel()` を呼び出すことで、ターゲティングのキャンセルもします。 |
| `Custom`                               | GameplayTargeting アビリティは、 `UGameplayAbility::ConfirmTaskByInstanceName()` を呼び出すことで、ターゲティングデータが準備できたかどうかを判断します。 `TargetActor` は `UGameplayAbility::CancelTaskByInstanceName()` に応答することで、ターゲティングのキャンセルもします。                                                                                               |
| `CustomMulti`                          | GameplayTargeting アビリティは、 `UGameplayAbility::ConfirmTaskByInstanceName()` を呼び出すことで、ターゲティングデータが準備できたかどうかを判断します。 `TargetActor` は `UGameplayAbility::CancelTaskByInstanceName()` に応答することで、ターゲティングのキャンセルもします。データ生成時に `AbilityTask` を終了しないでください。                                          |

すべての EGameplayTargetingConfirmation::Type がすべての `TargetActor` にサポートされているわけではありません。 たとえば、 `AGameplayAbilityTargetActor_GroundTrace` は `Instant` の確認をサポートしていません。

`WaitTargetData` `AbilityTask` は 「 `AGameplayAbilityTargetActor` クラスをパラメータとして取り込み」、「 `AbilityTask` のアクティベーション毎にインスタンスをスポーン」し、「 `AbilityTask` が終了した際に `TargetActor` を破棄」します。 `WaitTargetDataUsingActor` `AbilityTask` は 既にスポーンされた `TargetActor` を取り込みますが、`AbilityTask` が終了した際にそれを破棄します。 これらの `AbilityTasks` はどちらも、使用するたびに「スポーンする」か、または「新たにスポーンされた `TargetActor` を要求する」ため、非効率です。 これらはプロトタイピングには素晴らしいですが、自動小銃のように、常に `TargetData` を生成しているケースが有るのであれば、製品版では最適化を検討してもよいです。 GASShooter は [`AGameplayAbilityTargetActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h) のカスタムサブクラスと、スクラッチで書かれた新しい [`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask` があり、`TargetActor` を破棄せずに再利用できます。

`TargetActors` はデフォルトではレプリケーションされません : しかしながら、ローカルプレイヤーがどこをターゲティングしているかを他のプレイヤーが見えるのがゲームとして理にかなっているのであれば、レプリケーションを作るようにすることもできます。 それらには `WaitTargetData` `AbilityTask` 上の RPCs を介してサーバーと通信するためのデフォルトの機能が含まれています。 `TargetActor` の `ShouldProduceTargetDataOnServer` プロパティが `false` に設定されると、クライアントは  `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` の `CallServerSetReplicatedTargetData()` を介した確認時に `TargetData` をサーバーに RPC します。 `ShouldProduceTargetDataOnServer` が `true` ならば、クライアントは汎用確認イベント `EAbilityGenericReplicatedEvent::GenericConfirm` を送り、 `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` でサーバーに RPC し、サーバーは受信した RPC の上で、サーバー上でデータを生成するためにトレースまたはオーバーラップのチェックを行います。 クライアントがターゲティングをキャンセルした場合、汎用的なキャンセルイベント `EAbilityGenericReplicatedEvent::GenericCancel` を送り、 `UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback` でサーバーに RPC します。 ご覧のとおり、 `TargetActor` と `WaitTargetData` `AbilityTask` の両方には多くのデリゲートがあります。 `TargetActor` は入力に応答し、 `TargetData` の ready または confirm または cancel のデリゲートの生成とブロードキャストを行います。 `WaitTargetData` は `TargetActor` の `TargetData` の ready と confirm と cancel のデリゲートのリッスンし、その情報を `GameplayAbility` とサーバーに中継します。 `TargetData` をサーバーに送信する場合、サーバーで検証を行って、チートを予防するために `TargetData` が正当に見えるか確認することをおすすめします。 サーバーで `TargetData` を直接生成することでこの問題を完全に回避できますが、 Owning Client （所有クライアント）が mispredictions （予測ミス）を引き起こす可能性があります。

使用する `AGameplayAbilityTargetActor` の特定のサブクラスに応じて、 異なる `ExposeOnSpawn` パラメータが `WaitTargetData` `AbilityTask` ノードで公開されます。 一般的なパラメータは以下のものがあります :

| Common `TargetActor` Parameters | 説明                                                                                                                                                                                                                                                                                                                            |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Debug                           | `true` の場合、 non-shipping ビルドでは、 `TargetActor` が トレースを実行するたびに、トレース/オーバーラップのデバッグ情報が描画されます。 `Instant` ではない `TargetActors` は `Tick()` でトレースを実行するので、これらのデバッグ描画の呼び出しは `Tick()` でも発生することを忘れないでください。                             |
| Filter                          | [Optional] トレース/オーバーラップが発生した際にターゲットから `Actors` を除外（削除）するための特別な構造体。  典型的な使用例は、プレイヤーの `Pawn` を除外することで、ターゲットが特定のクラスであることを要求します。 より高度な使用例については、 [Target Data Filters](#concepts-target-data-filters) を参照してください。 |
| Reticle Class                   | [Optional] `TargetActor` がスポーンする `AGameplayAbilityWorldReticle` のサブクラスです。                                                                                                                                                                                                                                       |
| Reticle Parameters              | [Optional] 焦点板の設定。 [焦点板](#concepts-targeting-reticles) を参照してください。                                                                                                                                                                                                                                           |
| Start Location                  | トレースを開始する場所のための特別な構造体。 通常、これは、プレイヤーのビューポイントか、武器の銃口か、あるいは `Pawn` の座標となります。                                                                                                                                                                                       |

デフォルトの `TargetActor` クラスでは、 `Actors` はトレース/オーバーラップに直接ある場合のみに有効なターゲットです。 彼らがトレース/オーバーラップから離れた（彼らが移動したかあなたが目を逸した）のであれば、彼らはもはや有効ではありません。 `TargetActor` に最後に有効だったターゲットを記憶させたいのであれば、カスタム `TargetActor` クラスにこの機能を追加する必要があります。 わたしは、（「 `TargetActor` が確認またはキャンセルを受信する」か、「 `TargetActor` が新しい有効なターゲットをトレース/オーバーラップで見つける」か、「ターゲットが無効になる（破棄される）」まで持続するこれらを）パーシスタントターゲットと呼びます。 GASShooter はロケットランチャーのセカンダリーアビリティのホーミングターゲットを、パーシスタントターゲットとして使用しています。


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-target-data-filters"></a>

#### 4.11.3 Target Data Filters

`Make GameplayTargetDataFilter` と `Make Filter Handle` 両方のノードを使うことで、「プレイヤーの `Pawn` を除外」または「特定のクラスのみを選択」することができます。 より高度なフィルタリングが必要ならば、 `FGameplayTargetDataFilter` をサブクラス化して `FilterPassesForActor` 関数をオーバーライドすることができます。

```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** actor がフィルターを通過しターゲットになれる場合は true を返す */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

しかしながら、 `FGameplayTargetDataFilterHandle` を要求するため、 `Wait Target Data` ノードに直接機能しません。 サブクラスを受け入れるには、新しいカスタム `Make Filter Handle` を作る必要があります :

```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>

#### 4.11.4 Gameplay Ability World Reticles （Gameplay Ability ワールド焦点板）

[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html) (`焦点板`) は、 非 `Instant` 確認済みの [`TargetActors`](#concepts-targeting-actors) でターゲティングする際に、 **誰を** ターゲティングしているかを視覚化します。 `TargetActors` はすべての `焦点板` のスポーンと破棄のライフタイムを担当します。 `焦点板` は `AActors` なので、表現にあらゆるの種類の可視コンポーネントを使用することが可能です。 [GASShooter](https://github.com/tranek/GASShooter) で見られる一般的な実装は、 `WidgetComponent` を使用して、UMG Widget をスクリーンスペース（常にプレイヤーのカメラに面している）に表示することです。 `焦点板` はそれらがどの `AActor` にあるかを知りませんが、カスタム `TargetActor` でその機能をサブクラス化することができます。 `TargetActors` は通常、 `Tick()` 毎に `焦点板` の座標をターゲットの座標に更新します。

GASShooter は `焦点板` を使用して、ロケットランチャーのセカンダリーアビリティのホーミングロケットのためにロックオンターゲットを表示します。 敵の赤いインジケーターは `焦点板` です。 同様の白い画像はロケットランチャーの十字線です。

![Reticles in GASShooter](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`焦点板` は、設計者向け（これらはブループリントで開発することを目的としています）に多少の `BlueprintImplementableEvents` が付属しています :


```c++
/** bIsTargetValid の値が変わるたびに呼び出される。 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** bIsTargetAnActor の値が変わるたびに呼び出される。 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`焦点板` はオプションで `TargetActor` によって提供される [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) を、設定のために使用することができます。 デフォルトの構造体は、一つの変数 `FVector AOEScale` のみを提供します。 技術的には、この構造体をサブクラス化できますが、`TargetActor` は ベースの構造体のみを受け入れます。 これをデフォルトの `TargetActors` のサブクラス化ができないようにするのは少々近視眼的な気がします。 とはいえ、独自のカスタム `TargetActor` を作れば、独自のカスタムの焦点版のパラメータ構造体を提供でき、それらをスポーンさせる時に手動であなたの `AGameplayAbilityWorldReticles` のサブクラスを渡すことができます。

`焦点板` はデフォルトではレプリケーションされませんが、ローカルプレイヤーがどこをターゲティングしているかを他のプレイヤーが見えるのがゲームとして理にかなっているのであれば、レプリケーションさせることができます。

`焦点板` は、デフォルトの `TargetActors` を使用して、現在有効なターゲットにのみ表示されます。 たとえば、ターゲットをトレースするために `AGameplayAbilityTargetActor_SingleLineTrace` を使用しているならば、 `焦点板` は敵が直接トレースパスにいる場合のみに表示されます。 もしあなたが目をそらすと、敵はもはや有効なターゲットではなくなり、 `焦点板` は表示されなくなります。 `焦点板` に最後に有効だったターゲットを残したいのであれば、 `TargetActor` をカスタマイズして最後に有効だったターゲットを記憶させ、 `焦点板` をそれらに保持する必要があります。 わたしは、（「 `TargetActor` が確認またはキャンセルを受信する」か、「 `TargetActor` が新しい有効なターゲットをトレース/オーバーラップで見つける」か、「ターゲットが無効になる（破棄される）」まで持続するこれらを）パーシスタントターゲットと呼びます。 GASShooter はロケットランチャーのセカンダリーアビリティのホーミングターゲットを、パーシスタントターゲットとして使用しています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-containers"></a>

#### 4.11.5 Gameplay Effect Containers Targeting （Gameplay Effect コンテナのターゲティング）

[`GameplayEffectContainers`](#concepts-ge-containers) には、 [`TargetData`](#concepts-targeting-data) を生成するための、オプションの効率的な手段が付属しています。 このターゲティングは、 `EffectContainer` がクライアントとサーバーに適用されたら、即座に実行されます。 これは [`TargetActors`](#concepts-targeting-actors) より効率的です。 なぜなら、ターゲットオブジェクトの CDO (ClassDefaultObject) （スポーンも破棄もされない `Actors` ）で実行されるからです。 しかし、プレイヤーの入力がなく、確認を必要とせず即座に発生し、キャンセルできず、クライアントからサーバーにデータを送ることができません（両方でデータを生成します）。 インスタントトレースやコリジョンオーバーラップに適しています。 Epic の [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) にコンテを使用した二種類のターゲティングの例が含まれています - アビリティの所有者をターゲットにすることと、イベントから `TargetData` を引き出すことです 。 １つはブループリントでプレイヤーからのオフセット（子のブループリントクラスで設定）を即座にスフィアトレースする実装をしています。 独自のターゲティングタイプを作るために、 C++ またはブループリントで `URPGTargetType` をサブクラス化できます。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae"></a>

## 5. Commonly Implemented Abilities and Effects （一般的に実装されている Abilities と Effects）

<a name="cae-stun"></a>

### 5.1 Stun

通常、スタンは、`Character` の有効な `GameplayAbilities` をすべてキャンセルさせ、新しい `GameplayAbility` の有効化を阻害し、スタンの期間中の移動を阻害します。 サンプルプロジェクトのメテオ `GameplayAbility` は、命中したターゲットにスタンを適用します。

ターゲットのアクティブな `GameplayAbilities` をキャンセルするには、スタン [`GameplayTag` が追加された](#concepts-gt-change) 時に `AbilitySystemComponent->CancelAbilities()` を呼び出します。

スタンの間、新しい `GameplayAbilities` が有効になるのを阻害するには、 `GameplayAbilities` の [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags) にスタン `GameplayTag` を与えます。

スタンの間、移動を阻害するには、`CharacterMovementComponent` の `GetMaxSpeed()` 関数をオーバーライドし、所有者がスタン `GameplayTag` を所持していた場合は 0 を返すようにします。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-sprint"></a>

### 5.2 Sprint

サンプルプロジェクトでは、スプリントする方法の例を提供しています - `Left Shift` を押されている間より速く走ります。

より速い移動は、フラグをネットワークを介してサーバーに送信することで、 `CharacterMovementComponent` により predictively （予測）的に処理されます。 詳細については、 `GDCharacterMovementComponent.h/cpp` を参照してください。

`GA` は `Left Shift`の入力の応答を処理し、「 `CMC` にスプリントの開始と停止」と「 `Left Shift` が押されている間、 predictively （予測）的にスタミナの補充」を指示します。 詳細については、 `GA_Sprint_BP` を参照してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ads"></a>

### 5.3 Aim Down Sights （エイムダウンサイト）

サンプルプロジェクトは、これをスプリントと正に同じ方法で処理しますが、移動速度を増加させるのではなく減少させます。

predictively （予測）的な移動速度の減少の詳細については、 `GDCharacterMovementComponent.h/cpp` を参照してください。

入力処理の詳細については、 `GA_AimDownSight_BP` を参照してください。 aiming down sights （エイムダウンサイト）にはスタミナコストがありません。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ls"></a>

### 5.4 Lifesteal

ライフスチールはダメージ [`ExecutionCalculation`](#concepts-ge-ec) の中で処理しています。 `GameplayEffect` は `Effect.CanLifesteal` のような `GameplayTag` をもたせます。 `ExecutionCalculation` は `GameplayEffectSpec` が前述の `Effect.CanLifesteal` `GameplayTag` を所持しているかチェックします。 `GameplayTag` が存在する場合、 `ExecutionCalculation` は、 modifier として与えるヘルスの量を使用して [動的に `Instant` `GameplayEffect` を作成](#concepts-ge-dynamic) し、それを `Source` の `ASC` に適用します。

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

### 5.5 Generating a Random Number on Client and Server （クライアントとサーバーでの乱数生成）

ときには、 `GameplayAbility` の中で（銃弾の反動や拡散などのために）「乱数」を生成する必要があります。 クライアントとサーバーの両方で同じ乱数を生成したいと思うでしょう。 これを行うには、 `GameplayAbility` の有効化の際に `ランダムシード` を同じに設定する必要があります。 クライアントが mispredicts （予測ミス）をし、乱数シーケンスがサーバーと同期ズレした場合に備えて、`GameplayAbility` の有効化ごとに `ランダムシード` を設定しておくと良いでしょう。

| Seed Setting Method                                                          | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ---------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| アクティベーション prediction （予測）キー を使用する                        | `GameplayAbility` のアクティベーション prediction （予測）キーは int16 で、「同期されていること」と「 `Activation()` の中でサーバーとクライアントの両方ですぐ利用可能であること」が保証されています。 サーバーとクライアントの両方で、これを `ランダムシード` として設定可能です。 この方法の欠点は、 prediction （予測）キーは常にゲーム開始の毎に 0 から始まり、キーの生成の度に一定の値が加算されることです。 これは、各試合がまさに同じ乱数シーケンスになるということです。 これは、あなたのニーズに対して十分な乱数かもしれないし、そうではないかもしれません。 |
| `GameplayAbility` のアクティブ化の際に、イベントのペイロードでシード値を送る | イベント毎に `GameplayAbility` を有効化し、ランダムに生成されたシード値をイベントのレプリケーションを介してクライアントからサーバーに送信します。 これはよりランダム性が高くなりますが、クライアントは簡単にゲームをハックでき、毎回同じシード値のみを送られる可能性があります。 また、イベントによる `GameplayAbilities` の有効化は入力バインドからの有効化を妨げます。                                                                                                                                                                                 |

もしランダム値の偏りが小さいならば、殆どのプレイヤーがシーケンスがゲーム毎に同じことに気づかず、アクティベーション prediction （予測）キーを `ランダムシード` に使用することはあなたにとって機能するでしょう。 ハッカー対策のためになにかもっと複雑にしたいのであれば、「prediction （予測）キーを作成」したり 「イベントペイロードを介して送信する `ランダムシード` を生成」したりをサーバーで行えるの場合は、 `Server Initiated` `GameplayAbility` を使用することでおそらくより上手く機能するでしょう。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-crit"></a>

### 5.6 Critical Hits

クリティカルヒットはダメージ [`ExecutionCalculation`](#concepts-ge-ec) の中で処理しています。 `GameplayEffect` は `Effect.CanCrit` のような `GameplayTag` をもたせます。 `ExecutionCalculation` は `GameplayEffectSpec` が前述の `Effect.CanCrit` `GameplayTag` を所持しているかチェックします。 `GameplayTag` が存在する場合、 `ExecutionCalculation` はクリティカルヒットチャンス（ `Source` からキャプチャされた `Attribute` ）に対応する乱数を生成し、それに成功しているならばクリティカルヒットダメージ（これも `Source` からキャプチャされた `Attribute` ）を加算します。 ダメージを predict （予測）しないので、サーバーだけで `ExecutionCalculation` を実行するので、クライアントとサーバー間で乱数生成器の同期を気にする必要はありません。 もしダメージ計算を行うために `MMC` を使い、これを predictively （予測）的に実行してみようとした場合、 `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance` から `ランダムシード` への参照を取得する必要があります。

[GASShooter](https://github.com/tranek/GASShooter) のヘッドショットの方法を参照してください。 チャンスを乱数に依存せず、代わりに `FHitResult` のボーン名のチェックをするとを除いて同じコンセプトです。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-nonstackingge"></a>

### 5.7 Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target （非スタック Gameplay Effects 、ただし、実際にターゲットに影響を与えるのは最大の大きさのもののみの）

スローエフェクトは Paragon ではスタックしませんでした。 各スローのインスタンスは適用され、通常は生存期間中は保持されますが、最大の magnitude のスローエフェクトだけが実際にキャラクターに効果を及ぼします。 GAS はこのシナリオのためにすばらしい `AggregatorEvaluateMetaData` を提供します。 詳細と実装については、 [`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated) を参照してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-paused"></a>

### 5.8 Generate Target Data While Game is Paused （ゲームの一時停止中にターゲットデータの生成）

あなたのプレイヤーの `WaitTargetData` `AbilityTask` で [`TargetData`](#concepts-targeting-data) が生成されるまでゲームのポーズが必要な場合、ポーズの代わりに `slomo 0` を使用するのを提案します。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>

### 5.9 One Button Interaction System

[GASShooter](https://github.com/tranek/GASShooter) はワンボタンインタラクションシステムを実装していおり、 プレイヤーは「E」を押すか押し続けることで、やり取り可能なオブジェクトとやり取りできます。 たとえば、プレイヤーの蘇生、武器箱の開放、スライドドアの開閉ができます。

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging"></a>

## 6. Debugging GAS

多くの場合、 GAS 関連の問題をデバッグしている時は、以下のようなことが知りたいでしょう :
> * 「私のアトリビュートの値はなにか？」
> * 「私が保持している gameplay tags はなにか？」
> * 「私が現在保持している gameplay effects はなにか？」
> * 「私が付与したアビリティはなんで、実行中なのはどれで、有効化を妨げられているのはどれか？」

GAS は実行時にこれらの質問に答えるための２つの手法が付属しています - [`showdebug abilitysystem`](#debugging-sd) と [`GameplayDebugger`](#debugging-gd) の中でのフックです。

**Tip:** UE4 は C++ のコードを最適化するのが好きなので、いくつかの関数のデバッグが困難になります。 コードを深くトレースしている時、これに遭遇するのはまれです。 Visual Studio solution の設定を `DebugGame Editor` に設定してもコードのトレースと値のインスペクトが困難の場合、最適化された関数を `PRAGMA_DISABLE_OPTIMIZATION_ACTUAL` と `PRAGMA_ENABLE_OPTIMIZATION_ACTUAL` マクロでラッピングすることですべての最適化を無効化できます。 プラグインをソースからリビルドするまで、これをプラグインコードで使用することはできません。 これは、インライン関数では、何をしていて何処にあるかに応じて、機能する場合としない場合があります。 デバッグが完了したら必ずマクロを削除してください！

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

ゲーム内のコンソールで で `showdebug abilitysystem` とタイプしてください。 この機能は３つの「ページ」に分かれています。 ３つのページすべてに、現在所持している `GameplayTags` が表示されます。 コンソールで `AbilitySystem.Debug.NextCategory` とタイプすることで、３つのページを循環します。

最初のページは、すべての `Attributes` の `CurrentValue` が表示されます :
![First Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

二番目のページは、すべての `Duration` と `Infinite` の `GameplayEffects` と、それらがスタックされている数と、それらが提供する `GameplayTags` と、それらが提供する `Modifiers` が表示されます。
![Second Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

三番目のページは、すべての付与された `GameplayAbilities` と、それらが実行されているかどうかと、それらが有効化からブロックされているかどうかと、現在実行中の `AbilityTasks` の状態が表示されます。
![Third Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

`PageUp` と `PageDown` により、ターゲットを循環できますが、ページは、あなたのローカルでコントロールされている `Character` の `ASC` のデータだけが表示されます。 しかしながら、 `AbilitySystem.Debug.NextTarget` と `AbilitySystem.Debug.PrevTarget` を使うことで、他の `ASCs` のデータも表示されます。 ですが、上半分のデバッグ情報は更新されませんし、緑色のターゲティング用の長方形の角柱も更新されませんので、どの `ASC` が現在ターゲットされているのか知る方法がありません。 このバグは既に報告されています。 https://issues.unrealengine.com/issue/UE-90437

**Note:** `showdebug abilitysystem` を機能させるためには、 GameMode で、現在の HUD クラスを選択する必要があります。 さもないと、コマンドが見つからず、「 Unknown Command 」が返されます。

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-gd"></a>

### 6.2 Gameplay Debugger

GAS はGameplay Debugger に機能を追加します。 Gameplay Debugger へのアクセスは アポストロフィー (') キーで行います。 アビリティのカテゴリを有効にするにはテンキーの 3 を押します。 カテゴリは使用しているプラグインによって異なる場合があります。 ラップトップのようにキーボードにテンキーがない場合は、プロジェクトセッティングでキーバインドを変更することができます。

**ほかの** `Characters` の `GameplayTags` や `GameplayEffects` や `GameplayAbilities` を見たい時、Gameplay Debugger を使います。 あいにく、ターゲットの `Attributes` の `CurrentValue` は表示されません。 画面の中央にいる `Character` がターゲットとなります。 エディタのワールドアウトライナでそれを選択するか、別の `Character` を注視しアポストロフィー (') を押し直すことで、ターゲットを変更する事ができます。 現在インスペクトしている `Character` の上には最大の赤い円が置かれます。

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-log"></a>

### 6.3 GAS Logging

GAS のソースコードには多様な verbosity levels で生成された沢山のログステートメントが含まれています。 おそらくこれらを `ABILITY_LOG()` ステートメントとして見るでしょう。 デフォルトの verbosity level は `Display` です。 これより高いものはデフォルトではコンソールに表示されません。

log category のverbosity level を変更するには、以下のようにコンソールにタイプします :
```
log [category] [verbosity]
```

たとえば、 `ABILITY_LOG()` ステートメントをオンにするには、以下のようにコンソールにタイプします :
```
log LogAbilitySystem VeryVerbose
```

デフォルトにリセットするには、以下のようにタイプします :
```
log LogAbilitySystem Display
```

すべてのログカテゴリを表示するには、以下のようにタイプします :
```
log list
```

注目すべき GAS と関連があるログカテゴリ :

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

詳細については、 [Wiki on Logging](https://www.ue4community.wiki/Legacy/Logs,_Printing_Messages_To_Yourself_During_Runtime) を参照してください。


**[⬆ Back to Top](#table-of-contents)**

<a name="optimizations"></a>

## 7. Optimizations

<a name="optimizations-abilitybatching"></a>

### 7.1 Ability Batching （Ability のバッチ処理）

有効化し、オプションでサーバーに `TargetData` を送信し、１フレームで全てを終了する [`GameplayAbilities`](#concepts-ga) は [バッチ処理で ２，３個の RPCs を １つの RPC に凝縮](#concepts-ga-batching) することができます。 これらのような種類のアビリティはヒットスキャンガンに一般的に使用されます。

<a name="optimizations-gameplaycuebatching"></a>

### 7.2 Gameplay Cue Batching （Gameplay Cue のバッチ処理）

同時に沢山の [`GameplayCues`](#concepts-gc) を送信しているならば、 [１つの RPC でバッチ処理する](#concepts-gc-batching) ことを考慮すべきです。 目標は、 RPCs の数を減らし（ `GameplayCues` は信頼性の低い NetMulticasts です ）、できるだけ少ないデータを送信することです。

<a name="optimizations-ascreplicationmode"></a>

### 7.3 AbilitySystemComponent Replication Mode

デフォルトでは、 [`ASC`](#concepts-asc) は [`Full Replication Mode`](#concepts-asc-rm) です。 これにより、すべての [`GameplayEffects`](#concepts-ge) がすべてのクライアントにレプリケーションされます（これはシングルプレイヤーゲームには問題ありません。）。 マルチプレイヤーゲームでは、プレイヤーが所有する `ASCs` は `Mixed Replication Mode` を設定し、 AI 制御のキャラクターは `Minimal Replication Mode` を設定します。 これにより、プレイヤーキャラクターに適用された `GEs` はそのキャラクターの所有者のみにレプリケーションされ、 AI 制御のキャラクターに適用された `GEs` はクライアントにレプリケーションされません。 [`GameplayTags`](#concepts-gt) は 引き続きレプリケーションされ、 [`GameplayCues`](#concepts-gc) は `Replication Mode` に関係なく、引き続き信頼性の低い NetMulticasts ですべてのクライアントに送られます。 これにより、すべてのクライアントが表示する必要がない場合、レプリケーションされる `GEs` からのネットワークのデータが削減されます。

> Translators notes:SentyaAnko  
> This translation is likely to be incorrect.  
>> This will replicate `GEs` applied on a player character to only replicate to the owner of that character and `GEs` applied on AI controlled characters will never replicate `GEs` to clients.  

<a name="optimizations-attributeproxyreplication"></a>

### 7.4 Attribute Proxy Replication

Fortnite Battle Royale (FNBR) のような、多くのプレイヤーが参加する大規模なゲームでは、多くの [`Attributes`](#concepts-a) をレプリケーションする、常に関連する `PlayerStates` に存在する、多くの [`ASCs`](#concepts-asc) があります。 このボトルネックを最適化するために、 Fortnite では `PlayerState::ReplicateSubobjects()` の中で、 **simulated player-controlled proxies** で `ASC` とその [`AttributeSets`](#concepts-as) をレプリケーションから完全に無効化しています。 Autonomous proxies と AI にコントロールされた `Pawns` は引き続きそれらの [`Replication Mode`](#concepts-asc-rm) に従い、完全にレプリケーションされます。 常に関連する `PlayerStates` 上の `ASC` 上のレプリケーションされた `Attributes` の代わりに、 FNBR はプレイヤーの `Pawn` で、レプリケーションプロキシ構造体を使用します。 サーバーの `ASC` で `Attributes` が変更した時、プロキシ構造体も変更されます。 クライアントはプロキシ構造体からレプリケーションされた `Attributes` を受信し、変更をローカル `ASC` にプッシュバックします。 これにより、 `Attribute` のレプリケーションで `Pawn`の関連性と `NetUpdateFrequency` を使用できるようになります。 このプロキシ構造体はホワイトリストに記載された小さな `GameplayTags` のセットをビットマスクにレプリケーションもします。 この最適化はネットワーク上のデータ量を減らし、ポーンの関連性を活用できるようになります。 AI にコントロールされた `Pawns` は `Pawn` に `ASC` を持っており、既にこの関連性を使用しているため、この最適化は必要ありません。

> それ以降に既に行われた他のサーバー側の最適化（ Replication Graph など ）でまだそれが必要かわかりませんが、それは最も保守しやすいパターンではありません。

*Epic の Dave Ratti の [community questions #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) への回答*

<a name="optimizations-asclazyloading"></a>

### 7.5 ASC Lazy Loading （ASC 遅延読み込み）

Fortnite Battle Royale (FNBR) は多くのダメージを与える `AActors` （木や建物など）がワールドにあり、それぞれに [`ASC`](#concepts-asc) がついています。 これはメモリコストを増加する可能性があります。 FNBR はそれらが必要になった際（プレイヤーが最初にダメージを受けた際）に `ASCs` の遅延ロードをすることでこれを最適化します。 一部の `AActors` は試合中にダメージを与えることがないため、これにより全体的なメモリ使用量が削減されます。

**[⬆ Back to Top](#table-of-contents)**

<a name="qol"></a>

## 8. Quality of Life Suggestions

<a name="qol-gameplayeffectcontainers"></a>

### 8.1 Gameplay Effect Containers （コンテナ）

[GameplayEffectContainers](#concepts-ge-containers) は [`GameplayEffectSpecs`](#concepts-ge-spec) や [`TargetData`](#concepts-targeting-data) や [simple targeting](#concepts-targeting-containers) や関連する機能を、使いやすい構造体に結合します。 これらは、アビリティからスポーンした投射物に、（後で衝突時に適用する） `GameplayEffectSpecs` を伝達するのに最適です。

<a name="qol-asynctasksascdelegates"></a>

### 8.2 Blueprint AsyncTasks to Bind to ASC Delegates （ASC デリゲートにバインドする ブループリント AsyncTasks）

設計者にとって使いやすいイテレーション期間を増やすために、特に UI 用の UMG Widgets を設計している時、ブループリントの非同期タスクを （ C++ で）作成し、 UMG のブループリントグラフから直接、 `ASC` の共通の変更デリゲートにバインドします。 唯一の注意点は、それらは手動で破棄する必要がある（ウィジェットを破棄するときなど）ことで、そうしないと永久にメモリに残り続けます。 サンプルプロジェクトには３つのブループリントの非同期タスクが含まれています。

アトリビュートの変更をリッスン :

![Listen for Attributes Changes BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

クールダウンの変更をリッスン :

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

`GE` のスタックの変更をリッスン :

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting"></a>

## 9. Troubleshooting

<a name="troubleshooting-notlocal"></a>

### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`

[クライアントで `ASC` の初期化](#concepts-asc-setup) が必要です。

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>

### 9.2 `ScriptStructCache` errors

[`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) の呼び出しが必要です。

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>

### 9.3 Animation Montages are not replicating to clients

[GameplayAbilities](#concepts-ga) で、 `PlayMontage` の代わりに `PlayMontageAndWait` ブループリントノードを使っているかを確認してください。 この [AbilityTask](#concepts-at) は `ASC` を介してモンタージュを自動的にレプリケーションしますが、 `PlayMontage` ノードはそうではありません。


**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>

### 9.4 Duplicating Blueprint Actors is setting AttributeSets to nullptr ( Blueprint のアクターを複製すると、AttributeSets が nullptr に設定される)

[Unreal Engine にバグ](https://issues.unrealengine.com/issue/UE-81109) があり、既存のブループリントアクタークラスを複製すると、 `AttributeSet` のポインタが nullptr に設定されてしまいます。 この問題はいくつかの回避策があります。 私は特別な `AttributeSet` のポインタをクラスに作成せず （ .h にポインタがなく、コンストラクタで `CreateDefaultSubobject` を呼び出さない ）、代わりに `AttributeSets` を `PostInitializeComponents()` 内で `ASC` に直接追加することに成功しました。（サンプルプロジェクトでは示されていません。）

```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// ... その他の AttributeSets があれば、それも含めます。
	}
}
```

このシナリオでは、[マクロで作られた `AttributeSet` 上の関数を呼び出す](#concepts-as-attributes) 代わりに、 `ASC` 上の関数を使って `AttributeSet` の値を読み書きします。


```c++
/** アトリビュートの現在値（最終値）を取得します。 */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** アトリビュートの基本値を設定します。既存のアクティブな modifier はクリアされず、新しい基本値に基づいて動作します。 */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

つまり、 `GetHealth()` `GetHealth()`は以下のようになります :

```c++
float AGDPlayerState::GetHealth() const
{
	if (AbilitySystemComponent)
	{
		return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());
	}

	return 0.0f;
}
```

ヘルス `Attribute` を設定（初期化）するには、以下のようになります。

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

念のため、 `ASC` が想定しているのは、 `AttributeSet` クラス毎に最大 1 つの `AttributeSet` オブジェクトです。

**[⬆ Back to Top](#table-of-contents)**

<a name="acronyms"></a>

## 10. Common GAS Acronyms （一般的な GAS の頭字語）

| Name                                                                                                     | 頭字語              |
|--------------------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                                   | ASC                 |
| AbilityTask                                                                                              | AT                  |
| [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)   | ARPG, ARPG Sample   |
| CharacterMovementComponent                                                                               | CMC                 |
| GameplayAbility                                                                                          | GA                  |
| GameplayAbilitySystem                                                                                    | GAS                 |
| GameplayCue                                                                                              | GC                  |
| GameplayEffect                                                                                           | GE                  |
| GameplayEffectExecutionCalculation                                                                       | ExecCalc, Execution |
| GameplayTag                                                                                              | Tag, GT             |
| ModifierMagnitudeCalculation                                                                             | ModMagCalc, MMC     |

**[⬆ Back to Top](#table-of-contents)**

<a name="resources"></a>

## 11. Other Resources

* [公式ドキュメント](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* ソースコード！
   * 特に `GameplayPrediction.h`
* [Epic による「 Action RPG 」サンプルプロジェクト](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) には GAS `#gameplay-ability-system` 専用の テキストチャンネルがあります。
   * ピン留めされたメッセージを確認してください
* [Dan 'Pan' による、リソースのGitHubのリポジトリ](https://github.com/Pantong51/GASContent)
* [SabreDartStudios による YouTube ビデオ](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>

### 11.1 Q&A With Epic Game's Dave Ratti

<a name="resources-daveratti-community1"></a>

#### 11.1.1 Community Questions 1

[GAS に関する Unreal Slackers Discord Server コミュニティの質問に対する Dave Ratti 氏の回答](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):


1. GameplayAbilities に関係なく、必要に応じてスコープ付きの prediction （予測）ウィンドウを作成するためにはどうすればよいでしょうか？例えば、ファイア・アンド・フォーゲット発射体が敵に当たった時に、ダメージの GameplayEffect を locally predict （ローカル予測）するには？

>> 補足
>> fire and forget projectile
>> ロックオンしたら発射だけで能動的に標的を追尾するような、自身が標的を追尾する能力を持つ発射体のこと。

> PredictionKey システムはこのような事を目的としたものではありません。
> 基本的にこのシステムでは、クライアントが predictive （予測）アクションを開始し、サーバーにキーとともに伝え、クライアントとサーバー両方で同じことを実行し、与えられている prediction key に predictive （予測）サイドエフェクトを関連付けることで機能します。
> 例えば、「 predictively （予測的）にアビリティを起動しています」や「ターゲットデータを生成したので、 WaitTargetData タスクのあとのアビリティのグラフの部分を predictively （予測的）に実行しようとしています」等です。
> 
> このパターンでは、 PredictionKey はサーバーから「跳ね返り」、 UAbilitySystemComponent::ReplicatedPredictionKeyMap （プロパティのレプリケーション）を介してクライアントに戻ってきます。
> キーがサーバーからレプリケーションされて戻ってくると、クライアントはすべての locally predictive （ローカル予測）された副作用（ GameplayCues, GameplayEffects ）をアンドゥすることができます：レプリケーションされたバージョンは **必ず存在** し、存在しない場合は misprediction （予測ミス）となります。
> （いつ predictive （予測）された副作用がアンドゥされたを正確に把握することが、ここでは重要です：早すぎれば隙間ができ、遅すぎると「二重」になってしまいます。GameplayCues と instant の Gameplay の「暴発」は決して「もとに戻す」こともロールバックすることもできません。関連付けられた prediction key （予測キー）がある場合、クライアント上でスキップされるだけです。）
> 
> 更に突っ込んで言うと： predictive （予測）アクションとは、サーバーが勝手に行うものではなく、クライアントが指示したときにのみ行うものであることが重要です。
> つまり、「必要に応じてキーを作成し、サーバーに伝えて何かを実行できるようにする」ための一般的な方法としては、（その「何か」が「クライアントに言われて初めてサーバーが実行するもの」でない限り、）機能しないのです。
> 
> 元の質問に戻ります：ファイア・アンド・フォーゲット発射体などについてです。
> Paragon と Fornite には、 GameplayCues を使用する発射体の Actor クラスがあります。
> しかし、我々はこれらを行うために Prediction Key （予測キー）システムを使用していません。
> その代わりに、 Non-Replicated GameplayCues のいう概念があります。
> GameplayCues はローカルで実行され、サーバーでは完全にスキップされます。
> これらは基本的に、 UGameplayCueManager::HandleGameplayCue を直接呼び出すことで実現しています。
> UAbilitySystemComponent を経由していないので、 Prediction Key （予測キー）のチェックやアーリーリターンは行われません。
> 
> レプリケーションされない GameplayCues の欠点は、レプリケーションされないというところです。
> そのため、これらの関数を呼び出すコードパスがすべての人に実行されるかどうかは、発射体クラス/ブループリント次第です。
> 我々は（ BeginPlay で呼び出される）スタートアップ、爆発、壁/キャラクターへの衝突などの cues があります。
> 
> これらの種類のイベントはクライアント側で既に生成されているので、レプリケーションしない GampeplayCue を呼び出すことは大したことではありませんでした。
> 複雑なブループリントは厄介なので、何がどこで実行されているかを理解しているかどうかは作者次第です。


2. WaitNetSync AbilityTask を OnlyServerWait を指定して、 locally predicted （ローカル予測）された GameplayAbility にスコープ付き Prediction （予測）ウィンドウを作成した場合、サーバーが prediction key （予測キー）を含む RPC を待っているため、サーバーへのパケットを遅らせて GameplayAbility のタイミングをコントロールすることで、プレイヤーは潜在的に不正行為が行えますか？ Paragon や Fortnite ではこのような問題が発生したことはありますか？また、発生した場合、 Epic はどのように対処しましたか？

>> 補足
>> exploits (exploit code)
>> 脆弱性実証コード、不正アクセス用攻撃プログラム。

> はい、これは重要な問題です。サーバー上で実行されているアビリティブループリントで、クライアントの「信号」を待っているものは、潜在的にエクスプロイト型のラグスイッチのように攻撃されやすいです。
> 
> Paragon には UAbilityTask_WaitTargetData に似た、カスタムのターゲッティングタスクがありました。
> このタスクではタイムアウトや、即時のターゲッティングモードのためにクライアントを待機させる「最大遅延」がありました。
> もしターゲッティングモードがユーザーの確認（ボタンを押す）を待っている場合は、ユーザーが時間をかけることができるので、それは無視されます。
> しかし、即時にターゲッティングを確認するアビリティについては、「A) サーバー側でターゲットデータを生成する」か「B) アビリティをキャンセルする」まで、一定の時間だけ待つことになります。
>
> WaitNetSync にはそのようなメカニズムはありませんでしたが、これは非常にまれにしか使われませんでした。
>
> Fortnite ではこのようなものを利用していないと思います。
> Fortnite の武器アビリティは Fortnite 固有の一つにバッチされた RPC で特殊なケースです：一つの RPC で、アビリティの起動、ターゲットの提供、アビリティの終了を行います。
> そのため、 Battle Royale (Fortnite) における武器アビリティは本質的にこのような脆弱性はありません。
>
> 私の考えでは、これはおそらくシステム全体で解決できることだと思いますが、私達がすぐに変更することはないでしょう。
> WaitNetSync をスポット的に修正して、あなたの言うケースの最大遅延を含めることはおそらく合理的なタスクですが、やはり当分の間、私達の側でこれを行うことはなさそうです。


3. どの EGameplayEffectReplicationMode を Paragon と Fortnite では使用していて、また、 Epic のおすすめは、いつそれぞれを使用するのか？

> どちらのゲームも、プレイヤーが操作するキャラクターには Mixed モードを、 AI が操作するキャラクター（AI ミニオン、ジャングルクリープ、AI ハスク等）には Minimal モードを基本的に使用しています。
> これは、マルチプレイヤーゲームでシステムを使用する多くの方におすすめしたい方法です。
> これらの設定は、プロジェクトの早い段階で行ったほうが良いでしょう。
> 
> Fortnite では、更に数歩進んだ最適化が行われています。
> 実際には simulated proxies については、 UAbilitySystemComponent を全く再現していません。
> コンポーネントとアトリビュートのサブオブジェクトは、所有する fortnite player state クラスの ::ReplicateSubobjects() 内でスキップされます。
> 複製された最低限のデータを、 AbilitySystemComponent からポーン自身の構造体にプッシュします（基本的には、アトリビュート値のサブセットとビットマスクでレプリケーションされたタグのホワイトリストのサブセット）。
> 我々はこれを「プロキシ」と呼んでいます。
> 受信側では、ポーンに複製されたプロキシデータを受け取り、 player state の AbilitySystemComponent にプッシュしなおします。
> FNBR (Fortnite) では各プレイヤーに ASC がありますが、直接レプリケーションされるわけではありません：その代わりに、ポーン上の最低限のプロキシ構造体を介してデータをレプリケーションし、受信側の ASC にルートバックします。
> これは、「A) より小さなデータセット」「B) ポーンの関連性を利用する」という点で有利です。
> 
> その後に行われた他のサーバーサイドの最適化（レプリケーショングラフなど）で、いまだに必要かどうかはわかりませんし、この方法は最もメンテナンス性が高いパターンでもありません。


4. GameplayPrediction.h にあるように、 GameplayEffects の除去を predict （予測）することができませんが、 GameplayEffects の除去におけるレイテンシーの影響を軽減する戦略はありますか？ 例えば、移動速度の低下を除去する時に、現在ではサーバーが GameplayEffect の除去を複製してプレイヤーのキャラクターの位置をスナップするのを待つ必要があります。

> これは難しい問題で、良い答えはありません。
> 私達は一般的に、許容範囲とスムージングでこの問題を回避してきました。
> AbilitySystem と CharacterMovementSystem との正確な同期が良い状態ではなく、修正したいと考えていることには完全に同意します。
> 
> 私は GE を predictive （予測的）に削除するようにすることを考えていましたが、すべてのエッジケースを解決してから次のステップに進むことはできませんでした。
> キャラクターの移動は AbilitySystem や移動速度補正の可能性などを知り得ない内部の保存された移動バッファを持っているので、これらはすべてを解決できるものではありません。
> GE の削除を予測できない以外にも、修正のフィードバックのループに陥る可能性もあります。
> 
> もし本当に絶望的なケースがあると考えるのであれば、移動速度 GE を阻害する GE を predictively （予測的）に追加することができます。
> 私自身はやったことはありませんが、以前に理論的に考えたことがあります。あるクラスの問題に役に立つ事ができるかもしれません。


5. AbilitySystemComponent は Paragon と Fortnite では PlayerState に、 Action RPG Sample では Character に置かれていることを知っています。
AbilitySystemComponent はどこに置かれるべきか、そのオーナーは何であるべきか、 Epic の内部規則、ガイドライン、又は推奨事項はなんですか？

> 一般的には、リスポーンする必要のないものは、オーナーとアバターアクターを同じものにすべきだと思います。 AI の敵、建物、ワールドの小道具などのようなものです。
> 
> リスポーンするものは、オーナーとアバターを別のものにして、 AbilitySystemComponent をリスポーン後にセーブオフ/再作成/復元 する必要がないようにします。
PlayerState は論理的な選択で、すべてのクライアントにレプリケーションされます（ PlayerController はそうではありません）。
欠点として、 PlayerState は常に relevant （関連している）ので、 100 人規模のゲームでは問題が発生する可能性があります。（質問 3 で FN (Fortnite) が行ったことについてのメモを参照してください）。


6. 所有者は同じだがアバターが異なる複数の AbilitySystemComponents を持つことは可能でしょうか（例えば、オーナーが PlayerState に設定されているポーンと weapon/items/projectiles の場合）？

> 私が考える最初の問題は、所有するアクターに IGameplayTagAssetInterface と IAbilitySystemInterface を実装することです。
> 前者は可能かもしれません：すべての ASC からタグを集約するだけです（ただし気をつけてください - HasAlMatchingGameplayTags は ASC のクロス縮約によってのみ満たされるかもしれません。その呼び出しを各 ASC に転送して、結果を一緒に OR するだけでは十分ではないでしょう）。
> しかし後者はさらに厄介です：どの ASC が権威があるものなのか？誰かが GE を適用したいと思ったとき、どの ASC がそれらを受け取るべきなのか？
> これらを解決できるかもしれませんが、この問題の側面は最も難しいでしょう：オーナーが複数の ASC を傘下に持つ。
> 
> ポーンと武器の ASC を分けることはそれだけで意味があります。
> 例えば、武器を説明するタグと所有するポーンを説明するタグを識別することができます。
> 武器に付与されたタグは所有者にも「適用」され、それ以外には適用されないというのも意味があるかもしれません（例えばアトリビュートと GE は独立していますが、所有者は上述のように所有しているタグを集約します）。
> これはうまくいくと思います。
> しかし、同じオーナーのもとに複数の ASC が存在するのは厄介なことになるかもしれません。


7. locally predicted （ローカル予測）されたアビリティのクールダウン期間を、サーバーが Owning Client （所有クライアント）に上書きしないようにする方法はありますか？ 遅延が大きいシナリオでは、ローカルのクールダウン期間が終了したが、サーバー上ではまだクールダウン期間中である場合に、 Owning Client （所有クライアント）が能力の有効化を再度「試みる」ことになります。 Owning Client （所有クライアント）の有効化の要求がネットワーク経由でサーバーに到達する頃には、サーバーはクールダウンを解除しているか、サーバーは残りのミリ秒の間、有効化の要求をキューに入れることができるかもしれません。 さもなければ、レイテンシーの高いクライアントは、レイテンシーの低いクライアントに比べて、アビリティを再起動するまでの時間が長くなります。 これは、クールダウンが 1 秒以下のベーシックアタックのように、クールダウンが非常に短いアビリティで顕著に現れます。 サーバーが locally predicted （ローカル予測）されるアビリティのクールダウン時間を上書きしないようにする方法がないとしたら、アビリティの再活性化に対する高いレイテンシーの影響を緩和するための Epic の戦略は何でしょうか？ 別の例に基づいた言い方をすると、 Paragon のベーシックアタックやその他のアビリティは、高いレイテンシーのプレイヤーが、 local prediction （ローカル予測）された低いレイテンシーのプレイヤーと同じ速度で攻撃や起動ができるように、 Epic はどのように設計したのでしょうか？

> 簡潔に答えると、これを防ぐ方法はなく、 Paragon にはこの問題がありました。高いレイテンシーの接続では、ベーシックアタックの ROF （rate of fire 、銃器の連射速度）が低くなります。
> 
> 私はこの問題を解決するために、 GE の duration を計算する際にレイテンシーを考慮する「 GE reconciliation」を追加しました。
> 基本的には、 GE の総時間の一部をサーバーが負担することで、 GE のクライアントの有効時間が、どのようなレイテンシーがあっても 100% 一貫したものになるようにしました（ただし変動すると問題が発生します）。
> しかしながら、これを出荷可能な状態にすることはできませんでしたし、プロジェクトの進行が早かったため、完全には対処できませんでした。
> 
> Fortnite では、武器の発射頻度について、独自のブックキーピング行っています：武器のクールダウンに GE を使用していません。これがゲームにとって重要な問題であれば、この方法をおすすめします。


8. GameplayAbilitySystem プラグインの Epic のロードマップはなんですか？ Epic は 2019 年以降にどのような機能を追加する予定ですか？

> 現時点では、全体的にシステムはかなり安定していると感じており、大きな新機能に取り組んでいる人はいません。
> Fortnite や UDN / プルリクエストなどでバグフィックスや小さな改善が行われることはありますが、今はそれだけです。
> 
> 長期的には、いずれ「 V2 」や大きな変更を行うことになると思います。
> 私達はこのシステムの開発から多くのことを学び、多くのことが正しく、多くのことが間違っていたと感じています。
> それらの間違いを修正したり、上で指摘された致命的な欠陥を改善する機会があれば嬉しいです。
> 
> もし V2 が登場することになれば、アップグレードパスの提供が何よりも重要になります。
> V2 を作って、 Fortnite を永遠に V1 のママにしておくようなことはしません：可能な限り自動的な移行ができるようなパスや手順を用意しますが、それでも手動でのリメイクが必要になることは間違いありません。
> 
> 優先度が高い修正点は以下のとおりです：
> * CharacterMovementSystem との相互運用性の向上。クライアント prediction （予測）の統一
> * GE の削除の prediction （予測） (質問 4)
> * GE のレイテンシーの調停  (質問 8)
> * RPC のバッチ処理やプロキシ構造など、一般的なんてっとワークの最適化。ほとんどが Fortnite のために行ったものですが、少なくともゲームが独自のゲーム固有の最適化をより簡単にかけるように、より一般化された形に分解する方法を見つけます。
> 
> より一般的なリファクタリングタイプの変更を検討しています：
> * GE がスプレッドシートの値を直接参照するのを根本的にやめて、代わりにパラメータを発行できるようにして、そのパラメータをスプレッドシートの値にバインドされた上位のオブジェクトが埋めるようにしたいと思います。  
>   現在のモデルの問題点は、 GE がカーブテーブルの行と密接に結合しているため、共有ができなくなってしまうことです。 パラメータ化のための一般化されたシステムを書けば、 V2 システムの基盤になると思います。
> * UGameplayAbility の「ポリシー」の数を減らします。  
>   私は ReplicationPolicy InstancingPolicy を削除します。  
>   レプリケーションは、 imo （ In My Opinion 、私の意見は）、実際にはほとんど必要なく、混乱の原因になっています。  
>   InstancingPolicy の代わりに、 FGameplayAbilitySpec をサブクラス化可能な UObject にすべきだと思います。  
>   これはイベントを持ち、ブループリント可能な「インスタンス化されていない能力オブジェクト」であるべきでした。  
>   UGameplayAbility は「実行ごとにインスタンス化される」オブジェクトであるべきです。  
>   実際にインスタンスを作成する必要がある場合はオプションにすることもできます：その代わり、「インスタンス化されない」アビリティは新しい UGameplayAbilitySpec を介して実装されます。
> * システムは「 filtered GE application container 」（どの GE をどのアクターに適用するかをより高レベルのゲームプレイロジックで駆動する）など、より「中間レベル」の構成要素を提供すべきです。  
>   「オーバーラッピングボリュームのサポート」（コリジョンプリミティブのオーバラップイベントに基づいて、「 Filtered GE application container 」を適用する）等です。  
>   これらは全てのプロジェクトがそれぞれの方法で実装することになるビルディングブロックです。  
>   これらを正しく実装することはかんたんなことではありませんので、基本的な実装方法をもっとうまく提供すべきだと思います。
> * 一般的に、プロジェクトを立ち上げるために必要な定型文を減らします。  
>   おそらく、パッシブアビリティやベーシックヒットスキャンの武器のようなものを out of the box （すぐに使える状態で）提供するために、「 Ex ライブラリ」などの別モジュールを用意するでしょう。  
>   このモジュールはオプションですが、すぐに稼働させることができるでしょう。
> * GameplayCues をアビリティシステムと連動しない別のモジュールに移したいと思います。  
>   ここは改善すべき点がたくさんあると思います。

これは私の個人的な意見であり、誰かに約束されたものではありません。
最も現実的な方法は、新しいエンジン技術の構想が出た時に、アビリティシステムを更新する必要があり、その時にこのようなことをするのではないかと考えています。
このような取り組みは、スクリプト、ネットワーク、物理/キャラクターの移動などに関連するものです。
しかし、これは非常に先のことなので、タイムラインの確約や見積もりはできません。


**[⬆ Back to Top](#table-of-contents)**

<a name="resources-daveratti-community2"></a>

#### 11.1.2 Community Questions 2
Community member [iniside](https://github.com/iniside)'s Q&A with Dave Ratti:

1. Is the support for decoupled fixed ticking planned ? I'd like to
have Game Thread be fixed (like 30/60fps) and let the rendering thread
run wild. I ask if this is something we should expect in future or
not, to make some assumptions about how gameplay should work.
I ask mainly because there is now a fixed async tick for physics and
this poses a question how the rest of the system might work in the
future. I do not hide that having the ability to have fixed tick game
thread without also fixing tick rate of the rest of the engine would
be beyond awesome.

> There are no plans to decouple rendering frame rate and game thread tick frame rate. I think the ship has sailed on this ever happening due to the complexity of these systems and the requirement to preserve backwards compatibility with previous engine versions.
>
> Instead, the direction we've gone is to have an asynchronous "Physics Thread" which runs at a fixed tick rate, independent of the game thread. Things that need to run at a fixed rate can run here and the game thread / rendering can operate how they always have.
>
> It's worth clarifying that Network Prediction supports what it calls Independent Ticking and Fixed Ticking modes. My long term plan is to keep Independent Ticking roughly how it is today in Network Prediction where it runs on the game thread at variable frame rate and there is no "group/world" prediction, it's just the classic "clients predict their own pawn and owned actors" model. And Fixed Ticking would be what uses the async physics stuff and allows you to predict non client controlled/owned actors like physics objects and other clients/pawns/vehicles/etc.


2. Is there any plan on how the integration of Network Prediction will
look with the Ability System ? Like for example, fixed frame ability
activation (so the server gets frames in which abilities were
activated and tasks executed instead of prediction keys) ?

> Yes, the plan is to rewrite/remove the Ability System's prediction keys and replace them with Network Prediction constructs. The MockAbility examples in NetworkPredictionExtras show how this might work but they are more "hard coded" than what GAS will require. 
>
> The main idea would be that we remove the explicit client->server Prediction Key exchange in the ASC's RPCs. There would no longer be prediction windows or scoped prediction keys. Instead everything would be anchored around NetworkPrediction frames. The important thing is that client and server agree on when things happen. Examples would be:
>
> * When abilities were activated/ended/cancelled
> * When Gameplay Effects were applied/removed
> * Attribute values (what an attributes value was at frame X)
>
> I think this could be done generically at the ability system level. But actually making the user-defined logic inside a UGameplayAbility completely rollback-able would still take more work. We may end up having a subclass of UGameplayAbility that is fully rollbackable and has access to a more limited set of functionality or only Ability Tasks that are marked as rollback-friendly. Something like that. There are also many implications to animation events and root motion and how those are processed.
>
> Wish I had a more clear answer but it's really important we get the foundation right before touching GAS again. Movement and physics have to be solid before the higher level systems can be changed.


3. Is there a plan to move Network Prediction development toward the
main branch ? Not gonna lie, I'd really like to check the latest code.
Regardless of it's state.

> We are working towards it. The system work is still all being done in NetworkPrediction (see NetworkPhysics.h) and the underlying async physics stuff should be all available (RewindData.h etc). But we also have use cases in Fortnite that we have been focused on that obviously can't be made public. We are working through bugs, performance optimizations, etc.
>
> For more context: when working on the early versions of this system, we were very focused on the "front end" of things - how state and simulations were defined and written. We learned a lot there. But as the async physics stuff has come online, we've been much more focused on just getting something real to work in this system, at the expense of throwing out some of our early abstractions. The goal here is to circle back when the real thing is working and reunifying things. E.g, get back to the "front end" and make the final version of that on top of the core pieces of tech we are working on now.


4. For some time on Main there was a plugin for sending Gameplay
Messages (Looked like Event/Message Bus), but it was removed. Any
plans to restore it ? With the Game Features/Modular Gameplay plugins,
having a generic Event Bus Dispatcher would be extremely useful.

> I think you are referring to the GameplayMessages plugin. This will probably come back at some point - the API isn't really finalized yet and the author didn't mean for it to be public yet. I agree it should be useful for modular gameplay design. But it's not really my area so I don't have much more information. 


5. I've been playing recently with async fixed physics and the results
are promising, though if there is going to be NP update in the future
I will probably just play around and wait, since to get it working I
still need to get entire engine into fixed tick and on the other hand
I try to keep physics at 33ms. Which does not make for a good
experience if everything is at 30 fps (:.

I have noticed there was some work on Async
CharacterMovementComponent, but not sure if this will be using Network
Prediction, or it is a separate effort ?

Since I noticed it, I also went ahead and tried to implement my custom
async movement at fixed tick rate, which worked okay, but on top of it
I also needed to add a separate update for interpolation. The setup
was to run simulation tick on separate worker threads at fixed 33ms
update, do calculations, save result, and interpolate it at the game
thread to match current frame rate. Not perfect, but it got the job
done.

My question is, if this is something that might be easier to set up in
the future, as there is just quite a bit of boilerplate code to write,
(the interpolation part) and it's not particularly efficient to
interpolate each moving object individually.

The async stuff is really interesting, because it would allow you to
really run game simulation at fixed update rate (which would make
fixed thread unneeded) and have more predictable results. Is this
something that is intended going forward, or more of a benefit to
select systems ? As far as I remember actor transforms are not updated
async and blueprints are not entirely thread safe. In other words is
it something that is planned to be supported at more of a framework
level or something that each game has to solve on it's own ?

> Async CharacterMovementComponent
>
> This is basically an early prototype/experiment of porting CMC as it is to the physics thread. I don't view it as the future of CMC yet, but it could evolve into that. Right now there is no networking support so it's not something I would really follow. The people doing it are mostly concerned with measuring input latency that this system would add and how that could be mitigated.
>
> I still need to get entire engine into fixed tick and on the other hand I try to keep physics at 33ms. Which does not make for a good experience if everything is at 30 fps (:.
>
> The async stuff is really interesting, because it would allow you to really run game simulation at fixed update rate (which would make fixed thread unneeded) 
>
> Yes. The goal here is that with async physics enabled, you can run the engine at variable tick rate while the physics and "core" gameplay simulations can run at the fixed rate (such as character movement, vehicles, GAS, etc).
>
> These are the cvars that need to be set to enable this now: (I think you've figured this out)  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> Chaos does provide interpolation for the physics state (E.g, the transforms that get pushed back to the UPrimitiveComponent and are visible to the game code). There is a cvar now, `p.AsyncInterpolationMultiplier`, which controls that if you want to look at it. You should see smooth continuous motion of physics bodies without having to write any extra code. 
>
> If you want to interpolate non physics state, it is still up to you to do that right now. The example would be like a cool-down that you want to update (tick) on the async physics thread but see smooth continuous interpolation on the game thread so that every render frame the cool down visualization is updated. We will get to this eventually but don't have examples yet.
>
> there is just quite a bit of boilerplate code to write,
>
> Yeah, so that has been a big general problem with the system up until now. We want to provide an interface that experienced programmers can use to maximize performance and safety (the ability to write gameplay code that "just works" predictively without tons of hazards and things you could-do-but-better-not). So something like CharacterMoverment might do a bunch of custom stuff to maximize its performance - e.g, writing templated code and doing batch updating, going wide, breaking the update loop into distinct phases etc. We want to provide a good "low level" interface into the async thread and rollback systems for this use case. And in this case too - it's still reasonable that the character movement system itself is extendable in its own way. For example providing a way to blueprint a custom movement mode and providing a blueprint API that is thread safe.
>
> But we recognize this is not acceptable for simpler gameplay objects that don't really need their own "system". Something more inline with Unreal is what is needed. E.g, using the reflection system, having general blueprint support, etc. There are examples of blueprints being used on other threads (see BlueprintThreadSafe keyword and what the animation system has been working towards). So I think there will be some form of this one day. But again, we aren't there yet.
>
> I realize you were just asking about interpolation but that is the general answer: right now we have you do everything manually like NetSerialize, ShouldReconcile, Interpolate, etc but eventually we'll have a way that is like "if you want to just use the reflection system, you don't have to manually write this stuff". We just don't want to *force* everyone to use the reflection system since that imposes other limitations that we think we don't want to take on the lowest levels of the system. 
>
> And then just to tie this back to what I said earlier - right now we are really focused on getting a few very specific examples working and performant and then we will turn attention back to the front end and making things friendly to use and iterate on, reducing boilerplate, etc for everybody else to use. 

**[⬆ Back to Top](#table-of-contents)**

<a name="changelog"></a>

## 12. GAS Changelog

これは「公式の Unreal Engine の更新変更ログ」と「私が遭遇したドキュメント化されていない変更」から集めた、 GAS の注目すべき変更（修正、変更、そして新しい機能）のリストです。 もしあなたがここに記載されていないなにかを見つけたならば、 issue を作成するか、プルリクエストを行ってください。

<a name="changelog-4.26"></a>
### 4.26
* GAS plugin is no longer flagged as beta.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Added the path string arg to a message to fix a crash in UGameplayCueManager::VerifyNotifyAssetIsInValidPath.
* Crash Fix: Fixed an access violation crash in AbilitySystemComponent_Abilities when using a ptr without checking it.
* Bug Fix: Fixed a bug where stacking GEs that did not reset the duration on additional instances of the effect being applied.
* Bug Fix: Fixed an issue that caused CancelAllAbilities to only cancel non-instanced abilities.
* New: Added optional tag parameters to gameplay ability commit functions.
* New: Added StartTimeSeconds to PlayMontageAndWait ability task and improved comments.
* New: Added tag container "DynamicAbilityTags" to FGameplayAbilitySpec. These are optional ability tags that are replicated with the spec. They are also captured as source tags by applied gameplay effects.
* New: GameplayAbility IsLocallyControlled and HasAuthority functions are now callable from Blueprint.
* New: Visual logger will now only collect and store info about instant GEs if we're currently recording visual logging data.
* New: Added support for redirectors on gameplay attribute pins in blueprint nodes.
* New: Added new functionality for when root motion movement related ability tasks end they will return the movement component's movement mode to the movement mode it was in before the task started.

<a name="changelog-4.25.1"></a>
### 4.25.1
* Fixed! UE-92787 Crash saving blueprint with a Get Float Attribute node and the attribute pin is set inline
* Fixed! UE-92810 Crash spawning actor with instance editable gameplay tag property that was changed inline

<a name="changelog-4.25"></a>
### 4.25
* Fixed prediction of `RootMotionSource` `AbilityTasks`
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) now additionally takes in the old `Attribute` value. We must supply that as the optional parameter to our `OnRep` functions. Previously, it was reading the attribute value to try to get the old value. However, if called from a replication function, the old value had already been discarded before reaching SetBaseAttributeValueFromReplication so we'd get the new value instead.
* Added [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy) to `UGameplayAbility`.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Removed a few ways for attackers to crash a server through the ability system.
* Crash Fix: We now make sure we have a GameplayEffect definition before checking tag requirements.
* Bug Fix: Fixed an issue with gameplay tag categories not applying to function parameters in Blueprints if they were part of a function terminator node.
* Bug Fix: Fixed an issue with gameplay effects' tags not being replicated with multiple viewports.
* Bug Fix: Fixed a bug where a gameplay ability spec could be invalidated by the InternalTryActivateAbility function while looping through triggered abilities.
* Bug Fix: Changed how we handle updating gameplay tags inside of tag count containers. When deferring the update of parent tags while removing gameplay tags, we will now call the change-related delegates after the parent tags have updated. This ensures that the tag table is in a consistent state when the delegates broadcast.
* Bug Fix: We now make a copy of the spawned target actor array before iterating over it inside when confirming targets because some callbacks may modify the array.
* Bug Fix: Fixed a bug where stacking GameplayEffects that did not reset the duration on additional instances of the effect being applied and with set by caller durations would only have the duration correctly set for the first instance on the stack. All other GE specs in the stack would have a duration of 1 second. Added automation tests to detect this case.
* Bug Fix: Fixed a bug that could occur if handling gameplay event delegates modified the list of gameplay event delegates.
* Bug Fix: Fixed a bug causing GiveAbilityAndActivateOnce to behave inconsistently.
* Bug Fix: Reordered some operations inside FGameplayEffectSpec::Initialize to deal with a potential ordering dependency.
* New: UGameplayAbility now has an OnRemoveAbility function. It follows the same pattern as OnGiveAbility and is only called on the primary instance of the ability or the class default object.
* New: When displaying blocked ability tags, the debug text now includes the total number of blocked tags.
* New: Renamed UAbilitySystemComponent::InternalServerTryActiveAbility to UAbilitySystemComponent::InternalServerTryActivateAbility.Code that was calling InternalServerTryActiveAbility should now call InternalServerTryActivateAbility.
* New: Continue to use the filter text for displaying gameplay tags when a tag is added or deleted. The previous behavior cleared the filter.
* New: Don't reset the tag source when we add a new tag in the editor.
* New: Added the ability to query an ability system component for all active gameplay effects that have a specified set of tags. The new function is called GetActiveEffectsWithAllTags and can be accessed through code or blueprints.
* New: When root motion movement related ability tasks end they now return the movement component's movement mode to the movement mode it was in before the task started.
* New: Made SpawnedAttributes transient so it won't save data that can become stale and incorrect. Added null checks to prevent any currently saved stale data from propagating. This prevents problems related to bad data getting stored in SpawnedAttributes.
* API Change: AddDefaultSubobjectSet has been deprecated. AddAttributeSetSubobject should be used instead.
* New: Gameplay Abilities can now specify the Anim Instance on which to play a montage.

<a name="changelog-4.24"></a>
### 4.24
* Fixed blueprint node `Attribute` variables resetting to `None` on compile.
* Need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) to use [`TargetData`](#concepts-targeting-data) otherwise you will get `ScriptStructCache` errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a `GameplayTag` setter to a blueprint that didn't have the variable previously defined.
* `UGameplayAbility::MontageStop()` function now properly uses the `OverrideBlendOutTime` parameter.
* Fixed `GameplayTag` query variables on components not being modified when edited.
* Added the ability for `GameplayEffectExecutionCalculations` to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
   * Implementation basically enables `GameplayTag`-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
   * To use, an execution has to add a tag to the new member variable `ValidTransientAggregatorIdentifiers`; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted `GameplayTag` source. We no longer reset the source when adding restricted tags to make it easier to add several in a row. 
* `APawn::PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`. Useful because [Mixed Replication Mode](#concepts-asc-rm) expects the owner of the `Pawn` to be the `Controller` if the `ASC` lives on the `Pawn`.
* Fixed bug with POD (Plain Old Data) in `FAttributeSetInitterDiscreteLevels`.

**[⬆ Back to Top](#table-of-contents)**

-----

<!--- 
引き続きこのあたりから。

SentyaAnko

| 文型   | 訳                                           |
| ------ | -------------------------------------------- |
| sv     | s は v する                                  |
| svc    | s は c である(ように v する)                 |
| svo    | s は o を(に) v する                         |
| svoo   | s は o1 に o2 を v する                      |
| svoc   | s は o が c (な状態)であるように v する      |



--->


<!--- working --->

-----
