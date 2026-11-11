# WAVLINK FirstLogin authentication bypass

## Summary

The `/protocol.csp` login handler in WAVLINK firmware `M_V260622` returns a management token to an unauthenticated client when `winstar.system.FirstLogin` is `1`. This is the default setting in the examined firmware images and is relevant to factory-default or reset devices. The tested request supplied no password, `usrid`, or session Cookie. In isolated firmware emulation, the returned token passed `check_login` and authorized a management setting change. The token was bound to the requesting client's source IP address.

This issue requires access to the device's management interface while it remains in the first-login state. The behavior after first-time initialization was not demonstrated. Whether the vendor intends to allow any client reaching the management interface to claim the first administrative session remains subject to vendor confirmation.

## Affected products

| Product | Firmware | Evidence |
| --- | --- | --- |
| WAVLINK WL-WN570HA1-B (identified as WN570HA1B in firmware) | `M_V260622` | Dynamic behavior reproduced in isolated QEMU firmware emulation; default configuration has `FirstLogin=1`. |
| WAVLINK WL-WN570HA2 | `M_V260622` | Examined firmware contains the same `/bin/ioos` binary (MD5 `d36f269b26717d6b6303d74369cecbfa`) and default `FirstLogin=1`; no separate physical-device test. |

No other models or firmware versions have been confirmed for this advisory.

## Weakness and impact

- **CWE:** CWE-306 (Missing Authentication for Critical Function).
- **Access required:** A client must be able to reach the management interface while the device is in the first-login state.
- **Demonstrated impact:** An unauthenticated client obtained a token that passed `check_login` and authorized a time-zone setting change in the test environment.
- **Potential impact:** Other management operations that accept the same token may be accessible. Those operations were not individually verified for this advisory.

## Reproduction in an authorized test environment

1. Use an isolated device or firmware-emulation environment running the affected image. Confirm that `winstar.system.FirstLogin` is `1`.
2. From a client with access to the management interface, send a request without credentials or a Cookie:

   ```http
   GET /protocol.csp?fname=sys&opt=login&function=set HTTP/1.1
   Host: <test-device>
   ```

3. The WL-WN570HA1-B emulation returned HTTP 200 with `error: 0`, `init_status: 0`, and a 32-character hexadecimal `token` despite the absence of credentials. The actual token is omitted here.
4. Pass the returned token to `GET /protocol.csp?fname=net&opt=check_login&function=get&token=<TOKEN>`. The emulation returned `error: 0`.
5. A subsequent time-zone setting request using that token also returned `error: 0` in the isolated test. Perform any configuration-changing verification only on a device you control.

The observed response alone would not establish management access; the `check_login` and setting-change results provide that additional evidence. An actual hardware test has not been reported.

## Mitigation

Complete first-time setup promptly and limit access to the management interface to trusted clients. A firmware correction should require an authenticated or device-ownership-verified first-login flow before issuing a management token. These measures are recommendations, not a claim that a vendor patch is available.

## Identifier status and references

No assigned CVE ID is claimed for this finding. A pending request or CAN tracking number is not a CVE ID.

- [WAVLINK WL-WN570HA1-B support page](https://www.wavlink.com/en_us/drivers/download/e97662e196aa.html)
- [WAVLINK WL-WN570HA2 product page](https://www.wavlink.com/en_us/product/WL-WN570HA2.html)

