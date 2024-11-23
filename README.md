This document describes a draft version of the lrb spec (version 0), files in this version can't be expected to always be in the correct format due to the version not bumping to 1 until the spec is finalized.

[This Other Document](https://tilde.town/~moss/lrr/lrb_proposal.html) describes the initial idea for the format, while this document tries to finalize the spec before a proper version 1 is declared.

### definitions & assumptions 
- all numbers written in little endian
- integers are named as `u` for unsigned and `i` for signed, followed by the number of bits, so a `u8` is one byte.
- floats are named as `f` followed by the number of bits, so an `f64` is a double precision float and an `f32` is a single precision float.
- a bool is one byte, which is `0` if it is false, or otherwise true.

### Mod Table
at the start of a .lrb file, the following is written:
|name|type|description
|-|-|-
|magic number|byte[3]|should always be `0x4C 0x52 0x42` (LRB).
|lrb version|u8|for this spec, value should be 0.
|mod count|u16|the amount of entries in the mod table
|[mod entries]|modtable_entry[mod count]|list of entries to the mod table, each one reading as described below

### Mod Table Entry
for each mod in the mod table, the following is written:
|name|type|description
|-|-|-
|name length|u8|the length of the name string
|name|utf-8 string of length [name length]|the name of the mod
|version|u16|starts at 0 and increments with each breaking change to a mod, such that an implementation can know if it's current version of a mod will properly load the file.
|section count|u8|the number of entries in this mod's section table
|[section entries]|section_entry[section count]|variable sized list of entries to this mod's section table, each one reading as described below. 
|optional flag|bool|indicates that this is an optional mod, and that the following string is present (if false the optional string  part of the entry is omitted)
|optional string length|u8|the length of the optional string
|optional string|utf-8 string of length [optional string length]|a string intended to be displayed for the user if the track loads without this mod present

### Section Table Entry
for each section in a mod's section table, the following is written:
|name|type|description
|-|-|-
|pointer|u64|an absolute pointer within the file (if the mod table is `0xFF` bytes long and this section immediately follows then the value of this pointer is `0xFF`)
|length|u64|length in bytes of this section
