---
title: Devices in Containers on Windows
description: What device support exists for containers on Windows
keywords: docker, containers, devices, hardware
author: cwilhit
---

**This is currently preview material. See the 4th item in the 'Requirements' section below for more information.**

# Devices in Containers on Windows

By default, Windows containers are given minimal access to host devices--just like Linux containers. There are certain workloads where it is beneficial--or even imperative--to access and communicate with host hardware devices. This guide covers which devices are supported in containers and how to get started.

## Requirements

- You must be running Windows Server 2019 or later or Windows 10 Pro/Enterprise with the October 2018 Update
- Your container image version must be 1809 or later.
- Your containers must be Windows containers running in process-isolated mode.
- While the Windows devices functionality exists in the Docker daemon, it does not yet exist in the Docker client (see this [pull request](https://github.com/docker/cli/pull/1606) to track). You must wait for a future release of Docker for Windows / Docker EE with this code to take advantage of this feature. This document will be updated when the status changes.

## Run a Container with a Device

To start a container with a device, use the following command:

```shell
docker run --isolation=process --device="class/{interface class GUID}" mcr.microsoft.com/windows/servercore:1809
```

You must replace the `{interface class guid}` with an appropriate [device interface class GUID](https://docs.microsoft.com/en-us/windows-hardware/drivers/install/overview-of-device-interface-classes), which can be found in the section below.

To start a container with multiple devices, use the following command and string together multiple `--device` arguments:

```shell
docker run --isolation=process --device="class/{interface class GUID}" --device="class/{interface class GUID}" mcr.microsoft.com/windows/servercore:1809
```

In Windows, all devices declare a list of interface classes that they implement. By passing this command to Docker, it will ensure that all devices which identify as implementing the requested class will be plumbed into the container.

This means you are **not** assigning the device away from host. Instead, the host is sharing it with the container. Likewise, because you are specifying a class GUID, _all_ devices that implement that GUID will be shared with the container.

## What Devices are Supported

The following devices (and their device interface class GUIDs) are supported today:
  
<table border="1" style="background-color:FFFFCC;border-collapse:collapse;border:1px solid FFCC00;color:000000;width:75%" cellpadding="5" cellspacing="5">
<thead>
<tr valign="top">
<th><center>Device Type</center></th>
<th><center>Interface Class GUID</center></th>
</tr>
</thead>
<tbody>
<tr valign="top">
<td><center>GPIO</center></td>
<td><center>916EF1CB-8426-468D-A6F7-9AE8076881B3</center></td>
</tr>
<tr valign="top">
<td><center>I2C Bus</center></td>
<td><center>A11EE3C6-8421-4202-A3E7-B91FF90188E4</center></td>
</tr>
<tr valign="top">
<td><center>COM Port</center></td>
<td><center>86E0D1E0-8089-11D0-9CE4-08003E301F73</center></td>
</tr>
<tr valign="top">
<td><center>SPI Bus</center></td>
<td><center>DCDE6AF9-6610-4285-828F-CAAF78C424CC</center></td>
</tr>
</tbody>
</table>

> [!TIP]
> The devices listed above are the _only_ devices supported in Windows containers today. Attempting to pass any other class GUIDs will result in the container failing to start.

## Hyper-V Container Device Support

Device assignment and device sharing are not supported in Hyper-V isolated containers today.

## Linux Containers on Windows (LCOW) Device Support

Device assignment and device sharing are not supported in LCOW today.
