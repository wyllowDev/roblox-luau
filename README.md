# roblox-luau
A luau library to manipulate roblox place/model files
luau-roblox
A luau library to manipulate Roblox instances from place/model files.

Usage
Setup the library, ideally with .luaurc.

Virtual DataModel
Using the Virtual DataModel is easier, but it has more interface overhead.

local roblox = require("@roblox");

local placeContents = ...; -- read the place file
local game = roblox.deserializePlace(placeContents);

-- do something with the game

local content = roblox.serializePlace(game);
Note

The Virtual DataModel could upgrade deserialized instances, converting properties under instances to their latest version (based on the current version of luau-roblox), converting some deprecated properties.

This "upgrade" step is manually implemented under this library and must be activated.

To activate it, call applyPatches function after requiring the library:

local roblox = require("@roblox")

roblox.manager.applyPatches()
This step is optional, and removing this step could mean outdated properties would error on use.

DOM
Using the DOM over the Virtual DataModel is ideally faster, with zero overhead. The downside is that you have to manually handle the instances, classes, parents, sharedstrings, and references.

local roblox = require("@roblox");

local dom = roblox.dom;

local placeContents = ...; -- read the place file
local doc = dom.deserialize(placeContents);

-- do something with the doc

local content = dom.serialize(doc, "BINARY");
Types
The library does not include types internally, but types are available as a separate package: @roblox/types.

local roblox = require("@roblox");
local types = require("@roblox/types/roblox");
local enums_types = require("@roblox/types/roblox_enums");

local Enum: enums_types.Enums = roblox.manager.datatypes.Enum;

local object: types.Part = roblox.manager.Instance.new("Part");

object.Material = Enum.Material.Wood;
object.Posit|
        --  ^ <- autocomplete
Warning

The roblox types provided in this library are known to make the new luau type solver a ton slower.

It is recommended to use the library's provided roblox types on the older type solver.

alternatively you can use the built-in roblox types with luau-lsp to annotate with, which should be faster. But could be more ahead of the roblox version.

Runtime Specifications
Note

This library would attempt to use runtime native functions if available, otherwise fallback to pure Luau implementations.

General Runtime support requirements:

Luau (0.669+).
vector support.
Require-by-string.
.luaurc support.
runtime	capabilities	version	notes
zune	all	0.5.*	performs best with + this library utilizes all possible features
Lune	all-luau	0.10.*	fallbacks to most luau implementations
other/unknown	some	~	missing support for zstd, incomplete task, and datetime
If a runtime is not listed above, it would be treated as other/unknown. This just means the library does not have specific optimization for the runtime, and would use pure Luau implementations for everything, there will be missing features like no zstd support, smaller datetime support, poor lz4 compression, etc.

capabilities:

all means all complete use of features and support for this library.
all-luau means all features are supported, but this library would use pure luau implementations for missing runtime features.
most means some features are missing, but the library is still usable.
some means only basic features are supported, and the library could be slow but should remain usable.
Warning

Roblox binary format could use different compression methods, in which this library does not have pure Luau implementations for all of them, and currently only has lz4 luau implementation.

With the lz4 compression implementation in pure luau, the output size could be significantly worse, the size could be many times larger than the ideal/original size.

This library would use runtime native compression methods if available, and would be manually used by this library, those runtime without it should be noted above in the list as all-pure or below.

In most cases, Roblox uses lz4 compression for binary format.

Tip

For the best performance, and best compatibility, use this library with the zune runtime.

Information
Warning

64-bit integer data could not be represented fully due to luau's number limitations, the max precision is 2^53.

DOM supported formats:

Binary (.rbxl, .rbxm)
 deserialization
 serialization
XML (.rbxlx, .rbxmx)
 deserialization
 serialization
Goals:

High performance + compatibility
Up to date Roblox instances
