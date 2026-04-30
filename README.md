# vers-sdk (Zig)

Zig SDK for the Vers VM orchestration platform.

## Installation

Add as a Zig dependency via `build.zig.zon` or copy `src/client.zig` directly:

```zig
// build.zig.zon
.dependencies = .{
    .vers_sdk = .{
        .url = "https://github.com/hdresearch/zig-sdk/archive/refs/heads/main.tar.gz",
    },
},
```

## Usage

```zig
const std = @import("std");
const vers = @import("vers_sdk");

pub fn main() !void {
    var gpa = std.heap.GeneralPurposeAllocator(.{}){};
    defer _ = gpa.deinit();
    const allocator = gpa.allocator();

    const client = vers.Client.init(allocator, .{});

    // List VMs
    var resp = try client.listVms(null);
    defer resp.deinit();

    const parsed = try resp.json();
    defer parsed.deinit();

    // Create a root VM
    var root = try client.createNewRootVm(
        \\{"vm_config":{"vcpu_count":2,"mem_size_mib":2048}}
    , true, null);
    defer root.deinit();

    // Branch from commit
    var branch = try client.branchByCommit(commit_id,
        \\{}
    , null, null);
    defer branch.deinit();
}
```

## Environment Variables

- `VERS_API_KEY` — API key for authentication (read automatically)
- `VERS_BASE_URL` — Override base URL (default: `https://api.vers.sh`)

## Building

```bash
zig build
zig build test
```
