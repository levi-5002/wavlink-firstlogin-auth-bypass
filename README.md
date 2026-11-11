# WAVLINK FirstLogin management token issue

Independent security research concerning the WAVLINK WL-WN570HA1-B and WL-WN570HA2. This repository is not operated by WAVLINK.

## Advisory

- [FirstLogin authentication bypass in firmware M_V260622](advisories/FirstLogin-authentication-bypass.md)
- Weakness: CWE-306, Missing Authentication for Critical Function
- CVE status: No CVE ID has been verified for this finding. A CVE request or its tracking CAN must not be represented as an assigned CVE.

The advisory describes the conditions and evidence observed in an isolated firmware emulation. The WL-WN570HA1-B behavior was tested dynamically. The WL-WN570HA2 assessment is based on a byte-identical `/bin/ioos` binary and the same default `FirstLogin` setting; execution on physical hardware was not demonstrated.

Only firmware version `M_V260622` is included in the affected scope. Other WAVLINK models and versions are not claimed to be affected.

