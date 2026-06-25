# Tips: Building a Singleton Mutex/Event Killer (Roblox Manager Method)

---

## What it does

Some applications use a named Windows Mutex and/or named Event to enforce single-instance behavior. When a second instance launches, it checks for these objects and exits if they already exist. To allow multiple instances, you need to close those handles inside the running process before the new instance checks for them.

---

## Key Windows APIs to mention to the AI

- `NtQuerySystemInformation` with `SystemHandleInformation` (class 16) — enumerates every open handle across all processes system-wide
- `CreateToolhelp32Snapshot` + `Process32FirstW`/`Process32NextW` — lists running processes so you can find the target PIDs
- `OpenProcess` with `PROCESS_DUP_HANDLE` — opens a handle to the target process
- `DuplicateHandle` — used twice: once to get a local copy of the remote handle (for comparison), once with `DUPLICATE_CLOSE_SOURCE` flag to close the handle inside the remote process without needing a direct handle close
- `CompareObjectHandles` — verifies that a duplicated handle refers to the same kernel object as your reference handle (avoids closing the wrong handles)
- `OpenMutexW` / `OpenEventW` — open reference handles to the named objects so you have something to compare against

---

## The algorithm to describe

1. Find all PIDs of the target process by name using the toolhelp snapshot
2. Open reference handles to the named mutex and event using `OpenMutexW`/`OpenEventW`
3. Call `NtQuerySystemInformation` to get the full system handle table (may need to retry with a larger buffer if it returns `STATUS_INFO_LENGTH_MISMATCH`)
4. From the handle table, find your own process's entry for each reference handle to determine its object type number
5. Filter the handle table to entries owned by the target PIDs with the same object type number
6. For each candidate, `DuplicateHandle` it into your own process and use `CompareObjectHandles` to confirm it's the same kernel object
7. For confirmed handles, call `DuplicateHandle` again with `DUPLICATE_CLOSE_SOURCE` — this closes the handle inside the remote process as a side effect
8. Close all temporary duplicate handles and reference handles

---

## More
- The `SystemHandleEntry` struct layout matters — each entry is 24 bytes on 64-bit Windows: `u32 owner_pid`, `u8 object_type_number`, `u8 flags`, `u16 handle`, pointer-sized `object`, `u32 granted_access`, `u32 padding`
- Handle values in the table are stored as `u16` but used as pointer-sized values when passed to APIs
- `NtQuerySystemInformation` is undocumented — it lives in `ntdll.dll` and must be called via FFI
- Always close your own duplicated handles after comparison or they leak
- `CompareObjectHandles` requires Windows 10 1607+ — it's the correct way to check kernel object identity
- The process snapshot and handle table are point-in-time snapshots — there are inherent race windows, which is fine for this use case
