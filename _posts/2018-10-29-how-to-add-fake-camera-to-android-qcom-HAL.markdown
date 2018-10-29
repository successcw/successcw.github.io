---
layout: post
title: How to add fake camera to android qcom HAL
date: 2018-10-29 08:00:00 +0800
description: # Add post description (optional)
img: # Add image post (optional)
tags: [Android, Camera, Qcom, HAL]# add tag
---

## 背景
由于项目要求，需要给android设备添加一个虚拟摄像头，preview的数据是从远程通过h264传过来


## 在上层入口处做欺骗
hardware/qcom/camera/QCamera2/QCamera2Factory.cpp

```
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
```
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

set_preview流程
![framework]({{site.baseurl}}/assets/img/preview-process.png)

## Debuging skill
* MediaCodec(to be add)
* Sepolicy(to be add)
* Debug script(to be add)
