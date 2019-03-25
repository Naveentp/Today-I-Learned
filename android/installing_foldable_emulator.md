# Installing Foldable Emulator

> Samsung offers Foldable emulator app, which can be used to test how our apps respond in a foldable environment.

### Steps to install & run our apps in foldable emulator
---
#### Installing Emulator app in Devices
1. Download [Foldable Emulator apk](https://developer.samsung.com/common/download/check.do?actId=1253) offered by Samsung.

2. Install the app using command prompt like
    ```
    $ adb install FoldableEmulator.apk
    ```
3. Once the app installed, Open the "Foldable Emulator" app from apps list.
4. Run these commands to grant permissions
    ```
    -> ~ adb shell
    lux_uds:/ $ pm grant com.samsung.android.foldable.emulator android.permission.WRITE_SECURE_SETTINGS
    lux_uds:/ $ pm grant com.samsung.android.foldable.emulator android.permission.SYSTEM_ALERT_WINDOW
    ```
5. Once you app in your device, app shows fold/unfold actions to test your app. 

---
#### Installing Emulator app in AVD
1. Goto Android Studio -> Tools -> AVD Manager

2. Create virtual device -> Tablet -> Nexus 10

3. Once the AVD up and running, follow the same procedure as [Installing Emulator app in Devices](#Installing-Emulator-app-in-Devices)

[Read more here](https://developer.samsung.com/galaxy/foldable/test)