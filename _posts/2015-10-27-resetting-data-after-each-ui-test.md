---
layout: post
title: Resetting the application data after each test with Xcode 7 UI Testing
permalink: /resetting-application-data-after-each-test-with-xcode7-ui-testing
tags: ios, swift, xcode, testing, ui-testing
categories: ios
author_id: tomaslinhart
---

Apple introduced in Xcode 7 UI testing that allows developers to easily test their user interface, but if you try it yourself you will soon discover that tests are not executed in isolation. The application starts where it was left off and it can be very problematic if your application has a state that you modify through your tests. Eg. some kind of on-boarding, creating records and so on.

If you have experience with [Appium](http://appium.io/) or other test automation frameworks, you know that before each test is executed all content and settings are cleared. So the first thing you could try is executing `NSTask` with some custom shell command and remove the content and settings, but unfortunately `NSTask` is not available on iOS.

If you look at `XCUIApplication`, you will not discover any way to reset the content and settings.

```swift
public class XCUIApplication : XCUIElement {
    public func launch()
    public func terminate()
    public var launchArguments: [String]
    public var launchEnvironment: [String : String]
}
```

But as you can see, you can set `launchArguments` which allows you to set custom arguments that can be read when the application starts and based on that, the application can remove its content and settings. So we can easily pass an argument to the `setUp()` method.

```swift
let application = XCUIApplication()
application.launchArguments = ["--ResetData"]
application.launch()
```

After that we need to add the following lines before `UIApplicationMain` is executed to reset the data. Unfortunately, there is no `main.swift` on iOS where you could add this code and you have only an annotation on AppDelegate with `@UIApplicationMain`. Fortunately, you can remove this annotation and create a file `main.swift` where you place code that you would have in your Objective-C project.

```swift
autoreleasepool {
    UIApplicationMain(Process.argc, Process.unsafeArgv, nil, NSStringFromClass(AppDelegate))
}
```

And before that you can easily put the following lines for resetting the data that you will want to enable only in the debug mode. To enable the DEBUG flag, you need to add to your project settings a value under `Other Swift Flags`. You put `-D DEBUG` for Debug.

```swift
#if DEBUG
autoreleasepool {
    if Process.arguments.contains("--ResetData") {
        ApplicationResetManager.reset()
    }
}
#endif
```

The last part of this puzzle is to write the application reset logic. Basically, your application can be saving files to folders that are dedicated to it. It can also be saving files to shared folders through app groups and it could be also saving data to user defaults and keychain. So the application reset logic needs to include all of this. Here you can find code that we use in our application to reset all data we have.

```swift
import UIKit

class ApplicationResetManager: NSObject {

    /// Resets all saved data in the application.
    class func reset() {
        clearFiles()
        clearUserDefaults()
        clearKeychain()
        clearApplicationProperties()
    }

    // MARK: - Private

    private class func clearFiles() {
        let library = NSSearchPathForDirectoriesInDomains(.LibraryDirectory, .UserDomainMask, true)
        let caches = NSSearchPathForDirectoriesInDomains(.CachesDirectory, .UserDomainMask, true)
        let documents = NSSearchPathForDirectoriesInDomains(.DocumentDirectory, .UserDomainMask, true)
        // Remove this out, if you don't use app groups.
        let groupContainer = filesForGroupIdentifier("com.example.yourapp.group")

        let fileManager = NSFileManager()
        for path in library + caches + documents + groupContainer {
            _ = try? fileManager.removeItemAtPath(path)
        }
    }

    private class func filesForGroupIdentifier(groupIdentifier: String) -> [String] {
        let fileManager = NSFileManager.defaultManager()
        guard let path = fileManager.containerURLForSecurityApplicationGroupIdentifier(groupIdentifier)?.path else { return [] }
        guard let files = try? fileManager.contentsOfDirectoryAtPath(path) else { return [] }
        return files.map { "\(path)/\($0)" }
    }

    private class func clearUserDefaults() {
        // Remove this out, if you don't use app groups.
        let groupUserDefaults = NSUserDefaults(suiteName: "com.example.yourapp.group")
        groupUserDefaults?.clear()
        groupUserDefaults?.synchronize()

        let standardUserDefaults = NSUserDefaults.standardUserDefaults()
        standardUserDefaults.clear()
        standardUserDefaults.synchronize()
    }

    private class func clearKeychain() {
        let secClasses = [
            kSecClassGenericPassword as String,
            kSecClassInternetPassword as String,
            kSecClassCertificate as String,
            kSecClassKey as String,
            kSecClassIdentity as String
        ]

        for secClass in secClasses {
            let query = [kSecClass as String: secClass]
            SecItemDelete(query as CFDictionaryRef)
        }
    }

    private class func clearApplicationProperties() {
        let application = UIApplication.sharedApplication()
        if #available(iOS 9.0, *) {
            application.shortcutItems = nil
        }
        application.applicationIconBadgeNumber = 0
    }
}

// MARK: - NSUserDefaults+Clear
extension NSUserDefaults {
    private func clear() {
        for key in dictionaryRepresentation().keys {
            removeObjectForKey(key)
        }
    }
}
```

This code clears user defaults, all files, keychain and properties on `UIApplication`. Although the last one, you might not need for testing. You might also want to clear iCloud, CloudKit and other places where you store your data but you should have an idea how to do it.

You can find a fully working project under [our GitHub account](https://github.com/Onefootball/TestingWithResettingData).

Happy testing!
