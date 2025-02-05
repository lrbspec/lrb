This document describes a draft version of the lrb spec (version 0), files in this version can't be expected to always be in the correct format due to the version not bumping to 1 until the spec is finalized.

[This Other Document](https://tilde.town/~moss/lrr/lrb_proposal.html) describes the initial idea for the format, while this document tries to finalize the spec before a proper version 1 is declared.

some definitions & assumptions are borrowed from [this document](https://github.com/lrbspec/conventions). this includes how type names are written and what they mean (this is especially important for strings which can be written in a number of ways).

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
|name|string with u8 length and utf-8 encoding|the name of the mod
|version|u16|starts at 0 and increments with each breaking change to a mod, such that an implementation can know if it's current version of a mod will properly load the file.
|data present|bool|is true if this mod stores data aside from it's presence in the modtable. if false the data pointer and data length parts of the entry are omitted.
|data pointer|u64|pointer to the start of this mod's data
|data length|u64|the length of the section of data this mod stores in the track, in bytes
|optional flag|bool|indicates that this is an optional mod, and that the following string is present (if false the optional string part of the entry is omitted)
|optional string|string with u8 length and utf-8 encoding|a string intended to be displayed for the user if the track loads without this mod present
