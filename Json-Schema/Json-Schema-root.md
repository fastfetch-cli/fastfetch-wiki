# JSON config

**Title:** JSON config

|                           |             |
| ------------------------- | ----------- |
| **Type**                  | `object`    |
| **Required**              | No          |
| **Additional properties** | Not allowed |

**Description:** JSON config file for fastfetch. Usually located at `~/.config/fastfetch/config.jsonc`

| Property               | Pattern | Type        | Deprecated | Definition | Title/Description                                                                                        |
| ---------------------- | ------- | ----------- | ---------- | ---------- | -------------------------------------------------------------------------------------------------------- |
| - [$schema](#schema )  | No      | string      | No         | -          | JSON schema URL, for JSON validation and IDE intelligence                                                |
| - [logo](#logo )       | No      | Combination | No         | -          | Fastfetch logo configurations<br />See also https://github.com/fastfetch-cli/fastfetch/wiki/Logo-options |
| - [general](#general ) | No      | object      | No         | -          | Fastfetch general configurations                                                                         |
| - [display](#display ) | No      | object      | No         | -          | Configure how things should be displayed                                                                 |
| - [modules](#modules ) | No      | array       | No         | -          | Fastfetch modules to run    

---

Generated using [json-schema-for-humans](https://github.com/coveooss/json-schema-for-humans) on 2025-09-23 at 09:24:32 +0800
