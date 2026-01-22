This document describes version 1 of the lrb header spec.

some definitions & assumptions are borrowed from [these documents](https://github.com/lrbspec/conventions). this includes how type names are written and what they mean (this is especially important for strings which can be written in a number of ways), as well as some important details about implementing support for the lrb format that are out of scope for this spec.

### Mod Table
at the start of a .lrb file, the following is written:
|name|type|description
|-|-|-
|magic number|byte[3]|should always be `0x4C 0x52 0x42` (LRB).
|lrb version|u8|for this spec, value should be 1.
|mod count|u16|the amount of entries in the mod table
|[mod entries]|modtable_entry[mod count]|list of entries to the mod table, each one reading as described below

the mod table should not have duplicate entries in it. duplicate entries are mods that have the same name string, regardless of version or other metadata. the standard way to handle files that break this invariant is to ignore any entries but the first.

### Mod Table Entry
for each mod in the mod table, the following is written:
|name|type|description
|-|-|-
|name|string with u8 length and utf-8 encoding|the name of the mod
|version|u16|starts at 0 and increments with each breaking change to a mod, such that an implementation can know if it's current version of a mod will properly load the file.
|modflags|u8|see [Modflags](#modflags)
|data pointer|u64|pointer to the start of this mod's data. may be omitted depending on modflags
|data length|u64|the length of the section of data this mod stores in the track, in bytes. may be omitted depending on modflags

### Modflags
the modflags are a single byte with individual bits indicating certain properties of a mod:

`000EDCBA`

|symbol|name|description
|-|-|-
|`A`|required|should be set to `1` if the mod is required. this means that it is *not* safe to ignore the mod.
|`B`|physics|can be set to `1` to indicate to the loading implementation that the absence of an optional mod will break physics compatibility if missing (but might not have any effect on the track otherwise)[^3]
|`C`|camera|can be set to `1` to indicate to the loading implementation that the absence of an optional mod will break camera compatibility if missing[^1][^3]
|`D`|scenery|can be set to `1` to indicate to the loading implementation that the absence of an optional mod will break scenery compatibility if missing[^2][^3]
|`E`|extra data|a value of `1` indicates the presence of the data pointer & length in the modtable entry. otherwise this mod's only data is it's presence in the modtable.
|`000`|unused|written as zeros and not used in any way when loading.


[^1]: if the absense of the mod means that the camera transform will be different for any given frame, this is breaking camera compatibility. it is important for animations, for example
[^2]: given an arbitrary camera position, can you render and get the same result with/without the mod? if the answer is no then that is breaking scenery compability
[^3]: the physics, camera, and scenery bits are hints to the implementation loading the track (which may not actually know of the mod and thus would like to know the consequences of ignoring it), and not necessarily critical to the lrb format, but it is good practice to set them correctly
