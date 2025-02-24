## State Transition Serialization Format

A minimal extension to the merklization of the state as described in the Graypaper D.2. The merklization is a lossy format. This extention introduces minimal metadata for state components and deltas entries. This metadata enables full state deserialization.

```zig
//// KeyTypes which require metadata
pub const DictKeyType = enum {
    delta_storage, // Service storage entries
    delta_preimage, // Service preimage entries
    delta_preimage_lookup, // Service preimage lookup entries
};

// Metadata for storage entries
pub const DeltaStorageMetadata = struct {
    storage_key: [32]u8,
};

// Metadata for preimage entries
pub const DeltaPreimageMetadata = struct {
    hash: [32]u8,
    preimage_length: u32,
};

// Metadata for preimage lookup entries
pub const DeltaPreimageLookupMetadata = struct {
    hash: [32]u8,
    preimage_length: u32,
};

// Union of all possible metadata types
pub const DictMetadata = union(DictKeyType) {
    delta_storage: DeltaStorageMetadata,
    delta_preimage: DeltaPreimageMetadata,
    delta_preimage_lookup: DeltaPreimageLookupMetadata,
};

// KeyVal entry
pub const StateEntry = struct {
    key: [32]u8,
    val: []const u8,
    metadata: ?DictMetadata, // optional
};

pub const StateSnapshot = struct {
    state_root: types.StateRoot,
    keyvals: []StateEntry,
};

pub const StateTransition = struct {
    pre_state: StateSnapshot,
    block: types.Block,
    post_state: StateSnapshot,
};
```

