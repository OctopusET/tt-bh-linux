## Booting All 4 L2CPUs

Use the `boot_all` make target. See the "Booting all 4 L2CPUs" section of the
top-level `README.md`.

L2CPU0 and L2CPU1 each have a dedicated 4G DRAM tile, so they use the full 4G.
L2CPU2 and L2CPU3 share a 4G DRAM tile, so each must be given a non-overlapping
half of the memory.

`boot_all` handles this without separate device trees: it boots all four from
the same `blackhole-card.dtb` and `boot.py` patches the `memory@` node per
L2CPU at runtime via `--mem_start`/`--mem_size` (L2CPU2 gets the first 2G,
L2CPU3 the upper 2G). The reserved-memory and virtio regions are derived from
that patched memory region, so they follow automatically.

### Legacy: static per-L2CPU device trees

`blackhole-p100-2.dts` and `blackhole-p100-3.dts` are the older approach, where
each L2CPU's memory split was baked into a separate device tree (the first/last
2G respectively, with matching reserved-memory and pmem regions). These are kept
for reference only; `boot_all` no longer uses them. If you do use them, note
that they must be kept in sync with `blackhole-p100.dts` in the kernel tree.
