---
id: APIRef.DeviceObjectAPI
title: The `device` Object
---

`device` is globally available in every test file, it enables control over the current attached device (currently only simulators are supported).

### Methods

- [`device.launchApp()`](#devicelaunchappparams)
- [`device.relaunchApp()` **Deprecated**](#devicerelaunchappparams)
- [`device.terminateApp()`](#deviceterminateapp)
- [`device.sendToHome()`](#devicesendtohome)
- [`device.reloadReactNative()`](#devicereloadreactnative)
- [`device.installApp()`](#deviceinstallapp)
- [`device.uninstallApp()`](#deviceuninstallapp)
- [`device.openURL(url)`](#deviceopenurlurl-sourceappoptional)
- [`device.sendUserNotification(params)`](#devicesendusernotificationparams)
- [`device.sendUserActivity(params)`](#devicesenduseractivityparams)
- [`device.setOrientation(orientation)`](#devicesetorientationorientation)
- [`device.setLocation(lat, lon)`](#devicesetlocationlat-lon)
- [`device.setURLBlacklist([urls])`](#deviceseturlblacklisturls)
- [`device.enableSynchronization()`](#deviceenablesynchronization)
- [`device.disableSynchronization()`](#devicedisablesynchronization)
- [`device.resetContentAndSettings()`](#deviceresetcontentandsettings)
- [`device.getPlatform()`](#devicegetplatform)
- [`device.takeScreenshot(name)`](#devicetakescreenshotname)
- [`device.pressBack()` **Android Only**](#devicepressback-android-only)
- [`device.shake()` **iOS Only**](#deviceshake-ios-only)

### `device.launchApp(params)`
Launch the app defined in the current [`configuration`](APIRef.Configuration.md).

**Options:** 

##### 1. Restart the app
Terminate the app and launch it again. 
If set to `false`, the simulator will try to bring app from background, if the app isn't running, it will launch a new instance. default is `false`

```js
await device.launchApp({newInstance: true});
```

##### 2. Set runtime permissions
Grant or deny runtime permissions for your application. 

```js
await device.launchApp({permissions: {calendar: 'YES'}});
```
Detox uses [AppleSimUtils](https://github.com/wix/AppleSimulatorUtils) on iOS to support this functionality. Read about the different types of permissions and how to set them in AppleSimUtils' Readme.
Check out Detox's [own test suite](../detox/test/e2e/13.permissions.test.js)

##### 3. Launch from URL
Mock opening the app from URL to test your app's deep link handling mechanism.

```js
await device.launchApp({url: url});
```
###### Mock opening from a URL when app is not running
```js
await device.launchApp({url: url, newInstance: true});
```
This will launch a new instance of the app and handle the deep link.

######  Mock opening from a URL when app is in background

```js
await device.launchApp({url: url, newInstance: false});
```
This will launch the app from background and handle the deep link.

Read more in [Mocking Open From URL](APIRef.MockingOpenFromURL.md) section.

##### 4. Launch with user notifications

```js
await device.launchApp({userNotification: notification});
```

###### Mock receiving a notifications when app is not running
```js
await device.launchApp({userNotification: notification, newInstance: true});
```
This will launch a new instance of the app and handle the notification.

######  Mock receiving a notifications when app is in background

```js
await device.launchApp({userNotification: notification, newInstance: false});
```
This will launch the app from background and handle the notification.

Read more in [Mocking User Notifications](APIRef.MockingUserNotifications.md) section.

##### 5. Launch with user activity

```js
await device.launchApp({userActivity: activity});
```

###### Mock receiving an activity when app is not running
```js
await device.launchApp({userActivity: activity, newInstance: true});
```
This will launch a new instance of the app and handle the activity.

######  Mock receiving an activity when app is in background

```js
await device.launchApp({userActivity: activity, newInstance: false});
```
This will launch the app from background and handle the activity.

Read more in [Mocking User Activity](APIRef.MockingUserActivity.md) section.

##### 6. Launch into a fresh installation 
A flag that enables relaunching into a fresh installation of the app (it will uninstall and install the binary again), default is `false`.

```js
await device.launchApp({delete: true});
```

##### 7. Additional launch arguments
Detox can start the app with additional launch arguments

```js
await device.launchApp({launchArgs: {arg1: 1, arg2: "2"}});
```

The added `launchArgs` will be passed through the launch command to the device and be accessible via `[[NSProcessInfo processInfo] arguments]`

##### 8. Disable touch indicators (iOS only)
Disable touch indicators on iOS.

```js
await device.launchApp({disableTouchIndicators: true});
```

##### 9. Launch with a specific language (iOS only)
Launch the app with a specific system language

Information about accepted values can be found [here](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/LanguageandLocaleIDs/LanguageandLocaleIDs.html).

```js
await device.launchApp({
  languageAndLocale: {
    language: "es-MX",
    locale: "es-MX"
  }
});
```

With this API, you can run sets of e2e tests per language. For example:
```js
['es-MX', 'fr-FR', 'pt-BR'].forEach(locale => {
  describe(`Test suite in ${locale}`, () => {

    beforeAll(async () => {
      await device.launchApp({
        newInstance: true,
        languageAndLocale: {
          language: locale,
          locale
        }
      });
    });


    it('Test A', () => {
      
    })

    it('Test B', () => {
      
    })

  });
});
```

##### 10. Initialize the URL blacklist at device launch
Launch the app with an URL blacklist to disable network synchronization on certain endpoints. Useful if the app makes frequent network calls to blacklisted endpoints upon startup. 

```js
await device.launchApp({
  newInstance: true,
  launchArgs: { detoxURLBlacklistRegex: ' \\("http://192.168.1.253:19001/onchange","https://e.crashlytics.com/spi/v2/events"\\)' },
}); 
```

### `device.relaunchApp(params)`
**Deprecated** Use `device.launchApp(params)` instead. This method is now calling `launchApp({newInstance: true})` for backwards compatibility.<Br>
Kill and relaunch the app defined in the current [`configuration`](APIRef.Configuration.md).

### `device.terminateApp()`
By default, `terminateApp()` with no params will terminate the app file defined in the current [`configuration`](APIRef.Configuration.md).

To terminate another app, specify its bundle id

```js
await device.terminateApp('other.bundle.id');
```

### `device.sendToHome()`
Send application to background by bringing `com.apple.springboard` to the foreground.<br>
Combining `sendToHome()` with `launchApp({newInstance: false})` will simulate app coming back from background.<br>
Check out Detox's [own test suite](../detox/test/e2e/06.device.test.js)

```js
await device.sendToHome();
await device.launchApp({newInstance: false});
// app returned from background, do stuff
```
Check out Detox's [own test suite](../detox/test/e2e/06.device.test.js)

### `device.reloadReactNative()`
If this is a React Native app, reload the React Native JS bundle. This action is much faster than `device.launchApp()`, and can be used if you just need to reset your React Native logic.

<i>**Note:** This functionality does not work without faults. Under certain conditions, the system may not behave as expected and/or even crash. In these cases, use `device.launchApp()` to launch the app cleanly instead of only reloading the JS bundle.</i>

### `device.installApp()`
By default, `installApp()` with no params will install the app file defined in the current [`configuration`](APIRef.Configuration.md).

To install another app, specify its path

```js
await device.installApp('path/to/other/app');
```

### `device.uninstallApp()`
By default, `uninstallApp()` with no params will uninstall the app defined in the current [`configuration`](APIRef.Configuration.md).

To uninstall another app, specify its bundle id

```js
await device.installApp('other.bundle.id');
```

### `device.openURL({url, sourceApp[optional]})`
Mock opening the app from URL. `sourceApp` is an optional parameter to specify source application bundle id.<br>
Read more in [Mocking Open From URL](APIRef.MockingOpenFromURL.md) section.<br>
Check out Detox's [own test suite](../detox/test/e2e/15.urls.test.js)

### `device.sendUserNotification(params)`
Mock handling of received user notification when app is in foreground.<br>
Read more in [Mocking User Notifications](APIRef.MockingUserNotifications.md) section.<br>
Check out Detox's [own test suite](../detox/test/e2e/11.user-notifications.test.js)

### `device.sendUserActivity(params)`
Mock handling of received user activity when app is in foreground.<br>
Read more in [Mocking User Activity](APIRef.MockingUserActivity.md) section.<br>
Check out Detox's [own test suite](../detox/test/e2e/18.user-activities.test.js)

### `device.setOrientation(orientation)`
Takes `"portrait"` or `"landscape"` and rotates the device to the given orientation.
Currently only available in the iOS Simulator.<br>
Check out Detox's [own test suite](../detox/test/e2e/06.device-orientation.test.js)

### `device.setLocation(lat, lon)`
>Note: `setLocation` is dependent on `fbsimctl`. if `fbsimctl` is not installed, the command will fail, asking for it to be installed.
Sets the simulator location to the given latitude and longitude.
```js
await device.setLocation(32.0853, 34.7818);
```

### `device.setURLBlacklist([urls])`

Disable [EarlGrey's network synchronization mechanism](https://github.com/google/EarlGrey/blob/master/docs/api.md#network) on preferred endpoints. Useful if you want to on skip over synchronizing on certain URLs. To disable endpoints at initialization, pass in the blacklist at [device launch](#10-initialize-the-url-blacklist-at-device-launch).

```js
await device.setURLBlacklist(['.*127.0.0.1.*']);
```

```js
await device.setURLBlacklist(['.*my.ignored.endpoint.*']);
```

### `device.enableSynchronization()`
Enable [EarlGrey's synchronization mechanism](https://github.com/google/EarlGrey/blob/master/docs/api.md#synchronization
) (enabled by default). **This is being reset on every new instance of the app.**
```js
await device.enableSynchronization();
```


### `device.disableSynchronization()`
Disable [EarlGrey's synchronization mechanism](https://github.com/google/EarlGrey/blob/master/docs/api.md#synchronization
) (enabled by default) **This is being reset on every new instance of the app.**

```js
await device.disableSynchronization();
```


### `device.resetContentAndSettings()`
Resets the Simulator to clean state (like the Simulator > Reset Content and Settings... menu item), especially removing
previously set permissions.

```js
await device.resetContentAndSettings();
```

### `device.getPlatform()`
Returns the current device, `ios` or `android`.

```js
if (device.getPlatform() === 'ios') {
  await expect(loopSwitch).toHaveValue('1');
}
```

### `device.takeScreenshot(name)`
Takes a screenshot on the device and schedules putting it to
the [artifacts folder](APIRef.Artifacts.md#enabling-artifacts) upon
completion of the current test. Consider the example below:

```js
describe('Menu items', () => {
  it('should have Logout', async () => {
    // ...
    await device.takeScreenshot('tap on menu');
    // ...
  });
});
```

* If the test passes, the screenshot will be put to `<artifacts-location>/✓ Menu items should have Logout/tap on menu.png`.
* If the test fails, the screenshot will be put to `<artifacts-location>/✗ Menu items should have Logout/tap on menu.png`.

> NOTE: At the moment, taking screenshots on-demand in `--take-screenshots failing` mode is not yet implemented.

### `device.pressBack()` **Android Only**
Simulate press back button.

```js
await device.pressBack();
```

### `device.shake()` **iOS Only**
Simulate shake
