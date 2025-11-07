# ISON (Interned String Object Notation)
Interned String Object Notation, or ISON, is a binary JSON-like format that also utilizes string interning to reduce size. It is mostly used for CharacterVisuals data. It is typically found after decompressing a franchise file table record's table3 data buffer.

## Games using this format
- Madden NFL 25
- Madden NFL 26

## Format
### Constants
ISON utilizes some constant values to denote certain kinds of objects/values, similar to JSON. These constants are listed below:
#### Object Constants
These constants generally denote objects or arrays:
| Constant Value | Description | JSON Equivalent |
|--|--|--|
|``0x0D``  | Begins an ISON file | N/A |
| ``0x11`` | Ends an ISON file | N/A |
| ``0x0F`` | Begins an object | ``{`` |
| ``0x13`` | Ends an object | ``}`` |
| ``0x0E`` | Begins an array | ``[`` |
| ``0x12`` | Ends an array | ``]`` |

#### Value Constants
These constants denote different data types.
| Constant Value | Description |
|--|--|
|``0x10``  | Key value pair |
| ``0x0A`` | Interned string |
| ``0x0B`` | Non-interned string |
| ``0x09`` | Double |
| ``0x03`` | Byte |

### Reading ISON
With the constants outlined above in mind, reading ISON is conceptually very similar to a JSON.

Once the ISON begin constant is read, the next byte is read and constant-specific logic continues based on that byte until the ISON end constant is reached.

#### Constant-specific Logic
Each ISON constant has its own logic for reading the associated object/value:
##### Interned String
Interned string values are unsigned shorts that must be looked up in the interned string lookup found in the engine files to get the associated string
##### Non-interned String
Non-interned string values begin with an unsigned integer containing the length of the string data, followed by the string data of that length.
##### Double/Byte
Doubles and bytes are read like standard binary doubles/bytes, there is nothing special about the way they are stored.
##### Key Value Pair
Key value pairs, as the name implies, contain two ISON values back to back. The first serves as the key, while the second serves as the value. 
##### Object
Objects generally contain zero or more key value pairs. Keep reading the next byte and then value logic based on that byte until the object end constant is reached.
##### Array
Arrays contain zero or more values of any type. Keep reading the next byte and then value logic based on that byte until the array end constant is reached.