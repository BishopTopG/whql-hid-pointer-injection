# WHQL virtual HID packages that synthesize pointer input

## Summary

Three Microsoft-signed virtual HID packages accepted user-mode input and moved the console cursor on Windows 11. The motion matched a HID mouse report. Install required an administrator. After the virtual device was present, a process in the interactive session could submit the report.

This is local pointer spoofing. It is not a remote bug, not a kernel memory read or write, and not a privilege gain over the administrator install. A mouse-class filter (`UpperFilters` or `LowerFilters`) whose own control interface injected pointer motion was not confirmed in this pass. The packages below moved the cursor through the virtual HID device they install.

A loldrivers name list of 695 entries, checked on 2026-09-22, did not contain these names.

Report layouts, IOCTL codes, device paths, and proof-of-concept code are omitted. Hashes are included so the images can be blocklisted.

## Test host

Windows 11 AMD64, build 10.0.26200.7627. Secure Boot on. Test signing off. Cursor checks ran in the interactive console session.

## Affected images

### MUV Interactive vHID

| Field | Value |
| --- | --- |
| DriverVer | 03/21/2017, 12.35.27.507 |
| Role | Virtual HID function driver, plus a HID-KMDF mapper |
| Signature | Embedded Microsoft Windows Hardware Compatibility Publisher |
| `vHID.sys` | AMD64, 28144 bytes, SHA-256 `be94d804d649d9649f2cbd6e8c8a35c2d33bf24f9c185c36312ebeae02ddad29` |
| `hidkmdf.sys` | AMD64, 25072 bytes, SHA-256 `996b0310f5c0eaa55efff2f7c4600343fe6b16497b9e1d7af4bb63bdf336ecbf` |

A user-mode write to the virtual device moved the console cursor.

### EMRight EMRvhid

| Field | Value |
| --- | --- |
| DriverVer | 03/27/2016, 1.0.0.1 |
| Role | Virtual HID function driver. Not a mouse-class filter. |
| Signature | Embedded Microsoft Windows Hardware Compatibility Publisher |
| `EMRvhid.sys` | AMD64, 17168 bytes, SHA-256 `bb8414db4fd5a336401b112ebb32acd5d9e521528e0d022f671af1751afb26a4` |

A user-mode request to the virtual device moved the console cursor.

### Virtual HID Provider vhidflt

| Field | Value |
| --- | --- |
| DriverVer | 11/03/2021, 18.13.46.429 |
| Role | Lower filter on a virtual HID device. Pointer motion came from the virtual HID stack this package installs, not from the filter control interface. |
| Signature | Embedded Microsoft Windows Hardware Compatibility Publisher |
| `vhidflt.sys` | AMD64, 143592 bytes, SHA-256 `c8819dbd9a94046e1182695d412d73b2080e3b9342e765ad54bb5ef414de9f5c` |

## What this is not

Other signed mouse-filter packages were reviewed in the same lab pass. They are not listed. A negative list would be a search aid, and those packages did not show this behavior.

No claim is made for later catalog builds that do not match these hashes. No claim is made for every Windows build.

## Remediation

Microsoft can add the AMD64 hashes above to the vulnerable driver blocklist if signed virtual pointer injection is in scope for that list. File names collide. The hash is the blocklist key.

The package owner can remove the virtual control surface, or restrict the device DACL to the service that owns the device. A WHQL signature from 2017 still loads on this Windows 11 host with Secure Boot.

Enterprises can deny these hashes with WDAC or App Control where the product is not required.

## Scope

This note is a disclosure record for blocklist and access-control work. It does not include a reproduction.
