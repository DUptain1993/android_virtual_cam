# android_virtual_cam

[简体中文](./README_zh.md) | [繁體中文](./README_tc.md) | [English](./README.md)

A virtual camera based on Xposed

## DO NOT USE FOR ANY ILLEGAL PURPOSE. YOU BEAR ALL RESPONSIBILITY AND CONSEQUENCES.

### China mainland mirror (Gitee): https://gitee.com/w2016561536/android_virtual_cam

## Supported platforms

- Android 5.0+ (build targets Android 16 / API 36)

## Usage

1. Install this module and enable it in Xposed. LSPosed and other frameworks that use a scope list require you to select the target app instead of the System Framework.

2. In system Settings, grant the target app permission to access local storage, then force-stop the target app. If the app doesn't request this permission on its own, see step 3.

3. Open the target app. If it doesn't have permission to access local storage, a toast message will tell you that the `Camera1` directory has been redirected to the app's private directory `/[INTERNAL_STORAGE]/Android/data/[package_name]/files/Camera1/`. If there is no such message, the default `Camera1` directory is `/[INTERNAL_STORAGE]/DCIM/Camera1/`. If the directory doesn't exist, create it manually.

> Note: `Camera1` under the private directory only takes effect for that single app.

4. Open the camera preview in the target app. A toast message will show the resolution (`Width: ..., Height: ...`). Prepare your replacement video at that same resolution, name it `virtual.mp4`, and place it in the `Camera1` directory. If opening the camera doesn't show a toast message, there's no need to adjust the video resolution.

5. If taking a photo in the target app still shows the real camera image, and a `Photo capture detected` toast with a resolution appears, prepare a photo at that resolution, name it `1000.bmp`, and place it in the `Camera1` directory (other formats are supported if renamed with a `.bmp` extension). If no toast message appears when taking a photo, `1000.bmp` has no effect.

6. If you want the replacement video's audio to play, create a `no-silent.jpg` file in `/[INTERNAL_STORAGE]/DCIM/Camera1/`. (Applies globally and immediately.)

7. If you want to temporarily disable video replacement, create a `disable.jpg` file in `/[INTERNAL_STORAGE]/DCIM/Camera1/`. (Applies globally and immediately.)

8. If the toast messages are annoying, create a `no_toast.jpg` file in `/[INTERNAL_STORAGE]/DCIM/Camera1/`. (Applies globally and immediately.)

9. The directory-redirection message is only shown once by default. If you missed it, create a `force_show.jpg` file in `/[INTERNAL_STORAGE]/DCIM/Camera1/` to override the default and show it again. (Applies globally and immediately.)

10. If you need a different video per app, create a `private_dir.jpg` file in `/[INTERNAL_STORAGE]/DCIM/Camera1/` to force every app to use its own private directory. (Applies globally and immediately.)

> Note: options 6~10 all have matching switches inside the app itself — you can toggle them there instead of creating the files manually.

> On Android 11+ (API 30+), the app requests the "All files access" (`MANAGE_EXTERNAL_STORAGE`) permission so it can manage these control files under scoped storage.

## FAQ

Q1. Front camera orientation issues?
A1. In most cases, the replacement video for the front camera needs to be flipped horizontally and rotated 90° clockwise, and the video's resolution **after processing** should match the resolution shown in the toast message. This isn't always required — judge based on your actual situation.

Q2. Black screen, camera fails to start?
A2. Some apps currently cannot be hooked successfully (especially the system camera app). This can also be caused by an incorrect `Camera1` path (check whether you accidentally created a nested `Camera1` directory, e.g. `./DCIM/Camera1/Camera1/virtual.mp4` — only one level is needed).

Q3. Garbled / corrupted picture?
A3. The video resolution is incorrect.

Q4. Distorted picture?
A4. Use video editing software to adjust the original video to match the screen.

Q5. Creating `disable.jpg` has no effect?
A5. If the app version is `<=4.0`, control files under `[INTERNAL_STORAGE]/DCIM/Camera1` only take effect for apps that **have storage access permission**; apps without that permission should place them in their **private directory** instead.
If the app version is `>=4.1`, control files should be created in `[INTERNAL_STORAGE]/DCIM/Camera1` regardless of whether the target app has permission.

## Reporting issues

Please report directly via issues. For bug reports, attach the Xposed **module** log.

## Credits

Hook approach: https://github.com/wangwei1237/CameraHook

H.264 hardware decoding: https://github.com/zhantong/Android-VideoToImages

JPEG to YUV conversion: https://blog.csdn.net/jacke121/article/details/73888732
