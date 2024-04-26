# Cordova plugin for getting DAck Base infor (game user Id)

[![NPM version][npm-version]][npm-url] [![NPM downloads][npm-downloads]][npm-url] [![NPM total downloads][npm-total-downloads]][npm-url] [![PayPal donate](https://img.shields.io/badge/paypal-donate-ff69b4?logo=paypal)][donate-url] [![Twitter][twitter-follow]][twitter-url]

## Index

<!-- MarkdownTOC levels="2" autolink="true" -->

- [Supported Platforms](#supported-platforms)
- [Installation](#installation)
- [API](#api)
- [Example](#example)

<!-- /MarkdownTOC -->

## Supported Platforms

- iOS
- Android

## Installation

    $ cordova plugin add cordova-plugin-dackbase

#Use variable `ANDROID_PLAY_ADID_VERSION` to override dependency version on Android:

#    $ cordova plugin add cordova-plugin-dackbase --variable ANDROID_PLAY_ADID_VERSION='16.+'

## API

The API is available on the `cordova.plugins.dackbase` global object.

### getUserId()

Returns a `Promise<object>` with the following fields:

- `status`: `int` - Whether we have received the game user id locally or we should connect the username password
#- `trackingLimited`: `boolean` - Whether usage of advertising id is allowed by user.
#- `idfa`: `string` (_iOS only_) - Identifier for advertisers.
#- `trackingPermission` (_iOS 14+ only_): [`number`](#tracking-permission-values)
#   Tracking permission status, available on iOS 14+ devices.
#- `aaid`: `string` (_Android only_) - Android advertising ID.

### Tracking Permission Values

The tracking permission values are `number`s returned by [`getInfo()`](#getinfo)
and [`requestPermission()`](#requestPermission). The possible values are stored in constants on the
plugin object. See the [example](#example) on how to use them.

For the meaning of the values see [the tracking transparency API docs](https://developer.apple.com/documentation/apptrackingtransparency/attrackingmanagerauthorizationstatus):

| Constant                           | Value | Description                                                                                               |
| :--------------------------------- | :---- | :-------------------------------------------------------------------------------------------------------- |
| TRACKING_PERMISSION_NOT_DETERMINED | 0     | User has not yet received an authorization request to authorize access to IDFA |
| TRACKING_PERMISSION_RESTRICTED     | 1     | User restricted the value returned if authorization to access IDFA |
| TRACKING_PERMISSION_DENIED         | 2     | The value returned if the user denies authorization to access IDFA |
| TRACKING_PERMISSION_AUTHORIZED     | 3     | The value returned if the user authorizes access to IDFA |

## Example

```js
const idfaPlugin = cordova.plugins.idfa;

idfaPlugin.getInfo()
    .then(info => {
        if (!info.trackingLimited) {
            return info.idfa || info.aaid;
        } else if (info.trackingPermission === idfaPlugin.TRACKING_PERMISSION_NOT_DETERMINED) {
            return idfaPlugin.requestPermission().then(result => {
                if (result === idfaPlugin.TRACKING_PERMISSION_AUTHORIZED) {
                    return idfaPlugin.getInfo().then(info => {
                        return info.idfa || info.aaid;
                    });
                }
            });
        }
    })
    .then(idfaOrAaid => {
        if (idfaOrAaid) {
            console.log(idfaOrAaid);
        }
    });
```

[npm-url]: https://www.npmjs.com/package/cordova-plugin-idfa
[npm-version]: https://img.shields.io/npm/v/cordova-plugin-idfa.svg
[npm-downloads]: https://img.shields.io/npm/dm/cordova-plugin-idfa.svg
[npm-total-downloads]: https://img.shields.io/npm/dt/cordova-plugin-idfa.svg?label=total+downloads
[twitter-url]: https://twitter.com/chemerisuk
[twitter-follow]: https://img.shields.io/twitter/follow/chemerisuk.svg?style=social&label=Follow%20me
[donate-url]: https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=E62XVSR3XUGDE&source=url
