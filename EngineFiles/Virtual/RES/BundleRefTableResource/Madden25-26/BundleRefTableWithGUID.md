# BundleRefTableWithGUID (RES)
BundleRefTableWithGUID is a variant of the `BundleRefTableResource` resource type, used in Madden 25-26.

## Games using this format
- Madden NFL 25
- Madden NFL 26

## Format Overview
The main sections of the resource are:

1. `RES_HEADER`
2. `BRT_HEADER`
3. `BundleRef[]`
4. `Asset[]`
5. `AssetLookup[]`
6. `Bundle[]` (until relocation table offset)
7. relocation table (`uint[]`)

### Constants
| Name | Value | Description |
|--|--|--|
| `META_SIZE` | `0x10` | Base offset added to internal pointers to account for resource meta at start of binary data |

### String Pointer behavior
All string fields are references to an address in the string table, calculated as:

`absoluteOffset = META_SIZE + pointerValue`

This applies to `brtNameOffset`, `namePtr`, and `directoryPtr` fields throughout the template.

Strings at each offset are standard null-terminated strings.

### RES_HEADER
| Field | Type | Description |
|--|--|--|
| `relocTableOffset` | `uint` | Offset (relative to start of resource data) where relocation table begins |
| `relocTableSize` | `uint` | Relocation table size in bytes |
| *(padding)* | `ulong` | Normally empty bytes |

### BRT_HEADER
| Field | Type | Description |
|--|--|--|
| `brtNameOffset` | `uint64` | Pointer to BRT name string |
| `brtName` | `string` | Name value resolved from `brtNameOffset` |
| `assetLookupsPtr` | `uint64` | Pointer to `AssetLookup[]` |
| `bundleRefsPtr` | `uint64` | Pointer to `BundleRef[]` |
| `assetsPtr` | `uint64` | Pointer to `Asset[]` |
| `bundlesPtr` | `uint64` | Pointer to `Bundle[]` |
| `emptyStringPtr` | `uint64` | Pointer to empty string |
| `primaryInstanceGuid` | `GUID` | Primary EBX instance GUID for the BRT |
| *(padding)* | 16 bytes | Normally empty bytes |
| `assetLookupCount` | `uint` | Number of entries in `AssetLookup[]` |
| `bundleRefCount` | `uint` | Number of entries in `BundleRef[]` |
| `assetCount` | `uint` | Number of entries in `Asset[]` |
| `unkZero` | `uint` | Unknown (always zero) |
| `unkHash` | `uint` | Unknown (possibly bundle heap type, always the same) |
| `unkZero2` | `uint` | Unknown (always zero) |
| `unkOne` | `uint` | Unknown (always one) |
| `unkZero3` | `uint` | Unknown (always zero) |

### BundleRef
| Field | Type | Description |
|--|--|--|
| `namePtr` | `uint64` | Pointer to BundleRef name |
| `directoryPtr` | `uint64` | Pointer to directory string |
| `bundlePtr` | `uint64` | Pointer to parent `Bundle` entry in `Bundle[]` |

### Asset
| Field | Type | Description |
|--|--|--|
| `namePtr` | `uint64` | Pointer to Asset name |
| `directoryPtr` | `uint64` | Pointer to directory string |

### AssetLookup
| Field | Type | Description |
|--|--|--|
| `hash` | `uint64` | Lookup hash (FNV-64 of either the name or full path of referenced `Asset`, converted to lowercase) |
| `bundleRefIndex` | `uint` | Index into `BundleRef[]` |
| `assetIndex` | `uint` | Index into `Asset[]` |

### Bundle
| Field | Type | Description |
|--|--|--|
| `namePtr` | `uint64` | Pointer to bundle name |
| `parentBundlePtr` | `uint64` | Pointer/reference to parent bundle |

### Relocation table
After bundle parsing ends, relocation entries are read as:

- `relocCount = relocTableSize / 4`
- `relocTable[relocCount]` as `uint`

## Notes
- Entries in `AssetLookup[]` must be sorted in ascending order by hash value
- When an asset is looked up in a BRT by string, the string is converted to lowercase and hashed using FNV-64. Then, a match for that hash is searched for in `AssetLookup[]`. If found, the bundle referenced by the associated `BundleRef` is loaded in order to load the referenced `Asset`
