# Our names (`SCUS_949.00`)

Names the psx-crash port uses for this executable where they differ from or go beyond this tree's own. The symbol map
(`configs/USA/sym.main.txt`) keeps the tree's names; ours enter it only where the tree has none. "Port source" is the
psx-crash file and key that holds the address (`titles/<title>/executable.json` paths, or `entry/facts.cpp` initializers);
the port adopts the map's name when its constants are generated (psx-crash `docs/plans/0001-addresses-from-the-decomp-symbol-map.md`).

## Names that differ

| address | this tree | psx-crash | port source |
|---|---|---|---|
| `0x80011350` | `g_str_timeout_init` | `GpuWatchdogDiagnosticFormat` | `entry/facts.cpp kGpuWatchdog.diagnosticFormat` |
| `0x80011384` | `g_str_timeout_init_func_08x_08x_08x` | `GpuWatchdogCallbackFormat` | `entry/facts.cpp kGpuWatchdog.callbackFormat` |
| `0x80011D88` | `CORE_Main` | `NativeBootCMain` | `runtime.native_boot.c_main` |
| `0x80011FC4` | `CORE_Loop` | `NativeFrameCoreLoop` | `runtime.native_frame.core_loop` |
| `0x80012F10` | `Ns_PageUpdateEntries` | `PoolNode` | `runtime.pool_node` |
| `0x80015978` | `Ns_Probe` | `BlockPoolFindCell` | `runtime.block_pool.find_cell` |
| `0x800159C4` | `Ns_ProbeSafe` | `BlockPoolFindCellBounded` | `runtime.block_pool.find_cell_bounded` |
| `0x80015B58` | `Ns_Init` | `SceneLoadEntry` | `runtime.native_frame.scene.load_entry` |
| `0x8001652C` | `Core_Init` | `NativeBootInit` | `runtime.native_boot.init` |
| `0x80016E5C` | `Gpu_Update` | `NativeFrameGpuUpdate` | `runtime.native_frame.gpu_update` |
| `0x800172E8` | `Gpu_SwapBuffers` | `NativeFrameLoadingSwap` | `runtime.native_frame.loading_swap` |
| `0x80034504` | `RIncTickCount` | `RootCounterIncrement` | `runtime.native_frame.root_counter_increment` |
| `0x8003CB9C` | `Card_Init` | `NativeBootCallbackInitialize` | `runtime.native_boot.callback_initialize` |
| `0x8003D730` | `BIOS_Printf` | `GpuWatchdogLog` | `entry/facts.cpp kGpuWatchdog.log` |
| `0x8003E018` | `__SN_ENTRY_POINT` | `Crt0Entry` | `entry/facts.cpp kCrt0.entry` |
| `0x8003E0C0` | `__main` | `GameMainEntry` | `runtime.game_main_entry` |
| `0x8003E198` | `_bu_init` | `CallbackBootChangeClearPad` | `entry/facts.cpp kCallbackBoot.changeClearPad` |
| `0x8003E1A8` | `OpenEvent` | `CallbackBootOpenEvent` | `entry/facts.cpp kCallbackBoot.openEvent` |
| `0x8003E1D8` | `EnableEvent` | `CallbackBootCloseEvent` | `entry/facts.cpp kCallbackBoot.closeEvent` |
| `0x8003E1F8` | `EnterCriticalSection` | `FirstSyscallEntry` | `runtime.first_syscall_entry` |
| `0x8003E208` | `ExitCriticalSection` | `CallbackBootExitCritical` | `entry/facts.cpp kCallbackBoot.exitCritical` |
| `0x8003E298` | `SetRCnt` | `SetRootCounter` | `runtime.native_frame.set_root_counter` |
| `0x8003E370` | `StartRCnt` | `StartRootCounter` | `runtime.native_frame.start_root_counter` |
| `0x8003E3A4` | `StopRCnt` | `StopRootCounter` | `runtime.native_frame.stop_root_counter` |
| `0x8003E460` | `PadRead` | `NativeFrameBiosPadRead` | `runtime.native_frame.bios_pad_read` |
| `0x8003E4F0` | `VSync` | `Vsync` | `runtime.vsync` |
| `0x8003E870` | `SetIntrMask` | `GpuWatchdogCritical` | `entry/facts.cpp kGpuWatchdog.critical` |
| `0x80042864` | `set_alarm` | `NativeBootGpuWatchdogStart` | `runtime.native_boot.gpu_watchdog_start` |
| `0x80042898` | `get_alarm` | `NativeBootGpuWatchdogCheck` | `runtime.native_boot.gpu_watchdog_check` |
| `0x80043520` | `CdControl` | `NativeBootCdControl` | `runtime.native_boot.cd_control` |
| `0x80043668` | `CdControlF` | `NativeBootCdControlF` | `runtime.native_boot.cd_control_f` |
| `0x80043984` | `CdReadSync` | `NativeBootCdReadSync` | `runtime.native_boot.cd_read_sync` |
| `0x800439A4` | `CdRead` | `NativeBootCdRead` | `runtime.native_boot.cd_read` |
| `0x800440EC` | `CD_sync` | `NativeBootCdSync` | `runtime.native_boot.cd_sync` |
| `0x80044E8C` | `CD_reset` | `NativeBootCdInitialize` | `runtime.native_boot.cd_initialize` |
| `0x80051444` | `_card_auto` | `CallbackBootInitializePad` | `entry/facts.cpp kCallbackBoot.initializePad` |
| `0x80051454` | `InitCARD` | `CallbackBootStartPad` | `entry/facts.cpp kCallbackBoot.startPad` |
| `0x800514EC` | `g_ns_page_free_sentinel` | `PoolNodeTypeCallbackTable` | `runtime.pool_node.globals.type_callback_table` |
| `0x800549F0` | `g_vsync_cb_active_cnt` | `VsyncQueryCounter` | `runtime.vsync.query_counter` |
| `0x80054B40` | `g_gpu_stat_reg` | `GpuWatchdogGp0Pointer` | `entry/facts.cpp kGpuWatchdog.gp0Pointer` |
| `0x80054B44` | `g_gpu_dma2_madr_reg` | `GpuWatchdogStatusPointer` | `entry/facts.cpp kGpuWatchdog.statusPointer` |
| `0x80054B4C` | `g_gpu_dma2_chcr_reg` | `GpuWatchdogGp1Pointer` | `entry/facts.cpp kGpuWatchdog.gp1Pointer` |
| `0x80054B5C` | `g_gpu_dpcr_reg` | `GpuWatchdogDmaControlPointer` | `entry/facts.cpp kGpuWatchdog.dmaControlPointer` |
| `0x80054B60` | `g_gpu_queue_enabled` | `GpuWatchdogLastCallback` | `entry/facts.cpp kGpuWatchdog.lastCallback` |
| `0x80054B64` | `g_gpu_alarm_deadline` | `GpuWatchdogLastPayload` | `entry/facts.cpp kGpuWatchdog.lastPayload` |
| `0x80054B68` | `g_gpu_alarm_counters` | `GpuWatchdogLastArgument` | `entry/facts.cpp kGpuWatchdog.lastArgument` |
| `0x80054B70` | `g_gpu_q_write` | `GpuWatchdogQueueWrite` | `entry/facts.cpp kGpuWatchdog.queueWrite` |
| `0x80054B74` | `g_gpu_q_read` | `GpuWatchdogQueueRead` | `entry/facts.cpp kGpuWatchdog.queueRead` |
| `0x80054B80` | `g_gpu_reset_cnt` | `GpuWatchdogCriticalToken` | `entry/facts.cpp kGpuWatchdog.criticalToken` |
| `0x80054B84` | `g_gpu_alarm_active` | `GpuWatchdogDeadline` | `entry/facts.cpp kGpuWatchdog.deadline` |
| `0x80054B88` | `g_gpu_alarm_timeout_cnt` | `GpuWatchdogPollCount` | `entry/facts.cpp kGpuWatchdog.pollCount` |
| `0x800555A0` | `g_cd_sync_cb` | `LibcdInitLastSyncCallback` | `entry/facts.cpp kLibcdInit.lastSyncCallback` |
| `0x800555A4` | `g_cd_ready_cb` | `LibcdInitLastReadyCallback` | `entry/facts.cpp kLibcdInit.lastReadyCallback` |
| `0x800555B0` | `g_cd_read_sectors` | `LibcdInitLastStatus` | `entry/facts.cpp kLibcdInit.lastStatus` |
| `0x800555B4` | `g_cd_read_buf_ptr` | `LibcdInitLastResult` | `entry/facts.cpp kLibcdInit.lastResult` |
| `0x800555C0` | `g_cd_read_mode_val` | `LibcdInitLastCommand` | `entry/facts.cpp kLibcdInit.lastCommand` |
| `0x800555C1` | `g_cd_com_flags_tab` | `LibcdInitPendingCommand` | `entry/facts.cpp kLibcdInit.pendingCommand` |
| `0x8005587C` | `g_cd_status` | `LibcdInitSyncStatus` | `entry/facts.cpp kLibcdInit.syncStatus` |
| `0x80055880` | `g_cd_read_state` | `LibcdInitCommandWorkspace` | `entry/facts.cpp kLibcdInit.commandWorkspace` |
| `0x80056428` | `gp_bonus_return` | `NativeFrameDone` | `runtime.native_frame.done.address` |
| `0x80056710` | `gp_pbak_mode` | `SceneIdAddress` | `runtime.native_frame.scene.id_address` |
| `0x80056714` | `gp_pbak_frame_idx` | `SceneRequestAddress` | `runtime.native_frame.scene.request_address` |
| `0x80057054` | `g_pad_mode_state` | `NativeFrameBiosPadRead` | `runtime.native_frame.bios_pad_read.address` |
| `0x8005728C` | `g_ns_recent_page_eids` | `PoolNodeEscapeClassTable` | `runtime.pool_node.globals.escape_class_table` |
| `0x800580A0` | `g_slst_buf0` | `PoolNodeHandleTable` | `runtime.pool_node.globals.handle_table` |
| `0x8005C530` | `g_ns_hash_buckets` | `FindCellBucketTablePointer` | `runtime.block_pool.find_cell.bucket_table_pointer` |
| `0x8005C534` | `g_ns_entry_table` | `FindCellBoundedPoolBasePointer` | `runtime.block_pool.find_cell_bounded.pool_base_pointer` |
| `0x8005C540` | `g_ns_nsd_header` | `FindCellBoundedCellCountBlockPointer` | `runtime.block_pool.find_cell_bounded.cell_count_block_pointer` |
| `0x8005CFAC` | `g_ns_active_cd_slot` | `PoolNodeNodeCursor` | `runtime.pool_node.globals.node_cursor` |
| `0x8005CFBC` | `g_ns_page_status_map` | `BlockPoolSideIndexTable` | `runtime.block_pool.pool_globals.side_index_table.address` |
| `0x8005E03C` | `g_file_map` | `FrameSceneTable` | `entry/facts.cpp kFrame.sceneTable` |

## Names added to the map

| address | added as | kind | port source |
|---|---|---|---|
| `0x80012510` | `NativeFrameTransition` | func | `runtime.native_frame.transition` |
| `0x80056410` | `NativeBootUseCdAddress` | data | `runtime.native_boot.use_cd_address` |
| `0x8005C550` | `BlockPoolHeapTop` | data | `runtime.block_pool.pool_globals.heap_top.address` |
| `0x8005C554` | `PoolNodeNodeTable` | data | `runtime.pool_node.globals.node_table` |

## Code sites

Addresses inside a function the port stops at, returns to or patches; not symbols.

| address | where | port source |
|---|---|---|
| `0x80012030` | `NativeFrameCoreLoop + 0x6C` | `runtime.native_frame.iteration.entry` |
| `0x80012510` | `NativeFrameCoreLoop + 0x54C` | `runtime.native_frame.done.load_entry` |
| `0x80013094` | `PoolNode + 0x184` | `runtime.pool_node.sites.measure_dead_end` |
| `0x80013110` | `PoolNode + 0x200` | `runtime.pool_node.sites.escape_class_init` |
| `0x8001313C` | `PoolNode + 0x22C` | `runtime.pool_node.sites.call_to_lookup` |
| `0x80013140` | `PoolNode + 0x230` | `runtime.pool_node.sites.call_to_lookup_delay_slot` |
| `0x80015978` | `BlockPoolFindCell + 0x0` | `runtime.block_pool.find_cell.class_shift_site.address` |
| `0x80015980` | `BlockPoolFindCell + 0x8` | `runtime.block_pool.find_cell.bucket_load_site.address` |
| `0x80015984` | `BlockPoolFindCell + 0xC` | `runtime.block_pool.find_cell.bucket_mask_site.address` |
| `0x800159A4` | `BlockPoolFindCell + 0x2C` | `runtime.block_pool.find_cell.stride_site.address` |
| `0x800159A8` | `BlockPoolFindCell + 0x30` | `runtime.block_pool.fault_site.address` |
| `0x800170F4` | `NativeFrameGpuUpdate + 0x298` | `runtime.native_frame.first_vsync` |
| `0x800170FC` | `NativeFrameGpuUpdate + 0x2A0` | `runtime.native_frame.after_first_vsync` |
| `0x80017124` | `NativeFrameGpuUpdate + 0x2C8` | `runtime.native_frame.second_vsync` |
| `0x8001712C` | `NativeFrameGpuUpdate + 0x2D0` | `runtime.native_frame.after_vsync` |
| `0x80017328` | `NativeFrameLoadingSwap + 0x40` | `runtime.native_frame.loading_swap.after_vsync` |
| `0x8003E470` | `NativeFrameBiosPadRead + 0x10` | `runtime.native_frame.bios_pad_read.load_entry` |
| `0x800431B8` | `_patch_gte + 0x1C` | `runtime.first_bios_dispatch_return_address` |
| `0x800431E8` | `_patch_gte + 0x4C` | `runtime.post_get_c0_dispatch_return_address` |
