fIt can be configured at /proc/sys:

vm.overcommit_memory:
        This parameter controls the kernel's memory overcommit behavior. It has three possible values:
            0: Heuristic overcommit handling. The kernel tries to estimate the amount of memory available and makes decisions based on that estimation.
            1: Always overcommit. The kernel allows all memory allocations, regardless of the current memory usage, which can increase the risk of invoking the OOM killer.
            2: Don't overcommit. The kernel strictly checks for available memory and can deny requests for memory allocation if they exceed the available memory.
        You can set this value with: sysctl vm.overcommit_memory=1 (replace 1 with your desired setting).

vm.overcommit_ratio:
        This parameter defines the percentage of physical RAM that is overcommitted when vm.overcommit_memory is set to 2. Its default value is usually 50.
        Set it with: sysctl vm.overcommit_ratio=50.

vm.oom_kill_allocating_task:
        By default, the OOM killer selects a process to kill based on a set of heuristics, which may not always be the process that caused the memory shortage.
        Setting vm.oom_kill_allocating_task to 1 makes the OOM killer target the specific task that triggered the out-of-memory condition.
        Set it with: sysctl vm.oom_kill_allocating_task=1.

vm.panic_on_oom:
        This parameter determines the kernel's reaction to an OOM condition. By setting it to 1, the kernel will panic when an OOM condition occurs, which can be useful for ensuring a quick reboot in critical systems.
        Set it with: sysctl vm.panic_on_oom=1.

vm.min_free_kbytes:
        This parameter sets the minimum amount of memory that is kept free across the system for emergency allocations.
        Set it with: sysctl vm.min_free_kbytes=value.

Persisting Changes Across Reboots:
        To make these changes permanent (persist after reboot), you need to add them to the /etc/sysctl.conf file or a dedicated file in /etc/sysctl.d/.

Swappiness:
