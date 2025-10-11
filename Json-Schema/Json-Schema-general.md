- [3. Property `JSON config > general`](#general)
  - [3.1. Property `JSON config > general > thread`](#general_thread)
  - [3.2. Property `JSON config > general > escapeBedrock`](#general_escapeBedrock)
  - [3.3. Property `JSON config > general > playerName`](#general_playerName)
  - [3.4. Property `JSON config > general > dsForceDrm`](#general_dsForceDrm)
    - [3.4.1. Property `JSON config > general > dsForceDrm > oneOf > item 0`](#general_dsForceDrm_oneOf_i0)
    - [3.4.2. Property `JSON config > general > dsForceDrm > oneOf > item 1`](#general_dsForceDrm_oneOf_i1)
    - [3.4.3. Property `JSON config > general > dsForceDrm > oneOf > item 2`](#general_dsForceDrm_oneOf_i2)
  - [3.5. Property `JSON config > general > wmiTimeout`](#general_wmiTimeout)
  - [3.6. Property `JSON config > general > processingTimeout`](#general_processingTimeout)
  - [3.7. Property `JSON config > general > preRun`](#general_preRun)
  - [3.8. Property `JSON config > general > detectVersion`](#general_detectVersion)

## <a name="general"></a>3. Property `JSON config > general`

|                           |             |
| ------------------------- | ----------- |
| **Type**                  | `object`    |
| **Required**              | No          |
| **Additional properties** | Not allowed |

**Description:** Fastfetch general configurations

| Property                                           | Pattern | Type        | Deprecated | Definition | Title/Description                                                            |
| -------------------------------------------------- | ------- | ----------- | ---------- | ---------- | ---------------------------------------------------------------------------- |
| - [thread](#general_thread )                       | No      | boolean     | No         | -          | Use separate threads for HTTP requests                                       |
| - [escapeBedrock](#general_escapeBedrock )         | No      | boolean     | No         | -          | On Bedrock Linux, whether to escape the bedrock jail                         |
| - [playerName](#general_playerName )               | No      | string      | No         | -          | The name of the player to use for Media and Player modules. Linux only       |
| - [dsForceDrm](#general_dsForceDrm )               | No      | Combination | No         | -          | Force display detection to use DRM. Linux only                               |
| - [wmiTimeout](#general_wmiTimeout )               | No      | integer     | No         | -          | Set the timeout (ms) for WMI queries, \`-1\` for no timeout. Windows only    |
| - [processingTimeout](#general_processingTimeout ) | No      | integer     | No         | -          | Set the timeout (ms) when waiting for child processes, \`-1\` for no timeout |
| - [preRun](#general_preRun )                       | No      | string      | No         | -          | Set the command to be executed before printing logos                         |
| - [detectVersion](#general_detectVersion )         | No      | boolean     | No         | -          | Whether to detect and display component versions. Mainly for benchmarking    |

### <a name="general_thread"></a>3.1. Property `JSON config > general > thread`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Use separate threads for HTTP requests

### <a name="general_escapeBedrock"></a>3.2. Property `JSON config > general > escapeBedrock`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** On Bedrock Linux, whether to escape the bedrock jail

### <a name="general_playerName"></a>3.3. Property `JSON config > general > playerName`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** The name of the player to use for Media and Player modules. Linux only

### <a name="general_dsForceDrm"></a>3.4. Property `JSON config > general > dsForceDrm`

|                           |                  |
| ------------------------- | ---------------- |
| **Type**                  | `combining`      |
| **Required**              | No               |
| **Additional properties** | Any type allowed |
| **Default**               | `false`          |

**Description:** Force display detection to use DRM. Linux only

| One of(Option)                         |
| -------------------------------------- |
| [item 0](#general_dsForceDrm_oneOf_i0) |
| [item 1](#general_dsForceDrm_oneOf_i1) |
| [item 2](#general_dsForceDrm_oneOf_i2) |

#### <a name="general_dsForceDrm_oneOf_i0"></a>3.4.1. Property `JSON config > general > dsForceDrm > oneOf > item 0`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Try `wayland`, then `x11`, then `drm`

Specific value: `false`

#### <a name="general_dsForceDrm_oneOf_i1"></a>3.4.2. Property `JSON config > general > dsForceDrm > oneOf > item 1`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Use `/sys/class/drm` only

Specific value: `"sysfs-only"`

#### <a name="general_dsForceDrm_oneOf_i2"></a>3.4.3. Property `JSON config > general > dsForceDrm > oneOf > item 2`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Try `libdrm` first, then `sysfs` if libdrm fails

Specific value: `true`

### <a name="general_wmiTimeout"></a>3.5. Property `JSON config > general > wmiTimeout`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `5000`    |

**Description:** Set the timeout (ms) for WMI queries, `-1` for no timeout. Windows only

### <a name="general_processingTimeout"></a>3.6. Property `JSON config > general > processingTimeout`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `5000`    |

**Description:** Set the timeout (ms) when waiting for child processes, `-1` for no timeout

### <a name="general_preRun"></a>3.7. Property `JSON config > general > preRun`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |
| **Default**  | `""`     |

**Description:** Set the command to be executed before printing logos

### <a name="general_detectVersion"></a>3.8. Property `JSON config > general > detectVersion`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Whether to detect and display component versions. Mainly for benchmarking
