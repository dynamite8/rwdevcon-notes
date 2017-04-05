## Synopsis


### Breakpoint Commands

```breakpoint set``` most flexible but most typing

```b``` breakpoint in same file, or simple functioon names, memory addresses

```rbreak``` creates regular expression breakpoint

Perform a one shot breakpoint and goes away
```rb . -o```

Break on every function in this hello.swift class ```rbreak . -f hello.swift```

Search for every function in framework ```rb . -s CallKit```

breakpoint on every if let
```breakpoint set if let```

### What now after stopping in code?

```p``` and ```po```

```po``` expression -o

Default to ObjectiveC

Control language your command is executed. This is an example of swift.
```expression -lswift -o -- let a = "hello world"; a```

Don't ignore breakpoints
expression -i0 -o -- [UIApplication sharedApplication]

Debug your own expressions.  Pause execution command and let you debug your own lldb expression. ```expression -g -o -logic -- @"hello world"```

### Custom LLDB Commands

#### Command alias

Display the main bundle
```command alias bundle expression -lobjc -o --[NSBundle mainBundle]```

Run multiple commands if they're on the same line
* Use xcode to create your code snippet
* ```pbpaste \ tr -d '\n' | pbcopy``` paste and get rid of all the new lines
* Paste into your command alias script
* Iterate if needed (i.e., LLDB JIT build errors)

#### Command Regex

```command regex name 's/(REGEX)/COMMAND/'```

Example: po using the Objective-C context ```command regex cpo -- 's/(.+)/expression -lobjc -O -- %1/'```

one or more character, it'll run this expression

~/.lldbinit

Save all your commands in there so that they'll persist.

### Demo 2
```
(lldb) expression -lobjc -o -- [[[UIApplication sharedApplication] keyWindow] rootViewController]
<UINavigationController: 0x7ff46a818400>

(lldb) command alias
error: 'command alias' requires at least two arguments
(lldb) command alias root expression -lobjc -o -- [[[UIApplication sharedApplication] keyWindow] rootViewController]
(lldb) root
<UINavigationController: 0x7ff46a818400>
```

expression -lobjc -o -- [UIView _shortMethodDescription]

(lldb) command regex method 's/(.+)/expression -lobjc -o -- [UIView _shortMethodDescription]/'


(lldb) command regex method 's/(.+)/expression -lobjc -o -- [%1 _shortMethodDescription]/'

(lldb) dclass -c UITableViewDelegate
Dumping all classes which conform to UITableViewDelegate

Summary of the classes that have viewDidLoad
(lldb) lookup viewDidLoad -s

(lldb) lookup viewDidLoad -m MapKit
****************************************************
26 hits in: MapKit

## Dynamic Libraries

* A library that is loaded into your app at runtime like UIKit
* List of dynamic libraries in your app ```image list```

```otool -l Secret```

/System/Library/Frameworks
/Usr/lib/
/System/Library/PrivateFrameworks


```
(lldb) dclass -r (?i)invisible
Dumping all classes
************************************************************
EKUIInviteesViewInvisibleInviteeStatusSection
CKInvisibleInkEffectCoverageTracker
CKInvisibleInkEffectController
CKInvisibleInkGestureRecognizer
UIInputViewSetPlacementInvisible
CKInvisibleInkEffectView
CKInvisibleInkTextEffectView
CKInvisibleInkEmojiEffectView
CKInvisibleInkImageEffectView



(lldb) dclass -r (?i)invisible -v
Dumping all classes
************************************************************
EventKitUI: EKUIInviteesViewInvisibleInviteeStatusSection
ChatKit: CKInvisibleInkEffectCoverageTracker
ChatKit: CKInvisibleInkEffectController
ChatKit: CKInvisibleInkGestureRecognizer
UIKit: UIInputViewSetPlacementInvisible
ChatKit: CKInvisibleInkEffectView
ChatKit: CKInvisibleInkTextEffectView
ChatKit: CKInvisibleInkEmojiEffectView
ChatKit: CKInvisibleInkImageEffectView

```

```
(lldb) dclass -r (?i)invisible -v
Dumping all classes
************************************************************
EventKitUI: EKUIInviteesViewInvisibleInviteeStatusSection
ChatKit: CKInvisibleInkEffectCoverageTracker
ChatKit: CKInvisibleInkEffectController
ChatKit: CKInvisibleInkGestureRecognizer
UIKit: UIInputViewSetPlacementInvisible
ChatKit: CKInvisibleInkEffectView
ChatKit: CKInvisibleInkTextEffectView
ChatKit: CKInvisibleInkEmojiEffectView
ChatKit: CKInvisibleInkImageEffectView



(lldb) image list chatkit
error: no modules found that match 'chatkit'
(lldb) image list ChatKit
[  0] 5AC11B70-9CDF-38E2-A037-FF8AD5089EEB 0x02bfe000 /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator.sdk//System/Library/PrivateFrameworks/ChatKit.framework/ChatKit
(lldb)
```


```methods CKInvisibleInkImageEffectView```

(lldb) search CKInvisibleInkImageEffectView
<__NSArrayM 0x174246600>(
<CKInvisibleInkImageEffectView: 0x139e19da0; frame = (0 0; 374 451); layer = <CALayer: 0x170038440>>
)

(lldb) po [[0x139e19da0 class] superclass]
CKInvisibleInkEffectView

(lldb) search CKInvisibleInkEffectView
<__NSArrayM 0x1702493c0>(
<CKInvisibleInkImageEffectView: 0x139e19da0; frame = (0 0; 374 451); layer = <CALayer: 0x170038440>>
)

Can read the memory of the app
yoink /System/Library/Messages/iMessageEffects/CKFireworksEffect.bundle/CKFireworksEffect

/System/Library/Messages/iMessageEffects/CKFireworksEffect.bundle/CKFireworksEffect

```Objective-C
changho@C02QT1XTG8WP:~$ class-dump /private/tmp/CKFireworksEffect
//
//     Generated by class-dump 3.5 (64 bit).
//
//     class-dump is Copyright (C) 1997-1998, 2000-2001, 2004-2013 by Steve Nygard.
//

#pragma mark Named Structures

struct CGPoint {
    double x;
    double y;
};

struct CGRect {
    struct CGPoint origin;
    struct CGSize size;
};

struct CGSize {
    double width;
    double height;
};

#pragma mark -

//
// File: /tmp/CKFireworksEffect
// UUID: F181DA32-1AEF-316D-A2D8-7190B06170E6
//
//                           Arch: arm64
//                 Source version: 1870.32.18.0.0
//            Minimum iOS version: 10.2.0
//                    SDK version: 10.2.0
//
// Objective-C Garbage Collection: Unsupported
//

@protocol CKFullscreenEffectView
@property(nonatomic) BOOL messageOrientation;
@property(nonatomic) struct CGRect messageRect;
@property(nonatomic) struct CGPoint focusPoint;
@property(nonatomic) long long zIndex;
- (void)stopAnimation;
- (void)startAnimation;
@end

@interface GameScene : SKScene
{
    SKEmitterNode *_orbParticleEmitter;
    SKEmitterNode *_trailParticleEmitter;
    SKEmitterNode *_sparklesParticleEmitter;
    SKEmitterNode *_smokeParticleEmitter;
    SKNode *_trailContainer;
    SKSpriteNode *_lightHalo;
    SKTexture *_emitterGlowSphereTexture;
    SKTexture *_sparkTexture;
    SKTexture *_sparkSoftGlowTexture;
    SKTexture *_backGlowTexture;
    SKTexture *_sparklesType1Texture;
    SKTexture *_sparklesType2Texture;
    UIColor *_type2FireworkInnerPinkColor;
    UIColor *_type2FireworkOuterPinkColor;
    SKKeyframeSequence *_type2FireworkInnerPinkColorSequence;
    SKKeyframeSequence *_type2FireworkOuterPinkColorSequence;
    UIColor *_type2FireworkInnerBlueColor;
    UIColor *_type2FireworkOuterBlueColor;
    SKKeyframeSequence *_type2FireworkInnerBlueColorSequence;
    SKKeyframeSequence *_type2FireworkOuterBlueColorSequence;
    UIColor *_type2FireworkInnerGreenColor;
    UIColor *_type2FireworkOuterGreenColor;
    SKKeyframeSequence *_type2FireworkInnerGreenColorSequence;
    SKKeyframeSequence *_type2FireworkOuterGreenColorSequence;
    UIColor *_whiteColor;
    UIColor *_type1SmokeColor;
    UIColor *_backgroundColor;
    UIColor *_backgroundLitColor;
    SKKeyframeSequence *_type1FireworkSequence1;
    SKKeyframeSequence *_type1FireworkSequence2;
    SKFieldNode *_dragField;
    SKFieldNode *_noiseField;
    SKFieldNode *_intenseNoiseField;
    _Bool _type1FireworkEnabled;
    _Bool _type2FireworkEnabled;
    int _type1FireworkTrailCount;
    float _type1FireworkScale;
    float _type2FireworkScale;
    float _backGlowAlpha;
    float _backGlowScale;
    float _backGlowDuration;
    _Bool _shouldReduceParticles;
    float _globalScale;
}

@property(nonatomic) _Bool shouldReduceParticles; // @synthesize shouldReduceParticles=_shouldReduceParticles;
@property(nonatomic) float globalScale; // @synthesize globalScale=_globalScale;
@property(nonatomic) _Bool type2FireworkEnabled; // @synthesize type2FireworkEnabled=_type2FireworkEnabled;
@property(nonatomic) _Bool type1FireworkEnabled; // @synthesize type1FireworkEnabled=_type1FireworkEnabled;
- (void).cxx_destruct;
- (float)launchTrailAt:(struct CGPoint)arg1 angle:(float)arg2 speed:(float)arg3 group:(id)arg4;
- (void)runTrailParticleEmitter:(struct CGPoint)arg1 scale:(float)arg2;
- (void)runEndingSparkleParticleEmitter:(struct CGPoint)arg1 scale:(float)arg2 radius:(float)arg3 color:(id)arg4;
- (void)runOrbParticleEmitter:(struct CGPoint)arg1 scale:(float)arg2 color:(id)arg3;
- (void)runSmokeEmitter:(struct CGPoint)arg1 scale:(float)arg2 color:(id)arg3;
- (void)runSparkleEmitter:(struct CGPoint)arg1 scale:(float)arg2 rangeRatio:(float)arg3 particleSizeRatio:(float)arg4 color:(unsigned long long)arg5 texture:(id)arg6;
- (void)createFireworkTypeTwoAt:(struct CGPoint)arg1 scale:(float)arg2 color:(unsigned long long)arg3;
- (void)createFireworkTypeOneAt:(struct CGPoint)arg1 scale:(float)arg2;
- (void)runLightHaloEffectAt:(struct CGPoint)arg1 scale:(float)arg2 color:(unsigned long long)arg3;
- (void)launchFireworkAt:(struct CGPoint)arg1 type:(unsigned long long)arg2 scale:(float)arg3 color:(unsigned long long)arg4;
- (void)runChoregraphy;
- (void)setup;
- (id)init;

@end

@interface CKFireworksEffect : CKFullScreenEffect
{
    NSArray *_filters;
}

@property(retain, nonatomic) NSArray *filters; // @synthesize filters=_filters;
- (void).cxx_destruct;
- (id)messageFilters;
- (id)backgroundColor;
- (double)duration;
- (_Bool)shouldDrawOverNavigationBar;
- (Class)effectViewClass;
- (unsigned long long)layerCount;
- (_Bool)soundEffectHasHapticTrack;
- (id)soundEffectFileURL;
- (id)init;

@end

@interface CKFireworksEffectView : UIView <CKFullscreenEffectView>
{
    SKView *_fireworksView;
    GameScene *_scene;
    BOOL messageOrientation;
    long long zIndex;
    struct CGPoint focusPoint;
    struct CGRect messageRect;
}

@property(nonatomic) BOOL messageOrientation; // @synthesize messageOrientation;
@property(nonatomic) struct CGRect messageRect; // @synthesize messageRect;
@property(nonatomic) struct CGPoint focusPoint; // @synthesize focusPoint;
@property(nonatomic) long long zIndex; // @synthesize zIndex;
- (void).cxx_destruct;
- (void)stopAnimation;
- (void)applicationWillEnterForegroundNotification;
- (void)applicationDidEnterBackground;
- (void)applicationDidBecomeActive;
- (void)applicationWillResignActive;
- (id)initWithFrame:(struct CGRect)arg1;
- (void)startAnimation;

@end
```

Attach simulator, sprinboard
bring all the windows

(lldb) search NSWindow
<__NSArrayM 0x6080014556f0>(
<AtomNSWindow: 0x7fcd9b41ccc0>,
<NSToolbarFullScreenWindow: 0x7fcd9e115d60> - originalWindow.title: Slack - RWDevCon,
<NSToolTipPanel: 0x7fcd9b441a60>
)

(lldb) search NSWindow -p "[obj setLevel:1000]"
<__NSArrayM 0x60000164d9b0>(
<AtomNSWindow: 0x7fcd9b41ccc0>,
<NSToolbarFullScreenWindow: 0x7fcd9e115d60> - originalWindow.title: Slack - RWDevCon,
<NSToolTipPanel: 0x7fcd9b441a60>
)

Select the process and pause it to get the lldb
```
(lldb) search UIScrollView
<__NSArrayM 0x61800065a5b0>(
<CCUIImmediateTouchScrollView: 0x7fbe51014200; baseClass = UIScrollView; frame = (0 0; 0 0); gestureRecognizers = <NSArray: 0x618000641ad0>; layer = <CALayer: 0x61000022a620>; contentOffset: {0, 0}; contentSize: {0, 0}>,
<NCNotificationListCollectionView: 0x7fbe5384d600; baseClass = UICollectionView; frame = (0 0; 414 736); clipsToBounds = YES; autoresize = W+H; gestureRecognizers = <NSArray: 0x61800045b570>; layer = <CALayer: 0x61800042e840>; contentOffset: {0, 0}; contentSize: {414, 0}> collection view layout: <NCNotificationListCollectionViewFlowLayout: 0x7fbe542269f0>,
<UICollectionView: 0x7fbe5285a000; frame = (0 0; 414 736); clipsToBounds = YES; autoresize = W+H; gestureRecognizers = <NSArray: 0x600000644d10>; layer = <CALayer: 0x600000228d80>; contentOffset: {0, 0}; contentSize: {0, 0}> collection view layout: <UICollectionViewFlowLayout: 0x7fbe507554f0>,
<UIScrollView: 0x7fbe50809200; frame = (0 0; 398 1404); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x61800025daf0>; layer = <CALayer: 0x618000236760>; contentOffset: {0, 0}; contentSize: {398, 162}>,
<SearchUITableView: 0x7fbe5086dc00; baseClass = UITableView; frame = (0 0; 0 0); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x618000258db0>; layer = <CALayer: 0x618000235040>; contentOffset: {0, 0}; contentSize: {0, 0}>,
<SBSearchScrollView: 0x7fbe51888a00; baseClass = UIScrollView; frame = (0 0; 0 0); clipsToBounds = YES; hidden = YES; gestureRecognizers = <NSArray: 0x60800044fcc0>; layer = <CALayer: 0x6080002200a0>; contentOffset: {0, 94}; contentSize: {0, 94}>,
<UITableViewWrapperView: 0x7fbe5099ea00; frame = (0 0; 0 1); gestureRecognizers = <NSArray: 0x618000652b70>; layer = <CALayer: 0x61800043c120>; contentOffset: {0, 0}; contentSize: {0, 1}>,
<SearchUITableView: 0x7fbe51068200; baseClass = UITableView; frame = (0 0; 0 0); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x6100004421f0>; layer = <CALayer: 0x6100000398e0>; contentOffset: {0, 0}; contentSize: {0, 0}>,
<CCUIImmediateTouchScrollView: 0x7fbe530b7800; baseClass = UIScrollView; frame = (0 0; 0 0); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x608000646720>; layer = <CALayer: 0x60800022b520>; contentOffset: {0, 0}; contentSize: {0, 0}>,
<UICollectionView: 0x7fbe5203f600; frame = (0 0; 414 736); clipsToBounds = YES; autoresize = W+H; gestureRecognizers = <NSArray: 0x600000644bf0>; layer = <CALayer: 0x61000022e840>; contentOffset: {0, 0}; contentSize: {0, 0}> collection view layout: <UICollectionViewFlowLayout: 0x7fbe54011a00>,
<UITableView: 0x7fbe509c6200; frame = (0 0; 0 0); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x618000652810>; layer = <CALayer: 0x61800043c260>; contentOffset: {0, 0}; contentSize: {0, 0}>,
<CCUIImmediateTouchScrollView: 0x7fbe53060800; baseClass = UIScrollView; frame = (0 0; 0 0); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x618000647590>; layer = <CALayer: 0x608000228400>; contentOffset: {0, 0}; contentSize: {0, 0}>,
<UITableViewWrapperView: 0x7fbe50891a00; frame = (-8 0; 16 1); gestureRecognizers = <NSArray: 0x618000259200>; layer = <CALayer: 0x6180002350e0>; contentOffset: {0, 0}; contentSize: {16, 1}>,
<SBIconScrollView: 0x7fbe52807000; baseClass = UIScrollView; frame = (0 20; 414 604); autoresize = W+H; gestureRecognizers = <NSArray: 0x600000251c10>; layer = <CALayer: 0x600000223d20>; contentOffset: {828, 0}; contentSize: {1242, 604}>,
<SBPagedScrollView: 0x7fbe51052600; baseClass = UIScrollView; frame = (0 0; 828 1404); clipsToBounds = YES; gestureRecognizers = <NSArray: 0x61000025e9c0>; layer = <CALayer: 0x610000035b60>; contentOffset: {0, 0}; contentSize: {1656, 1404}>,
<UITableViewWrapperView: 0x7fbe51021000; frame = (-8 0; 16 1); gestureRecognizers = <NSArray: 0x6100004426a0>; layer = <CALayer: 0x610000038640>; contentOffset: {0, 0}; contentSize: {16, 1}>
)

(lldb) search UIScrollView -b
CCUIImmediateTouchScrollView * [0x00007fbe51014200]
NCNotificationListCollectionView * [0x00007fbe5384d600]
UICollectionView * [0x00007fbe5285a000]
UIScrollView * [0x00007fbe50809200]
SearchUITableView * [0x00007fbe5086dc00]
SBSearchScrollView * [0x00007fbe51888a00]
UITableViewWrapperView * [0x00007fbe5099ea00]
SearchUITableView * [0x00007fbe51068200]
CCUIImmediateTouchScrollView * [0x00007fbe530b7800]
UICollectionView * [0x00007fbe5203f600]
UITableView * [0x00007fbe509c6200]
CCUIImmediateTouchScrollView * [0x00007fbe53060800]
UITableViewWrapperView * [0x00007fbe50891a00]
SBIconScrollView * [0x00007fbe52807000]
SBPagedScrollView * [0x00007fbe51052600]
UITableViewWrapperView * [0x00007fbe51021000]
(lldb)

(lldb) search UIScrollView -b -m SpringBoard
SBIconScrollView * [0x00007fbe52807000]
SBSearchScrollView * [0x00007fbe51888a00]
SBPagedScrollView * [0x00007fbe51052600]

```

You need to go to the class that implements the method.

Swizzling the method scrollViewDidScroll

```
(lldb) po [SBFolderView class]
SBFolderView

(lldb) sclass SBFolderView -m scrollViewDidScroll:
```

Loading our modified class or swizzling method to the springboard process

(lldb) mload /Users/jchangho/Library/Developer/Xcode/DerivedData/Swizzle_SpringBoard-gwhgjbksgqavrwepbdqowljjkqrz/Build/Products/Debug-iphonesimulator/Swizzler.framework/Swizzler
Success

## Why Study Assembly?
* Explore apps you didn't write
* See how Swift works with Objective-C

Create NSString in heap

(lldb) p/x @"yay, debugging"
(__NSCFString *) $0 = 0x00006180000325c0 @"yay, debugging"
(lldb)

## Python

- Indentation counts
- Methods end with colon :
- no type declarations, just declare it
- Included in Xcode (python 2.7)
- LLDB has a python module used for making custom Commands

## LLDB Module Global Variables
```lldb.process``` The process
```lldb.debugger```
```lldb.target``` Content on disk and in memory
```lldb.thread``` Currently selected thread
```lldb.frame```

```script lldb.process```
```script print lldb.process``` provide more description


Gives you a load address

(lldb) lookup UIViewController.viewDidLoad -l
****************************************************
1 hits in: UIKit
****************************************************
-[UIViewController viewDidLoad], load_addr=[0x10f45d654-0x10f45d65a]

(lldb) script lldb.target.ResolveLoadAddress(0x10f45d654)
<lldb.SBAddress; proxy of <Swig Object of type 'lldb::SBAddress *' at 0x1052c6390> >
(lldb) script print lldb.target.ResolveLoadAddress(0x10f45d654)
UIKit`-[UIViewController viewDidLoad]
(lldb)

```
(lldb) script print lldb.target.modules
[<lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfb900> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfb8d0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfb930> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfb8a0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfb990> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfb9f0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfba20> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfba50> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfba80> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbab0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbae0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbb10> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbb40> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbb70> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbba0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbbd0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbc00> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbc30> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbc60> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbc90> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbcc0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbcf0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbd20> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbd50> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbd80> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbdb0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbde0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbe10> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbe40> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbe70> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbea0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbed0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbf00> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbf30> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbf60> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbf90> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105bfbfc0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16030> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16060> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16090> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c160c0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c160f0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16120> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16150> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16180> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c161b0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c161e0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16210> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16240> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16270> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c162a0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c162d0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16300> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16330> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16360> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16390> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c163c0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c163f0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16420> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16450> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16480> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c164b0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c164e0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16510> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16540> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16570> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c165a0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c165d0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16600> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16630> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16660> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16690> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c166c0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c166f0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16720> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16750> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16780> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c167b0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c167e0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16810> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16840> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16870> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c168a0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c168d0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16900> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16930> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16960> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16990> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c169c0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c169f0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16a20> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16a50> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16a80> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16ab0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16ae0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16b10> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16b40> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16b70> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16ba0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16bd0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16c00> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16c30> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16c60> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16c90> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16cc0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16cf0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16d20> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16d50> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16d80> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16db0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16de0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16e10> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16e40> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16e70> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16ea0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16ed0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16f00> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16f30> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16f60> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16f90> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c16fc0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f030> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f060> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f090> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f0c0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f0f0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f120> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f150> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f180> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f1b0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f1e0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f210> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f240> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f270> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f2a0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f2d0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f300> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f330> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f360> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f390> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f3c0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f3f0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f420> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f450> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f480> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f4b0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f4e0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f510> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f540> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f570> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f5a0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f5d0> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f600> >, <lldb.SBModule; proxy of <Swig Object of type 'lldb::SBModule *' at 0x105c1f630> >]
(lldb) script print lldb.target.module['UIKit']
(x86_64) /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator.sdk//System/Library/Frameworks/UIKit.framework/UIKit
(lldb)
```
