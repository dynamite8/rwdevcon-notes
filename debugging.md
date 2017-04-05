## Synopsis

Debugging tips

##

### Apple's Debugger, LLDB

- Replacement for GDB
- More than p & po
- Create custom commands

### 2 Most Important Commands

```help [command name]```

```apropos [keyword]```

- search through all documentation that contains the keyword that follows apropos

```b task_for_pid```

- breakpoint for function name task_for_pid

Example for Objective C, instance method lookup
-[class_name method_name]
Module

```image lookup -rn ^\-[UIViewController.set```
- Looks up all the setters for UIViewController

Regular expression automatically matches wildcards to both ends of query ```viewDid == .*viewDid.*```

Beware of spaces, both options will work:
```UIView\ set"``` == ```UIView\s\set```

Searches for function name that you're unsure of the case ```(?i)debug```

### Xcode shortcuts

```cmd+shift+y``` Expands/collapse code editor

```cmd+k``` clears debug output

```cmd+1``` File list

### Demo 1
```image lookup -n objectiveCProperty```

```
(lldb) image lookup -rn (?i)objectiveCProperty
4 matches found in /Users/jchangho/Library/Developer/Xcode/DerivedData/URL_Watcher-fnplhqzpgbekajgcctxpsfdrsyro/Build/Products/Debug-iphonesimulator/URL_Watcher.app/URL_Watcher:
        Address: URL_Watcher[0x0000000100001620] (URL_Watcher.__TEXT.__text + 256)
        Summary: URL_Watcher`-[ObjectiveCClass objectiveCProperty] at ObjectiveCClass.h:27        Address: URL_Watcher[0x0000000100001620] (URL_Watcher.__TEXT.__text + 256)
        Summary: URL_Watcher`-[ObjectiveCClass objectiveCProperty] at ObjectiveCClass.h:27        Address: URL_Watcher[0x0000000100001640] (URL_Watcher.__TEXT.__text + 288)
        Summary: URL_Watcher`-[ObjectiveCClass setObjectiveCProperty:] at ObjectiveCClass.h:27        Address: URL_Watcher[0x0000000100001640] (URL_Watcher.__TEXT.__text + 288)
        Summary: URL_Watcher`-[ObjectiveCClass setObjectiveCProperty:] at ObjectiveCClass.h:27
(lldb)
```


```
(lldb) image lookup -rn aInstanceMethod
10 matches found in /Users/jchangho/Library/Developer/Xcode/DerivedData/URL_Watcher-fnplhqzpgbekajgcctxpsfdrsyro/Build/Products/Debug-iphonesimulator/URL_Watcher.app/URL_Watcher:
        Address: URL_Watcher[0x0000000100001550] (URL_Watcher.__TEXT.__text + 48)
        Summary: URL_Watcher`-[ObjectiveCClass aInstanceMethod] at ObjectiveCClass.m:28        Address: URL_Watcher[0x0000000100001550] (URL_Watcher.__TEXT.__text + 48)
        Summary: URL_Watcher`-[ObjectiveCClass aInstanceMethod] at ObjectiveCClass.m:28        Address: URL_Watcher[0x00000001000033f0] (URL_Watcher.__TEXT.__text + 7888)
        Summary: URL_Watcher`@objc URL_Watcher.SwiftClass.aInstanceMethod () -> Swift.String at SwiftClass.swift        Address: URL_Watcher[0x00000001000033c0] (URL_Watcher.__TEXT.__text + 7840)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.aInstanceMethod () -> Swift.String at SwiftClass.swift:29        Address: URL_Watcher[0x0000000100003750] (URL_Watcher.__TEXT.__text + 8752)
        Summary: URL_Watcher`@objc URL_Watcher.SwiftClass.aInstanceMethod (with : Swift.String, in : __ObjC.UIView) -> () at SwiftClass.swift        Address: URL_Watcher[0x00000001000034e0] (URL_Watcher.__TEXT.__text + 8128)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.aInstanceMethod (with : Swift.String, in : __ObjC.UIView) -> () at SwiftClass.swift:37        Address: URL_Watcher[0x00000001000033c0] (URL_Watcher.__TEXT.__text + 7840)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.aInstanceMethod () -> Swift.String at SwiftClass.swift:29        Address: URL_Watcher[0x00000001000033f0] (URL_Watcher.__TEXT.__text + 7888)
        Summary: URL_Watcher`@objc URL_Watcher.SwiftClass.aInstanceMethod () -> Swift.String at SwiftClass.swift        Address: URL_Watcher[0x00000001000034e0] (URL_Watcher.__TEXT.__text + 8128)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.aInstanceMethod (with : Swift.String, in : __ObjC.UIView) -> () at SwiftClass.swift:37        Address: URL_Watcher[0x0000000100003750] (URL_Watcher.__TEXT.__text + 8752)
        Summary: URL_Watcher`@objc URL_Watcher.SwiftClass.aInstanceMethod (with : Swift.String, in : __ObjC.UIView) -> () at SwiftClass.swift

```

```
    (lldb) image look -rn \saInstanceMethod
1 match found in /Users/jchangho/Library/Developer/Xcode/DerivedData/URL_Watcher-fnplhqzpgbekajgcctxpsfdrsyro/Build/Products/Debug-iphonesimulator/URL_Watcher.app/URL_Watcher:
        Address: URL_Watcher[0x0000000100001550] (URL_Watcher.__TEXT.__text + 48)
        Summary: URL_Watcher`-[ObjectiveCClass aInstanceMethod] at ObjectiveCClass.m:28
```

Search for methods in class that has at least one or more words with at least one parameter

```
(lldb) image lookup -rn ObjectiveCClass.\w+\:
2 matches found in /Users/jchangho/Library/Developer/Xcode/DerivedData/URL_Watcher-fnplhqzpgbekajgcctxpsfdrsyro/Build/Products/Debug-iphonesimulator/URL_Watcher.app/URL_Watcher:
        Address: URL_Watcher[0x0000000100001590] (URL_Watcher.__TEXT.__text + 112)
        Summary: URL_Watcher`-[ObjectiveCClass instanceMethodWithInput:inView:] at ObjectiveCClass.m:36        Address: URL_Watcher[0x0000000100001640] (URL_Watcher.__TEXT.__text + 288)
        Summary: URL_Watcher`-[ObjectiveCClass setObjectiveCProperty:] at ObjectiveCClass.h:27
```

Find properties getter and setters
```
(lldb) image lookup -rn (?i)swiftProperty
8 matches found in /Users/jchangho/Library/Developer/Xcode/DerivedData/URL_Watcher-fnplhqzpgbekajgcctxpsfdrsyro/Build/Products/Debug-iphonesimulator/URL_Watcher.app/URL_Watcher:
        Address: URL_Watcher[0x00000001000032f0] (URL_Watcher.__TEXT.__text + 7632)
        Summary: URL_Watcher`@objc URL_Watcher.SwiftClass.swiftProperty.setter : Swift.String at SwiftClass.swift        Address: URL_Watcher[0x00000001000032a0] (URL_Watcher.__TEXT.__text + 7552)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.swiftProperty.getter : Swift.String at SwiftClass.swift:27        Address: URL_Watcher[0x0000000100003870] (URL_Watcher.__TEXT.__text + 9040)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.swiftProperty.materializeForSet : Swift.String at SwiftClass.swift        Address: URL_Watcher[0x00000001000032a0] (URL_Watcher.__TEXT.__text + 7552)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.swiftProperty.getter : Swift.String at SwiftClass.swift:27        Address: URL_Watcher[0x0000000100003350] (URL_Watcher.__TEXT.__text + 7728)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.swiftProperty.setter : Swift.String at SwiftClass.swift:27        Address: URL_Watcher[0x0000000100003230] (URL_Watcher.__TEXT.__text + 7440)
        Summary: URL_Watcher`@objc URL_Watcher.SwiftClass.swiftProperty.getter : Swift.String at SwiftClass.swift        Address: URL_Watcher[0x0000000100003870] (URL_Watcher.__TEXT.__text + 9040)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.swiftProperty.materializeForSet : Swift.String at SwiftClass.swift        Address: URL_Watcher[0x0000000100003350] (URL_Watcher.__TEXT.__text + 7728)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.swiftProperty.setter : Swift.String at SwiftClass.swift:27
```

```
(lldb) image lookup -rn URL.*aInstanceMethod
4 matches found in /Users/jchangho/Library/Developer/Xcode/DerivedData/URL_Watcher-fnplhqzpgbekajgcctxpsfdrsyro/Build/Products/Debug-iphonesimulator/URL_Watcher.app/URL_Watcher:
        Address: URL_Watcher[0x00000001000033f0] (URL_Watcher.__TEXT.__text + 7888)
        Summary: URL_Watcher`@objc URL_Watcher.SwiftClass.aInstanceMethod () -> Swift.String at SwiftClass.swift        Address: URL_Watcher[0x00000001000033c0] (URL_Watcher.__TEXT.__text + 7840)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.aInstanceMethod () -> Swift.String at SwiftClass.swift:29        Address: URL_Watcher[0x0000000100003750] (URL_Watcher.__TEXT.__text + 8752)
        Summary: URL_Watcher`@objc URL_Watcher.SwiftClass.aInstanceMethod (with : Swift.String, in : __ObjC.UIView) -> () at SwiftClass.swift        Address: URL_Watcher[0x00000001000034e0] (URL_Watcher.__TEXT.__text + 8128)
        Summary: URL_Watcher`URL_Watcher.SwiftClass.aInstanceMethod (with : Swift.String, in : __ObjC.UIView) -> () at SwiftClass.swift:37
```

### Creating breakpoints with name of method/properties

```URL_Watcher.SwiftClass.aInstanceMethod (with : Swift.String, in : __ObjC.UIView) -> ()```

Breakpoint commands
```br list```
```br list -b```


Every method in UIKit
```(lldb) image look -rn . UIKit```

### Class singleton lookup

Class method with everything up to shared, including spaces
```(lldb) image lookup -rn \+\[.*\sshared UIKit```

Filter further the search up to the ]
```(lldb) image lookup -rn \+\[.*\sshared.*\]$ UIKit```

Search inside UIKit
```(lldb) image lookup -rn (?i)nsobject.*description UIKit```

Lists all private and public methods

Prints all the methods in an instance
```po [[_UIRemoteKeyboards sharedRemoteKeyboards] _shortMethodDescription]```

All the private properties
```(lldb) po [[_UIRemoteKeyboards sharedRemoteKeyboards] _ivarDescription]```
