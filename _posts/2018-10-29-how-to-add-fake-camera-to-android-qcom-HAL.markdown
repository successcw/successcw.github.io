---
layout: post
title: How to add fake camera to android qcom HAL
date: 2018-10-29 08:00:00 +0800
description: # Add post description (optional)
img: # Add image post (optional)
categories: Android
tags: [Android]# add tag
---

## 背景
As project requirement, we need add a virutal camera, preview frame is read from usb h264 stream

## 在上层入口处做欺骗
hardware/qcom/camera/QCamera2/QCamera2Factory.cpp

```c++
QCamera2Factory::QCamera2Factory()
{
    ...
    mNumOfCameras++;
    mNumOfCameras_expose++;

    if (cameraId == VIRTUAL_CAM_ID) {
        mHalDescriptors[i].device_version = CAMERA_DEVICE_API_VERSION_1_0;
    }
    ...
}

int QCamera2Factory::getCameraInfo(int camera_id, struct camera_info *info)
{
    ...
    if (camera_id == VIRTUAL_CAM_ID) {
        rc = VCamera2HardwareInterface::getCamInfo(
                mHalDescriptors[camera_id].cameraId, info);
    }
    ...
}

int QCamera2Factory::cameraDeviceOpen(int camera_id,
                    struct hw_device_t **hw_device)
{
    ...
    if (camera_id == VIRTUAL_CAM_ID) {
       VCamera2HardwareInterface *hw = new VCamera2HardwareInterface((uint32_t)camera_id);
       if (!hw) {
           LOGE("Allocation of hardware interface failed");
           return NO_MEMORY;
       }
       rc = hw->openCamera(hw_device);
       if (rc != NO_ERROR) {
           delete hw;
       }
   }
   ...
}

```

## 实现VCamera2HardwareInterface
依照HAL1的设定去依次实现这些接口
```c++
camera_device_ops_t VCamera2HardwareInterface::mCameraOps = {
    .set_preview_window =        VCamera2HardwareInterface::set_preview_window,
    .set_callbacks =             VCamera2HardwareInterface::set_CallBacks,
    .enable_msg_type =           VCamera2HardwareInterface::enable_msg_type,
    .disable_msg_type =          VCamera2HardwareInterface::disable_msg_type,
    .msg_type_enabled =          VCamera2HardwareInterface::msg_type_enabled,

    .start_preview =             VCamera2HardwareInterface::start_preview,
    .stop_preview =              VCamera2HardwareInterface::stop_preview,
    .preview_enabled =           VCamera2HardwareInterface::preview_enabled,
    .store_meta_data_in_buffers= VCamera2HardwareInterface::store_meta_data_in_buffers,

    .start_recording =           VCamera2HardwareInterface::start_recording,
    .stop_recording =            VCamera2HardwareInterface::stop_recording,
    .recording_enabled =         VCamera2HardwareInterface::recording_enabled,
    .release_recording_frame =   VCamera2HardwareInterface::release_recording_frame,

    .auto_focus =                VCamera2HardwareInterface::auto_focus,
    .cancel_auto_focus =         VCamera2HardwareInterface::cancel_auto_focus,

    .take_picture =              VCamera2HardwareInterface::take_picture,
    .cancel_picture =            VCamera2HardwareInterface::cancel_picture,

    .set_parameters =            VCamera2HardwareInterface::set_parameters,
    .get_parameters =            VCamera2HardwareInterface::get_parameters,
    .put_parameters =            VCamera2HardwareInterface::put_parameters,
    .send_command =              VCamera2HardwareInterface::send_command,

    .release =                   VCamera2HardwareInterface::release,
    .dump =                      VCamera2HardwareInterface::dump,
};
```

start_preview流程
![framework]({{site.baseurl}}/assets/img/preview-process.png)

## Debuging point
* MediaCodec初始化失败
整个虚拟摄像头实现在hal层，从android 8.0开始，hal层使用vendor binder(/dev/vndbinder)通信
![framework]({{site.baseurl}}/assets/img/binder.png)
``` c
/hardware/interface/camera/provider/2.4/default/service.cpp
int main()
{
    ALOGI("Camera provider Service is starting.");
    // The camera HAL may communicate to other vendor components via
    // /dev/vndbinder
    android::ProcessState::initWithDriver("/dev/vndbinder");
    return defaultPassthroughServiceImplementation<ICameraProvider>("legacy/0", /*maxThreads*/ 6);
}
```
在hal层调用mediacodec去解码h264码流，需要访问到以下service
**media.metrics**
**media.resource_manager**
**media.player**
而这些service使用/dev/binder通信，所以会造成mediacodec初始化失败
解决方案：添加hack方法来规避
``` c
frameworks/native/libs/binder/IServiceManager.cpp
virtual sp<IBinder> getService(const String16& name) const
{
    unsigned n;
    //virtual camera: start
    if (!strcmp(ProcessState::self()->getDriverName().c_str(), "/dev/vndbinder") &&
            (!strcmp(String8(name).string(), "media.metrics") ||
             !strcmp(String8(name).string(), "media.resource_manager") ||
             !strcmp(String8(name).string(), "media.player"))) {
        ALOGI("access binder ServiceManager from vndbinder is not allowed, trick here^^");
        return NULL;
    }
    //virtual camera: end
    for (n = 0; n < 5; n++){
        if (n > 0) {
            if (!strcmp(ProcessState::self()->getDriverName().c_str(), "/dev/vndbinder")) {
                ALOGI("Waiting for vendor service %s...", String8(name).string());
                CallStack stack(LOG_TAG);
            } else {
                ALOGI("Waiting for service %s...", String8(name).string());
            }
            sleep(1);
        }
	}
}
```
* 微信preview无图像
普通camera preview，原理是应用层传surface到HAL层，由HAL层刷preview frame到这个surface
但微信等应用是拿到frame数据后自行来显示的，所以还需要在preview thread中做以下处理
``` c
if (CAMERA_MSG_PREVIEW_FRAME) {
    data_cb(msgType, previewMem, 0, metadata, user);
}
```
* Debug script
开启hal log
adb shell setprop persist.camera.hal.debug 6
adb shell setprop persist.camera.mci.debug 6
adb shell setprop persist.camera.global.debug 6

