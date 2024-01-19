# Source Engine/Postal III MDL
Documentation on the modified version of the Source Engine MDL model format from the video game [Postal III](https://en.wikipedia.org/wiki/Postal_III) developed by Trashmasters and Running with Scissors and published by Akella.

Things that were added in Postal III are marked in bold as **(Added in Postal III)**.
- [Source Engine/Postal III MDL](#source-enginepostal-iii-mdl)
	- [Data types](#data-types)
	- [Technical informations](#technical-informations)
	- [Header (`studiohdr_t`)](#header-studiohdr_t)
	- [Header 2 (`studiohdr2_t`)](#header-2-studiohdr2_t)
	- [Flags](#flags)
		- [Model flags](#model-flags)
		- [Bone flags](#bone-flags)
		- [Motion flags](#motion-flags)
		- [Sequence flags](#sequence-flags)
		- [Autolayer flags](#autolayer-flags)
	- [Structs](#structs)
		- [`mstudiobone_t`](#mstudiobone_t)
		- [`mstudiobonecontroller_t`](#mstudiobonecontroller_t)
		- [`mstudiohitboxset_t`](#mstudiohitboxset_t)
		- [`mstudioanimdesc_t`](#mstudioanimdesc_t)
		- [`mstudioseqdesc_t`](#mstudioseqdesc_t)
		- [`mstudiotexture_t`](#mstudiotexture_t)
		- [`mstudiobodyparts_t`](#mstudiobodyparts_t)
		- [`mstudioattachment_t`](#mstudioattachment_t)
		- [`mstudioflexdesc_t`](#mstudioflexdesc_t)
		- [`mstudioflexcontroller_t`](#mstudioflexcontroller_t)
		- [`mstudioflexrule_t`](#mstudioflexrule_t)
		- [`mstudioikchain_t`](#mstudioikchain_t)
		- [`mstudiomouth_t`](#mstudiomouth_t)
		- [`mstudioposeparamdesc_t`](#mstudioposeparamdesc_t)
		- [`mstudioiklock_t`](#mstudioiklock_t)
		- [`mstudiomodelgroup_t`](#mstudiomodelgroup_t)
		- [`mstudioanimblock_t`](#mstudioanimblock_t)
		- [`mstudioflexcontrollerui_t`](#mstudioflexcontrollerui_t)
		- [`mstudiolinearbone_t`](#mstudiolinearbone_t)
		- [`mstudioikrule_t`](#mstudioikrule_t)
		- [`mstudiocompressedikerror_t`](#mstudiocompressedikerror_t)
		- [`mstudioikerror_t`](#mstudioikerror_t)
		- [`mstudioanimvalue_t`](#mstudioanimvalue_t)
		- [`mstudioanim_t`](#mstudioanim_t)
		- [`mstudiomodel_t`](#mstudiomodel_t)
		- [`mstudiomesh_t`](#mstudiomesh_t)
		- [`mstudio_modelvertexdata_t`](#mstudio_modelvertexdata_t)
		- [`mstudio_meshvertexdata_t`](#mstudio_meshvertexdata_t)
		- [`mstudiovertex_t`](#mstudiovertex_t)
		- [`mstudioboneweight_t`](#mstudioboneweight_t)
		- [`mstudiobolton_t` **(Added in Postal III)**](#mstudiobolton_t-added-in-postal-iii)
		- [`mstudioprefab_t` **(Added in Postal III)**](#mstudioprefab_t-added-in-postal-iii)
		- [`sortedmeshvertex_t` **(Added in Postal III)**](#sortedmeshvertex_t-added-in-postal-iii)
		- [`sortedmesh_t` **(Added in Postal III)**](#sortedmesh_t-added-in-postal-iii)
	- [QC commands](#qc-commands)
		- [$rollingDrag](#rollingdrag)
		- [$insertbone](#insertbone)
		- [$plates](#plates)
		- [$sortplates](#sortplates)
		- [$plateorigin](#plateorigin)
		- [$hboxxform](#hboxxform)
		- [$bolton](#bolton)
		- [$prefab](#prefab)
		- [$cloth](#cloth)
		- [$sortedmesh](#sortedmesh)
		- [$helperbone](#helperbone)

Some of these informations of the Source Engine MDL were taken from [here](https://developer.valvesoftware.com/wiki/MDL_(Source)), with additions of Postal III's extra data.

## Data types
A list of data types used in the MDL data.
| Data type | Description |
| ----------- | ----------- |
| int | 4 bytes representing a signed 32-bit integer number. (-2147483648 to 2147483647) |
| unsigned int | 4 bytes representing an unsigned 32-bit integer number. (0 to 4294967295) |
| float | 4 bytes representing a float-value number. |
| bool | A single byte representing a true/false boolean value. (0 for false, 1 for true) |
| char | A single byte character, can be an array containing more than one, usually to read a string. (-127 to 127 or 0 to 255) |
| unsigned char | A single byte unsigned character. (0 to 255) |
| byte | A single byte that can be a value from `0x00` to `0xFF`. (0 to 255) |
| short | 2 bytes representing a short (16-bit) integer number. (-32768 to 32767)
| Vector | Consists of 3 (4 bytes) float-values (X, Y, Z) coordinates, making it 12 bytes. |
| Vector2D | Consists of 2 (4 bytes) float-values (X, Y) coordinates, making it 8 bytes. |
| Vector4D | Consists of 4 (4 bytes) float-values (X, Y, Z, W) coordinates, making it 16 bytes. |
| Quaternion | [Learn more](https://en.wikipedia.org/wiki/Quaternion) |
| RadianEuler | [Learn](https://en.wikipedia.org/wiki/Radian) [more](https://en.wikipedia.org/wiki/Euler%27s_formula)
| matrix3x4_t | [Learn more](https://developer.valvesoftware.com/wiki/Matrix3x4_t) |

## Technical informations

* Limit for `MAXSTUDIOSKINS` was increased from 32 to 256, allowing models to have 256 total textures.
* Limit for `MAXSTUDIOANIMS` for studiomdl was increased from 2000 to 3000. Surprisingly, this is still not enough to recompile Postal Dude's whole set of animations as it contains more than 3000 animations (about 3085 SMD animation files when decompiling the animation file using [Crowbar](https://developer.valvesoftware.com/wiki/Crowbar)), but the game can load it just fine. It is unknown on how the developers managed to get it compiled despite that defined limit.
* As part of the defined `STUDIO_ANIM_*` definitions, Postal III adds `STUDIO_ANIM_QUATROT	0x40 (64)` (Rotation animation is stored in quaternions, not angles)
* Just like above, they also added `STUDIO_ANIM_QUATROT_WIDTH` as shown in the code block below. (Comments that were in russian were roughly translated with [DeepL](https://www.deepl.com/en/translator) to english.)
```C++
#define STUDIO_ANIM_RAWPOS	0x01 // Vector48
#define STUDIO_ANIM_RAWROT	0x02 // Quaternion48
#define STUDIO_ANIM_ANIMPOS	0x04 // mstudioanim_valueptr_t
#define STUDIO_ANIM_ANIMROT	0x08 // mstudioanim_valueptr_t
#define STUDIO_ANIM_DELTA	0x10
#define STUDIO_ANIM_RAWROT2	0x20 // Quaternion64
#ifdef POSTAL3
#define STUDIO_ANIM_QUATROT	0x40 // Rotation animation is stored in quaternions, not angles
#endif

#ifdef POSTAL3
// Quaternion for turn animation. 32 bits is sometimes not enough
typedef Quaternion32 mstudioquat_t;
// How much mstudioanimvalue_t fits in mstudioquat_t
#define STUDIO_ANIM_QUATROT_WIDTH (sizeof(mstudioquat_t)/sizeof(mstudioanimvalue_t))
#endif
```

## Header (`studiohdr_t`)
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | id | Model format ID, such as `IDST` (`0x49 0x44 0x53 0x54`) |
| int | version | Format version number, such as `48` (`0x30 0x00 0x00 0x00`) |
| int | checksum | This has to be the same in the phy and vtx files to load! |
| char | name | The internal name of the model, padding with null bytes. Typically `my_model.mdl` will have an internal name of `my_model`. Limit of 64 characters. |
| int | length | Data size of MDL file in bytes. |
| Vector | eyeposition | Position of player viewpoint relative to model origin |
| Vector | illumposition | Position (relative to model origin) used to calculate ambient light contribution and cubemap reflections for the entire model. |
| Vector | hull_min | Corner of model hull box with the least (X, Y, Z) values |
| Vector | hull_max | Opposite corner of model hull box |
| Vector | view_bbmin | Same, but for bounding box |
| Vector | view_bbmax | Which is used for view culling |
| int | flags | Binary flags in little-endian order. Example: (`00000001,00000000,00000000,11000000`) means flags for position 0, 30, and 31 are set. See model flags section for more information |
| int | numbones | Number of data sections (of type [mstudiobone_t](#mstudiobone_t)) |
| int | boneindex | Offset of first data section then reads it |
| int | numbonecontrollers | Number of data sections (of type [mstudiobonecontroller_t](#mstudiobonecontroller_t)) |
| int | bonecontrollerindex | Offset of first data section then reads it |
| int | numhitboxsets | Number of data sections (of type [mstudiohitboxset_t](#mstudiohitboxset_t)) |
| int | hitboxsetindex | Offset of first data section then reads it. (Look up hitbox set by index, then calls through to set to get hitbox count for set.) |
| int | numlocalanim | Number of animations/poses (of type [mstudioanimdesc_t](#mstudioanimdesc_t)) |
| int | localanimindex | Offset of first animation descriptions then reads it |
| int | numlocalseq | Number of sequences (of type [mstudioseqdesc_t](#mstudioseqdesc_t)) |
| int | localseqindex | Offset of first sequence then reads it |
| int | activitylistversion | Initialization flag - have the sequences been indexed? |
| int | eventsindexed | ??? |
| int | numtextures | Number of raw textures (of type [mstudiotexture_t](#mstudiotexture_t)) |
| int | textureindex | Offset of first raw texture (containing VMT texture filenames) then reads it |
| int | numcdtextures | Number of raw textures search paths (texture directory offsets) |
| int | cdtextureindex | This offset points to a series of ints. Each int value, in turn, is an offset relative to the start of this header/the-file, at which there is a null-terminated string. |
| int | numskinref | Replaceable textures tables (Skin reference count) |
| int | numskinfamilies | Each skin-family assigns a texture-id to a skin location |
| int | skinindex | Offset to first skin reference then reads it |
| int | numbodyparts | Number of body parts (also known as bodygroups) (of type [mstudiobodyparts_t](#mstudiobodyparts_t)) |
| int | bodypartindex | Offset of first body part then reads it |
| int | numlocalattachments | Number of queryable local attachment points (of type [mstudioattachment_t](#mstudioattachment_t)) |
| int | localattachmentindex | Offset of first queryable local attachment point then reads it |
| int | numlocalnodes | Number of node values (animation node to animation node transition graph) |
| int | localnodeindex | Offset to first node value then reads it. (Node values appear to be single bytes) |
| int | localnodenameindex | Offset to node value names. (Names are null-terminated strings.) |
| int | numflexdesc | Number of flex descriptions (of type [mstudioflexdesc_t](#mstudioflexdesc_t)) |
| int | flexdescindex | Offset of first flex description then reads it |
| int | numflexcontrollers | Number of flex controllers (of type [mstudioflexcontroller_t](#mstudioflexcontroller_t)) |
| int | flexcontrollerindex | Offset of first flex controller then reads it |
| int | numflexrules | Number of flex rules (of type [mstudioflexrule_t](#mstudioflexrule_t)) |
| int | flexruleindex | Offset of first flex rule then reads it |
| int | numikchains | Number of inverse kinematics (chain) (of type [mstudioikchain_t](#mstudioikchain_t)) |
| int | ikchainindex | Offset of first inverse kinematic (chain) then reads it |
| int | nummouths | Number of mouths (of type [mstudiomouth_t](#mstudiomouth_t)) |
| int | mouthindex | Offset of first mouth then reads it |
| int | numlocalposeparameters | Number of local pose parameters (of type [mstudioposeparamdesc_t](#mstudioposeparamdesc_t)) |
| int | localposeparamindex | Offset of first local pose parameter then reads it |
| int | surfacepropindex | Offset to surface property value then reads it (single null-terminated string) |
| int | keyvalueindex | Offset to key-value data then reads it after the next variable below. (Unusual: In this one index comes first, then count.) |
| int | keyvaluesize | Key-value data is a series of strings. If you can't find what you're interested in, check the associated PHY file as well. |
| int | numlocalikautoplaylocks | Number of inverse kinematics (lock) (of type [mstudioiklock_t](#mstudioiklock_t)) |
| int | localikautoplaylockindex | Offset of first inverse kinematic (lock) then reads it |
| float | mass | Mass of object (4-bytes) in kilograms. (The collision model mass) |
| int | contents | contents flag, as defined in [bspflags.h](https://github.com/ValveSoftware/source-sdk-2013/blob/master/sp/src/public/bspflags.h#L17). Not all content types are valid; see documentation on [$contents](https://developer.valvesoftware.com/wiki/contents) QC command. |
| int | numincludemodels | Number of included external animations, models, etc. (of type [mstudiomodelgroup_t](#mstudiomodelgroup_t)). Other models can be referenced for re-used sequences and animations (See also: The [$includemodel](https://developer.valvesoftware.com/wiki/includemodel) QC option.) |
| int | includemodelindex | Offset of first included model then reads it |
| int | virtualModel | Placeholder for mutable-void* (Note that the SDK only compiles as 32-bit, so an int and a pointer are the same size (4 bytes)) (Implementation specific back pointer to virtual data) |
| int | szanimblocknameindex | Offset of first animblock name then reads it. (For demand loaded animation blocks) |
| int | numanimblocks | Number of animblocks (of type [mstudioanimblock_t](#mstudioanimblock_t)) |
| int | animblockindex | Offset of first animblock then reads it |
| int | animblockModel | Placeholder for mutable-void* |
| int | bonetablebynameindex | Points to a series of bytes? |
| int | pVertexBase | Placeholder for void* (Used by tools only that don't cache, but persist mdl's peer data engine uses virtualModel to back link to cache pointers) |
| int | pIndexBase | Placeholder for void* (Same as above) |
| byte | constdirectionallightdot | Used with [$constantdirectionallight](https://developer.valvesoftware.com/wiki/constantdirectionallight) from the QC. Model should have flag #13 set if enabled. |
| byte | rootLOD | Preferred rather than clamped |
| byte | numAllowedRootLODs | 0 means any allowed, N means Lod 0 -> (N-1) |
| byte | unused | ??? |
| int | unused4 | ??? (Zero out if version < 47) |
| int | numflexcontrollerui | Number of flex controller UIs (of type [mstudioflexcontrollerui_t](#mstudioflexcontrollerui_t)) |
| int | flexcontrolleruiindex | Offset of first flex controller UI then reads it |
| float | flVertAnimFixedPointScale | ??? |
| int | unused3 | ??? |
| int | studiohdr2index | Offset for additional header information. May be zero if not present, or also 408 if it immediately follows this [studiohdr_t](#header-studiohdr_t). |
| int | unused2 | ??? |

## Header 2 (`studiohdr2_t`)
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | numsrcbonetransform | Number of source bone transforms (???) |
| int | srcbonetransformindex | Offset to first source bone transform then reads it (???) |
| int | illumpositionattachmentindex | Offset to illum position attachment then reads it (???) |
| float | flMaxEyeDeflection | If set to 0, then equivalent to cos(30) |
| int | linearboneindex | Offset to linear bone (of type [mstudiolinearbone_t](#mstudiolinearbone_t)) then reads it |
| int | numboltons | Number of boltons (of type [mstudiobolton_t](#mstudiobolton_t-added-in-postal-iii)) **(Added in Postal III)** |
| int | boltonindex | Offset of first bolton then reads it (`numboltons` x [mstudiobolton_t](#mstudiobolton_t-added-in-postal-iii)) **(Added in Postal III)** |
| int | numprefabs | Number of prefabs (of type [mstudioprefab_t](#mstudioprefab_t-added-in-postal-iii)) **(Added in Postal III)** |
| int | prefabindex | Offset of first prefab then reads it (`numprefabs` x [mstudioprefab_t](#mstudioprefab_t-added-in-postal-iii)) **(Added in Postal III)** |
| int | reserved | Reserved space (55 x 4 bytes) |

## Flags
### Model flags
| Name | Position | Details |
| ----------- | ----------- | ----------- |
| STUDIOHDR_FLAGS_AUTOGENERATED_HITBOX | 0 | This flag is set if no hitbox information was specified. |
| STUDIOHDR_FLAGS_USES_ENV_CUBEMAP | 1 | This flag is set at loadtime, not mdl build time so that we don't have to rebuild models when we change materials. |
| STUDIOHDR_FLAGS_FORCE_OPAQUE | 2 | Use this when there are translucent parts to the model but we're not going to sort it. |
| STUDIOHDR_FLAGS_TRANSLUCENT_TWOPASS | 3 | Use this when we want to render the opaque parts during the opaque pass and the translucent parts during the translucent pass. Added using [$mostlyopaque](https://developer.valvesoftware.com/wiki/mostlyopaque) to the QC. |
| STUDIOHDR_FLAGS_STATIC_PROP | 4 | This is set any time the .qc files has [$staticprop](https://developer.valvesoftware.com/wiki/staticprop) in it. Means there's no bones and no transforms. |
| STUDIOHDR_FLAGS_USES_FB_TEXTURE | 5 | This flag is set at loadtime, not mdl build time so that we don't have to rebuild models when we change materials. |
| STUDIOHDR_FLAGS_HASSHADOWLOD | 6 | This flag is set by studiomdl.exe if a separate "[$shadowlod](https://developer.valvesoftware.com/wiki/lod)" entry was present for the .mdl (the shadow lod is the last entry in the lod list if present). |
| STUDIOHDR_FLAGS_USES_BUMPMAPPING | 7 | This flag is set at loadtime, not mdl build time so that we don't have to rebuild models when we change materials. |
| STUDIOHDR_FLAGS_USE_SHADOWLOD_MATERIALS | 8 | This flag is set when we should use the actual materials on the shadow LOD instead of overriding them with the default one (necessary for translucent shadows). |
| STUDIOHDR_FLAGS_OBSOLETE | 9 | This flag is set when we should use the actual materials on the shadow LOD instead of overriding them with the default one (necessary for translucent shadows). |
| STUDIOHDR_FLAGS_UNUSED | 10 |
| STUDIOHDR_FLAGS_NO_FORCED_FADE | 11 | This flag is set at mdl build time. |
| STUDIOHDR_FLAGS_FORCE_PHONEME_CROSSFADE | 12 | The npc will lengthen the viseme check to always include two phonemes. |
| STUDIOHDR_FLAGS_CONSTANT_DIRECTIONAL_LIGHT_DOT | 13 | This flag is set when the .qc has [$constantdirectionallight](https://developer.valvesoftware.com/wiki/constantdirectionallight) in it. If set, we use constantdirectionallightdot to calculate light intensity rather than the normal directional dot product. Only valid if `STUDIOHDR_FLAGS_STATIC_PROP` is also set. |
| STUDIOHDR_FLAGS_FLEXES_CONVERTED | 14 | Flag to mark delta flexes as already converted from disk format to memory format. |
| STUDIOHDR_FLAGS_BUILT_IN_PREVIEW_MODE | 15 | Indicates the studiomdl was built in preview mode (added with the `-preview` flag). |
| STUDIOHDR_FLAGS_AMBIENT_BOOST | 16 | Ambient boost (runtime flag). |
| STUDIOHDR_FLAGS_DO_NOT_CAST_SHADOWS | 17 | Don't cast shadows from this model (useful on first-person models). |
| STUDIOHDR_FLAGS_CAST_TEXTURE_SHADOWS | 18 | Alpha textures should cast shadows in vrad on this model (ONLY prop_static!). Usually forced in the [RAD file](https://developer.valvesoftware.com/wiki/RAD_file), although it can be defined using [$casttextureshadows](https://developer.valvesoftware.com/wiki/casttextureshadows) in the [QC](https://developer.valvesoftware.com/wiki/QC) file. |
| STUDIOHDR_FLAGS_SUBDIVISION_SURFACE | 19 | Model has a quad-only Catmull-Clark SubD cage. **(Does not exist in Postal III)** |
| STUDIOHDR_FLAGS_NO_ANIM_EVENTS | 20 | Flagged on load to indicate no animation events on this model. **(Does not exist in Postal III)** |
| STUDIOHDR_FLAGS_VERT_ANIM_FIXED_POINT_SCALE | 21 | If flag is set then studiohdr_t.flVertAnimFixedPointScale contains the scale value for fixed point vert anim data, if not set then the scale value is the default of 1.0 / 4096.0.  Regardless use studiohdr_t::VertAnimFixedPointScale() to always retrieve the scale value. **(Does not exist in Postal III)** |
| STUDIOHDR_FLAGS_SORT_MESHES_BY_DISTANCE | 30 | Sort translucent meshes by distance. **(Added in Postal III)** |

### Bone flags
| Flag | Value | Description |
| ----------- | ----------- | ----------- |
| BONE_PHYSICALLY_SIMULATED | 1 | Bone is physically simulated when physics are active |
| BONE_PHYSICS_PROCEDURAL | 2 | Procedural when physics is active |
| BONE_ALWAYS_PROCEDURAL | 4 | Bone is always procedurally animated |
| BONE_SCREEN_ALIGN_SPHERE | 8 | Bone aligns to the screen, not constrained in motion. |
| BONE_SCREEN_ALIGN_CYLINDER | 16 | Bone aligns to the screen, constrained by it's own axis. |
| BONE_CALCULATE_MASK | 31 |
| BONE_USED_BY_HITBOX | 256 | Bone (or child) is used by a hit box (A hitbox is attached to this bone) |
| BONE_USED_BY_ATTACHMENT | 512 | Bone (or child) is used by an attachment point (An attachment is attached to this bone) |
| BONE_USED_BY_VERTEX_LOD0 | 1024 | Bone (or child) is used by the toplevel model via skinned vertex |
| BONE_USED_BY_VERTEX_LOD1 | 2048 | Bone (or child) is used by the level 1 model via skinned vertex |
| BONE_USED_BY_VERTEX_LOD2 | 4096 | Bone (or child) is used by the level 2 model via skinned vertex |
| BONE_USED_BY_VERTEX_LOD3 | 8192 | Bone (or child) is used by the level 3 model via skinned vertex |
| BONE_USED_BY_VERTEX_LOD4 | 16384 | Bone (or child) is used by the level 4 model via skinned vertex |
| BONE_USED_BY_VERTEX_LOD5 | 32768 | Bone (or child) is used by the level 5 model via skinned vertex |
| BONE_USED_BY_VERTEX_LOD6 | 65536 | Bone (or child) is used by the level 6 model via skinned vertex |
| BONE_USED_BY_VERTEX_LOD7 | 131072 | Bone (or child) is used by the level 7 model via skinned vertex |
| BONE_USED_BY_VERTEX_MASK | 261120 |
| BONE_USED_BY_BONE_MERGE | 262144 | Bone is available for bone merge to occur against it |
| BONE_USED_BY_ANYTHING | 524032 | Is this bone used by anything? (If any `BONE_USED_BY_*` flags are true) |
| BONE_USED_MASK | 524032 | Most likely same as above flag |
| BONE_USED_BY_EYES_ATTACHMENT | 524288 | Bone (or child) is used by eyes attachment point (An attachment is attached to this bone) **(Added in Postal III)** |

### Motion flags
| Flag | Value | Description |
| ----------- | ----------- | ----------- |
| STUDIO_X | 1 |
| STUDIO_Y | 2 |
| STUDIO_Z | 4 |
| STUDIO_XR | 8 |
| STUDIO_YR | 16 |
| STUDIO_ZR | 32 |
| STUDIO_LX | 64 |
| STUDIO_LY | 128 |
| STUDIO_LZ | 256 |
| STUDIO_LXR | 512 |
| STUDIO_LYR | 1024 |
| STUDIO_LZR | 2048 |
| STUDIO_LINEAR | 4096 |
| STUDIO_TYPES | 262143 |
| STUDIO_RLOOP | 262144 | Controller that wraps shortest distance |

### Sequence flags
| Flag | Value | Description |
| ----------- | ----------- | ----------- |
| STUDIO_LOOPING | 1 | Ending frame should be the same as the starting frame |
| STUDIO_SNAP | 2 | Do not interpolate between previous animation and this one |
| STUDIO_DELTA | 4 | This sequence "adds" to the base sequences, not slerp blends |
| STUDIO_AUTOPLAY | 8 | Temporary flag that forces the sequence to always play |
| STUDIO_POST | 16 |
| STUDIO_ALLZEROS | 32 | This animation/sequence has no real animation data |
| STUDIO_CYCLEPOSE | 128 | Cycle index is taken from a pose parameter index |
| STUDIO_REALTIME | 256 | Cycle index is taken from a real-time clock, not the animations cycle index |
| STUDIO_LOCAL | 512 | Sequence has a local context sequence |
| STUDIO_HIDDEN | 1024 | Don't show in default selection views |
| STUDIO_OVERRIDE | 2048 | A forward declared sequence (empty) |
| STUDIO_ACTIVITY | 4096 | Has been updated at runtime to activity index |
| STUDIO_EVENT | 8192 | Has been updated at runtime to event index |
| STUDIO_WORLD | 16384 | Sequence blends in worldspace |
| STUDIO_NO_IK | 32768 | Sequence don't have IK rules **(Added in Postal III)** |

### Autolayer flags
| Flag | Value | Description |
| ----------- | ----------- | ----------- |
| STUDIO_AL_POST | 16 |
| STUDIO_AL_SPLINE | 64 | Convert layer ramp in/out curve is a spline instead of linear |
| STUDIO_AL_XFADE | 128 | Pre-bias the ramp curve to compense for a non-1 weight, assuming a second layer is also going to accumulate |
| STUDIO_AL_NOBLEND | 512 | Animation always blends at 1.0 (ignores weight) |
| STUDIO_AL_LOCAL | 4096 | Layer is a local context sequence |
| STUDIO_AL_POSE | 16384 | Layer blends using a pose parameter instead of parent cycle |

## Structs

### `mstudiobone_t`
Bones
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sznameindex | Offset for null-terminated string then reads it |
| int | parent | Parent bone |
| int | bonecontroller | bone controller index, -1 == none (6 x 4 bytes) |
| Vector | pos | Default values (Position) |
| Quaternion | quat | Default values (Quaternion) |
| RadianEuler | rot | Default values (Radian Euler) |
| Vector | posscale | Compression scale (Position) |
| Vector | rotscale | Compression scale (Rotation) |
| matrix3x4_t | poseToBone | Pose to bone |
| Quaternion | qAlignment | Alignment |
| int | flags | Flags |
| int | proctype | Procedural type |
| int | procindex | Procedural rule |
| int | physicsbone | Index into physically simulated bone |
| int | surfacepropidx | Index into string table for property name |
| int | contents | See [BSPFlags.h](https://github.com/ValveSoftware/source-sdk-2013/blob/master/sp/src/public/bspflags.h#L17) for the contents flags |
| int | unused | Remove as appropriate (8 x 4 bytes) |

### `mstudiobonecontroller_t`
Bone controllers
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | bone | -1 == 0 |
| int | type | X, Y, Z, XR, YR, ZR, M |
| float | start | ??? |
| float | end | ??? |
| int | rest | Byte index value at rest |
| int | inputfield | 0-3 user set controller, 4 mouth |
| int | unused | (8 x 4 bytes) |

### `mstudiohitboxset_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sznameindex | Offset for null-terminated string then reads it |
| int | numhitboxes | Number of hitboxes |
| int | hitboxindex | Offset to first hitbox then reads it |

### `mstudioanimdesc_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | baseptr | Base pointer |
| int | sznameindex | Offset for null-terminated string then reads it |
| float | fps | Frames per second |
| int | flags | Looping/non-looping flags |
| int | numframes | Number of frames |
| int | nummovements | Number of movements (Piecewise movement) |
| int | movementindex | Offset to first movement then reads it |
| int | unused1 | Remove as appropriate (and zero if loading older versions) (6 x 4 bytes) |
| int | animblock | Anim block |
| int | animindex | Offset to anim block then reads it. (Non-zero when anim data isn't in sections) |
| int | numikrules | Number of IK rules |
| int | ikruleindex | Offset to first IK rule then reads it. (Non-zero when IK data is stored in the mdl) |
| int | animblockikruleindex | Offset to anim block IK rule then reads it. (Non-zero when IK data is stored in animblock file) |
| int | numlocalhierarchy | Number of local hierarchy |
| int | localhierarchyindex | Offset to first local hierarchy then reads it |
| int | sectionindex | Offset to first fast lookup section then reads it |
| int | sectionframes | Number of frames used in each fast lookup section, zero if not used |
| short | zeroframespan | Frames per span |
| short | zeroframecount | Number of spans |
| int | zeroframeindex | Offset to first zero frame data then reads it |
| float | zeroframestalltime | Saved during read stalls |

### `mstudioseqdesc_t`
Sequence descriptions
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | baseptr | Base pointer |
| int | szlabelindex | Offset for null-terminated string then reads it |
| int | szactivitynameindex | Offset to activity name then reads it |
| int | flags | Looping/non-looping flags |
| int | activity | Initialized at loadtime to game DLL values |
| int | actweight | Activity weight |
| int | numevents | Number of events |
| int | eventindex | Offset to first event then reads it |
| Vector | bbmin | Per sequence bounding box (Minimum) |
| Vector | bbmax | Per sequence bounding box (Maximum) |
| int | numblends | Number of blends |
| int | animindexindex | Index into array of shorts which is groupsize[0] x groupsize[1] in length then reads it |
| int | movementindex | [blend] Float array for blended movement |
| int | groupsize | Group size (2 x 4 bytes) |
| int | paramindex | X, Y, Z, XR, YR, ZR (2 x 4 bytes) |
| float | paramstart | Local (0..1) starting value (2 x 4 bytes) |
| float | paramend | Local (0..1) ending value (2 x 4 bytes) |
| int | paramparent | Parent parameter |
| float | fadeintime | Ideal cross fate in time (0.2 default) |
| float | fadeouttime | Ideal cross fade out time (0.2 default) |
| int | localentrynode | Transition node at entry |
| int | localexitnode | Transition node at exit |
| int | nodeflags | Transition rules |
| float | entryphase | Used to match entry gait |
| float | exitphase | Used to match exit gait |
| float | lastframe | Frame that should generation EndOfSequence |
| int | nextseq | Auto advancing sequences |
| int | pose | Index of delta animation between end and nextseq |
| int | numikrules | Number of IK rules |
| int | numautolayers | Number of auto layers |
| int | autolayerindex | Offset to first auto layer then reads it |
| int | weightlistindex | Offset to weight list then reads it |
| int | posekeyindex | Offset to pose key (FIXME: make this 2D instead of 2x1D arrays) |
| int | numiklocks | Number of IK locks |
| int | iklockindex | Offset to first IK lock then reads it |
| int | keyvalueindex | Offset to first key value then reads it after the variable below |
| int | keyvaluesize | Size of key values |
| int | cycleposeindex | Index of pose parameter to use as cycle index |
| int | unused | Remove/add as appropriate (grow back to 8 ints on version change!) (7 x 4 bytes) |

### `mstudiotexture_t`
Skin info

Number of bytes past the beginning of this structure where the first character of the texture name can be found. Struct is 64 bytes long.
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sznameindex | Offset for null-terminated string then reads it |
| int | flags | Flags |
| int | used | Padding? |
| int | unused1 | Padding. |
| int | unused | Final padding (10 x 4 bytes) |

### `mstudiobodyparts_t`
Body part index
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sznameindex | Offset for null-terminated string then reads it |
| int | nummodels | Number of models |
| int | base | Base |
| int | modelindex | Index into models array then reads it |

### `mstudioattachment_t`
Attachment
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sznameindex | Offset for null-terminated string then reads it |
| unsigned int | flags | Attachment flags |
| int | localbone | Local bone |
| matrix3x4_t | local | Attachment point |
| float | scale | Scale of the attachment (3 x 4 bytes) **(Added in Postal III)** |
| int | unused | Final padding (5 x 4 bytes) |
```
Attachment flags:

ATTACHMENT_FLAG_WORLD_ALIGN 0x10000
```

### `mstudioflexdesc_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | szFACSindex | Offset to flex description then reads it |

### `mstudioflexcontroller_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sztypeindex | Offset to flex controller type then reads it |
| int | sznameindex | Offset for null-terminated string then reads it |
| int | localToGlobal | Remapped at load time to master list |
| float | min | Minimum flex controller value |
| float | max | Maximum flex controller value |

### `mstudioflexrule_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | flex | Flex ID (?) |
| int | numops | Number of operations (?) |
| int | opindex | Offset to first operation then reads it (?) |

### `mstudioikchain_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sznameindex | Offset for null-terminated string then reads it |
| int | linktype | Link type |
| int | numlinks | Number of links |
| int | linkindex | Offset to first link then reads it |

### `mstudiomouth_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | bone | Bone ID (?) |
| Vector | forward | Position of "forward" (?) |
| int | flexdesc | Flex description ID (?) |

### `mstudioposeparamdesc_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sznameindex | Offset for null-terminated string then reads it |
| int | flags | Flags (????) |
| float | start | Starting value |
| float | end | Ending value |
| float | loop | Looping range, 0 for no looping, 360 for rotations, etc. |

### `mstudioiklock_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | chain | IK lock chain (?) |
| float | flPosWeight | Weight position (?) |
| float | flLocalQWeight | Local quaternion weight (?) |
| int | flags | Flags (?) |
| int | unused | Final padding (4 x 4 bytes) |

### `mstudiomodelgroup_t`
Demand loaded sequence groups
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | szlabelindex | Offset to textual name then reads it |
| int | sznameindex | Offset to file name then reads it |

### `mstudioanimblock_t`
Used for piecewise loading of animation data
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | datastart | Offset to start of animation data (?) |
| int | dataend | Offset to end of animation data (?) |

### `mstudioflexcontrollerui_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sznameindex | Offset for null-terminated string then reads it |
| int | szindex0 | Read code block below |
| int | szindex1 | Read code block below |
| int | szindex2 | Read code block below |
| unsigned char | remaptype | See the `FlexControllerRemapType_t` enum in code block below |
| bool | stereo | Is this a stereo control? |
| byte | unused | Final padding (2 x 4 bytes) |
```C++
// For szindex0, szindex1 and szindex2 variables

// These are used like a union to save space
// Here are the possible configurations for a UI controller
//
// SIMPLE NON-STEREO:	0: control	1: unused	2: unused
// STEREO:				0: left		1: right	2: unused
// NWAY NON-STEREO:		0: control	1: unused	2: value
// NWAY STEREO:			0: left		1: right	2: value

// For remaptype variable
enum FlexControllerRemapType_t
{
	FLEXCONTROLLER_REMAP_PASSTHRU = 0,
	FLEXCONTROLLER_REMAP_2WAY,	// Control 0 -> ramps from 1-0 from 0->0.5. Control 1 -> ramps from 0-1 from 0.5->1
	FLEXCONTROLLER_REMAP_NWAY,	// StepSize = 1 / (control count-1) Control n -> ramps from 0-1-0 from (n-1)*StepSize to n*StepSize to (n+1)*StepSize. A second control is needed to specify amount to use
	FLEXCONTROLLER_REMAP_EYELID
};
```

### `mstudiolinearbone_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | numbones | Number of bones |
| int | flagsindex | Offset to flags then reads it |
| int | parentindex | Offset to parent then reads it |
| int | posindex | Offset to position then reads it |
| int | quatindex | Offset to quaternion then reads it |
| int | rotindex | Offset to rotation then reads it |
| int | posetoboneindex | Offset to "pose to bone" then reads it |
| int | posscaleindex | Offset to position scale then reads it |
| int | rotscaleindex | Offset to rotation scale then reads it |
| int | qalignmentindex | Offset to quaternion alignment then reads it |
| int | unused | Final padding (6 x 4 bytes) |

### `mstudioikrule_t`
**(enum Flags added in Postal III for this struct)**
```C++
enum Flags
{
	eNone			= 0,
	eTranslateOnly	= 1<<0
};
```
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | index | Index |
| int | type | Type |
| int | chain | Chain |
| int | bone | Bone |
| int | slot | iktarget slot.  Usually same as chain. |
| float | height | Height |
| float | radius | Radius |
| float | floor | Floor |
| Vector | pos | Position |
| Quaternion | q | Quaternion |
| int | compressedikerrorindex | Offset to compressed IK error (of type [mstudiocompressedikerror_t](#mstudiocompressedikerror_t)) then reads it |
| int | unused2 | Unused |
| int | iStart | ??? |
| int | ikerrorindex | Offset to IK error (of type [mstudioikerror_t](#mstudioikerror_t)) then reads it |
| float | start | Beginning of influence |
| float | peak | Start of full influence |
| float | tail | End of full influence |
| float | end | End of all influence |
| float | unused3 | Unused |
| float | contact | Frame footstep makes ground contact |
| float | drop | How far down the foot should drop when reaching for IK |
| float | top | Top of the foot box |
| int | unused6 | Unused |
| int | unused7 | Used as flags in Postal 3 (See enum Flags above this table) |
| int | unused8 | Unused |
| int | szattachmentindex | Offset to name of world attachment then reads it |
| int | unused | Final padding (7 x 4 bytes) |

### `mstudiocompressedikerror_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| float | scale | Scale (6 x 4 bytes) |
| short | offset | Offset to anim value (of type [mstudioanimvalue_t](#mstudioanimvalue_t)) then reads it (6 x 4 bytes) |

### `mstudioikerror_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| Vector | pos | Position |
| Quaternion | q | Quaternion |

### `mstudioanimvalue_t`
Animation frames
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| byte | valid | ??? |
| byte | total | Total animation frames (?) |
| short | value | ??? |

### `mstudioanim_t`
Per bone per animation DOF and weight pointers

**(Postal III seems to add `STUDIO_ANIM_QUATROT` for flags checking for the pointer "pPosV")**
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| byte | bone | Bone |
| byte | flags | Weighing options |
| short | nextoffset | Offset to next one (?) |

### `mstudiomodel_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| char | name | Name of the model. Limit of 64 characters. |
| int | type | Type of model (?) |
| float | boundingradius | Bounding radius of the model |
| int | nummeshes | Number of meshes |
| int | meshindex | Offset to mesh (of type [mstudiomesh_t](#mstudiomesh_t)) then reads it |
| int | numvertices | Number of unique vertices/normals/texcoords |
| int | vertexindex | Offset to vertex Vector then reads it |
| int | tangentsindex | Offset to tangents Vector then reads it |
| int | numattachments | Number of attachments |
| int | attachmentindex | Offset to first attachment then reads it |
| int | numeyeballs | Number of eyeballs |
| int | eyeballindex | Offset to first eyeball |
| mstudio_modelvertexdata_t | vertexdata | Vertex data (of type [mstudio_modelvertexdata_t](#mstudio_modelvertexdata_t)) |
| unsigned char | eyesdeflection | Eyes deflection (2 bytes) **(Added in Postal III)** |
| char | unused0 | Unused (2 bytes) **(Added in Postal III)** |
| int | unused | Final padding (7 x 4 bytes) |

### `mstudiomesh_t`
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | material | Material |
| int | modelindex | Offset to model (of type [mstudiomodel_t](#mstudiomodel_t)) |
| int | numvertices | Number of unique vertices/normals/texcoords |
| int | vertexoffset | Vertex [mstudiovertex_t](#mstudiovertex_t) |
| int | numflexes | Number of flexes (Vertex animation) |
| int | flexindex | Offset to first flex then reads it |
| int | materialtype | Material type |
| int | materialparam | Material parameters |
| int | meshid | A unique ordinal for this mesh |
| Vector | center | Center position of the mesh (?) |
| mstudio_meshvertexdata_t | vertexdata | Vertex data (of type [mstudio_meshvertexdata_t](#mstudio_meshvertexdata_t)) |
| int | unused | Final padding (8 x 4 bytes) |

### `mstudio_modelvertexdata_t`
```C++
struct mstudio_modelvertexdata_t
{
	DECLARE_BYTESWAP_DATADESC();
	Vector				*Position( int i ) const;
	Vector				*Normal( int i ) const;
	Vector4D			*TangentS( int i ) const;
	Vector2D			*Texcoord( int i ) const;
	mstudioboneweight_t	*BoneWeights( int i ) const;
	mstudiovertex_t		*Vertex( int i ) const;
	bool				HasTangentData( void ) const;
	int					GetGlobalVertexIndex( int i ) const;
	int					GetGlobalTangentIndex( int i ) const;

	// base of external vertex data stores
	const void			*pVertexData;
	const void			*pTangentData;
};
```

### `mstudio_meshvertexdata_t`
```C++
struct mstudio_meshvertexdata_t
{
	DECLARE_BYTESWAP_DATADESC();
	Vector				*Position( int i ) const;
	Vector				*Normal( int i ) const;
	Vector4D			*TangentS( int i ) const;
	Vector2D			*Texcoord( int i ) const;
	mstudioboneweight_t *BoneWeights( int i ) const;
	mstudiovertex_t		*Vertex( int i ) const;
	bool				HasTangentData( void ) const;
	int					GetModelVertexIndex( int i ) const;
	int					GetGlobalVertexIndex( int i ) const;

	// indirection to this mesh's model's vertex data
	const mstudio_modelvertexdata_t	*modelvertexdata;

	// used for fixup calcs when culling top level lods
	// expected number of mesh verts at desired lod
	int					numLODVertexes[MAX_NUM_LODS];
};
```

### `mstudiovertex_t`
NOTE: This is exactly 48 bytes
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| mstudioboneweight_t | m_BoneWeights | Bone weights (of type [mstudioboneweight_t](#mstudioboneweight_t)) |
| Vector | m_vecPosition | Vector position |
| Vector | m_vecNormal | Vector normal |
| Vector2D | m_vecTexCoord | 2D vector of texture coordinates |

### `mstudioboneweight_t`
16 bytes
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| float | weight | Weight (Size of `MAX_NUM_BONES_PER_VERT` being 3) |
| char | bone | Bone (Size of `MAX_NUM_BONES_PER_VERT` being 3) |
| byte | numbones | Number of bones |

### `mstudiobolton_t` **(Added in Postal III)**
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | type | Bolton type (Hair, Glasses, Masks, Bracelet1, Bracelet2, Earring1, Earring2, Ring1, Ring2, Pin1, Pin2) |
| int | szmodelnameindex | Offset to bolton model name then reads it |

### `mstudioprefab_t` **(Added in Postal III)**
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | sznameindex | Offset to prefab name then reads it |
| int | skin | Skin ID for this prefab |
| int | boltonsmask | Bolton mask (Bit mask value in power of 2 like flags to support multiple boltons up to 32 in one prefab) |
| int | bodypartsindex | Offset to values of each bodypart for this prefab then reads them (`numbodyparts` x `byte`) |

### `sortedmeshvertex_t` **(Added in Postal III)**
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| Vector | position | Vector position |
| Vector | normal | Vector normal |
| Vector4D | tangentS | 4D vector tangent (?) |
| Vector2D | texCoord | 2D vector texture coordinate |

### `sortedmesh_t` **(Added in Postal III)**
| Data | Variable | Description |
| ----------- | ----------- | ----------- |
| int | numvertices | Number of vertices |
| int | numfaces | Number of faces |
| int | verticesoffset | Offset to vertices |
| int | facesoffset | Offset to faces |
| int | facecentersoffset | Offset to face centers |
| char | material | Material name. Limit of 64 characters. |
| Vector | mins | Minimums |
| Vector | maxs | Maximums |

Below is the C++ source code for the 4 newly added structs from Postal III
```C++
struct mstudiobolton_t
{
	DECLARE_BYTESWAP_DATADESC();

	int						type;
	int						szmodelnameindex;
	inline const char*		pszModelName() const { return ((char *)this) + szmodelnameindex; }
};

struct mstudioprefab_t
{
	DECLARE_BYTESWAP_DATADESC();

	int						sznameindex;
	inline const char*		pszName() const { return ((char *)this) + sznameindex; }
	int						skin;
	int						boltonsmask;
	int						bodypartsindex;
	inline const byte*		pBodypartsRef() const { return ((byte*)this) + bodypartsindex; }
};

//-----------------------------------------------------------------------------
// sortedmesh_t
//-----------------------------------------------------------------------------

struct sortedmeshvertex_t
{
	DECLARE_BYTESWAP_DATADESC();

	Vector		position;
	Vector		normal;
	Vector4D	tangentS;
	Vector2D	texCoord;
};

struct sortedmesh_t
{
	DECLARE_BYTESWAP_DATADESC();

	int			numvertices;
	int			numfaces;

	int			verticesoffset;
	int			facesoffset;
	int			facecentersoffset;

	char		material[64];
	Vector		mins;
	Vector		maxs;

	const sortedmeshvertex_t* pVertices() const { return ((const sortedmeshvertex_t*)((const byte*)(this) + verticesoffset)); }
	const unsigned short* pIndices() const { return ((const unsigned short*)((const byte*)(this) + facesoffset)); }
	const Vector* pFaceCenters() const { return ((const Vector*)((const byte*)(this) + facecentersoffset)); }
};
```

## QC commands
You can find more about the known/existing QC commands [here](https://developer.valvesoftware.com/wiki/Category:QC_Commands). All other commands below will only be the new ones **added in Postal III**.

### $rollingDrag
This QC command is an addition to the existing QC command [$collisionmodel](https://developer.valvesoftware.com/wiki/collisionmodel). Might be similar to $drag but for rolling?

### $insertbone
Inserts a new bone.

(Not figured out yet, seems to expect some arguments, otherwise it's incomplete if nothing provided.)

### $plates
Seems to be related with materials. Currently unknown on what it does.

### $sortplates
Possibly used for "sorting plates" order from the QC command above. Currently unknown on what it exactly does.

### $plateorigin
Plate origin (?)

(Not figured out yet, seems to expect some arguments, otherwise it's incomplete if nothing provided.)

### $hboxxform
Hitbox "xform".

(Not figured out what it is yet, seems to expect some arguments, otherwise it's incomplete if nothing provided.)

### $bolton
Adds a new bolton to the model, typically for a character model supporting getting accessories with attachment bones.

First argument expects any sort of unique name for a bolton.

Second argument is the type of bolton from this list: `Hair`, `Glasses`, `Masks`, `Bracelet1`, `Bracelet2`, `Earring1`, `Earring2`, `Ring1`, `Ring2`, `Pin1`, `Pin2`

Third argument is the name of the model (without the .mdl file extension) found in the appropriate folder depending of the bolton type chosen in second argument. See `p3\models\characters\bolt-on` folder to then check the correct folder of the type you're choosing to find the name of the model you want to use.

Example:
```C++
$bolton "Glasses" "glasses" "Dude_glass_02"
```

### $prefab
Adds a new prefab to the model, usually for a character model allowing to have more than one character, like a base human model. Take `M_Avg` from Postal III for example which has over a hundred of prefabs being a different character per prefab.

A prefab consists of a unique prefab name, one or more boltons, a skin ID from the skin families (defined with [$texturegroup](https://developer.valvesoftware.com/wiki/texturegroup)) and an ID per bodygroup.

So far, only the unique bolton was figured out to work for second argument. Unique skin ID and bodygroup model has not been figured out yet with the exception of adding a # at the start of a skin definition inside [$texturegroup](https://developer.valvesoftware.com/wiki/texturegroup) then any name written after it to possibly define a unique name or ID for a skin. See the small example below. **It is an addition from Postal III to $texturegroup QC command.**

```C++
$texturegroup "skinfamilies"
{
	{#TheTemplate "template_body" "template_head" }
	{#TheDude "Postal_Dude_body" "Postal_Dude_head" }
}
```

First argument expects any sort of unique name for a prefab.

Second argument is a unique ID for a skin, bolton or bodygroup model.

Example **(incomplete)**:
```C++
$prefab "Dude_raincoat_01" "Glasses"
```

### $cloth
Adds a cloth mesh. This was most likely used for the PhysX features included in Postal III for cloth physics.

First argument most likely expects the name of a cloth text file or a cloth mesh. See the cloth text files found in `p3\models\cloth` for more informations.

Example **(incomplete)**:
```C++
$cloth "advertizing_01"
```

### $sortedmesh
According to the description of the flag [STUDIOHDR_FLAGS_SORT_MESHES_BY_DISTANCE](#flags), this seems to be used for sorting translucent meshes by distance or possibly not necessarily by distance, but something along those lines.

(Not figured out how it works yet, seems to expect some arguments, otherwise it's incomplete if nothing provided.)

### $helperbone
Helper bone.

(Not figured out what it is yet, seems to expect some arguments, otherwise it's incomplete if nothing provided.)
