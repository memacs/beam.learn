# 

# ErlSpawnOpts

```
/*

 * Possible flags for the flags field in ErlSpawnOpts below.
 */

#define SPO_LINK 1
#define SPO_USE_ARGS 2
#define SPO_MONITOR 4
#define SPO_SYSTEM_PROC 8
#define SPO_OFF_HEAP_MSGQ 16
#define SPO_ON_HEAP_MSGQ 32

extern int ERTS_WRITE_UNLIKELY(erts_default_spo_flags);

/*
 * The following struct contains options for a process to be spawned.
 */
typedef struct {
    int flags;
    int error_code;     /* Error code returned from create_process(). */
    Eterm mref;         /* Monitor ref returned (if SPO_MONITOR was given). */

    /*
     * The following items are only initialized if the SPO_USE_ARGS flag is set.
     */
    Uint min_heap_size;     /* Minimum heap size (must be a valued returned
                 * from next_heap_size()).  */
    Uint min_vheap_size;    /* Minimum virtual heap size  */
    int priority;       /* Priority for process. */
    Uint16 max_gen_gcs;     /* Maximum number of gen GCs before fullsweep. */
    Uint max_heap_size;         /* Maximum heap size in words */
    Uint max_heap_flags;        /* Maximum heap flags (kill | log) */
    int scheduler;
} ErlSpawnOpts;
```

## SPO_LINK

参见
[spawn_opt_option](http://erlang.org/doc/man/erlang.html#spawn_opt-5)
[ process_flag(message_queue_data, MQD)](http://erlang.org/doc/man/erlang.html#process_flag_message_queue_data)
```
Options = [spawn_opt_option()]
priority_level() = low | normal | high | max
max_heap_size() =
    integer() >= 0 |
    #{size => integer() >= 0,
      kill => boolean(),
      error_logger => boolean()}
message_queue_data() = off_heap | on_heap
spawn_opt_option() =
    link | monitor |
    {priority, Level :: priority_level()} |
    {fullsweep_after, Number :: integer() >= 0} |
    {min_heap_size, Size :: integer() >= 0} |
    {min_bin_vheap_size, VSize :: integer() >= 0} |
    {max_heap_size, Size :: max_heap_size()} |
    {message_queue_data, MQD :: message_queue_data()}
    
message_queue_data() = off_heap | on_heap

process_flag(Flag :: message_queue_data, MQD) -> OldMQD
OTP 19.0
Types
MQD = OldMQD = message_queue_data()
message_queue_data() = off_heap | on_heap
This flag determines how messages in the message queue are stored, as follows:

off_heap
All messages in the message queue will be stored outside of the process heap. This implies that no messages in the message queue will be part of a garbage collection of the process.

on_heap
All messages in the message queue will eventually be placed on heap. They can however temporarily be stored off heap. This is how messages always have been stored up until ERTS 8.0.

The default message_queue_data process flag is determined by command-line argument +hmqd in erl(1).

If the process potentially can get many messages in its queue, you are advised to set the flag to off_heap. This because a garbage collection with many messages placed on the heap can become extremely expensive and the process can consume large amounts of memory. Performance of the actual message passing is however generally better when not using flag off_heap.

When changing this flag messages will be moved. This work has been initiated but not completed when this function call returns.

Returns the old value of the flag.
```
# off head

```

typedef struct erl_off_heap {
    struct erl_off_heap_header* first;
    Uint64 overhead;     /* Administrative overhead (used to force GC). */
} ErlOffHeap;
```

进程控制块中有一个 ErlOffHeap 数据，这个数据是进程所有 off-heap（即“堆外”）数据的链表的头，first 指向第一个 ProcBin。目前 Erlang 进程只有 refc binary 这一种 off-heap 数据，不过以后有可能会有更多类型的 off-heap 数据类型，因为 off-heap 这种名称看上去很 general。ErlOffHeap 数据还有一个字段 overhead，这个字段记录了所有 off-heap 数据大小的总和，这个值会用于垃圾回收，如果这个 overhead 超过了进程的 vheap（虚拟堆）限制，则会进行垃圾回收。vheap 也是一个 general 的概念，尽管目前仅用于 binary。有关 vheap 的初始化、增长和对垃圾回收的控制，请参阅这篇博文[注5]。另外提一下，在 Erlang 虚拟机的代码中，有一个宏 MSO，经常能看到类似 MSO(c_p).overhead 和 MSO(c_p).first 这样的调用，MSO(c_p) 宏实际上获得的就是当前进程 c_p 的 ErlOffHeap 数据。估计 MSO 是 memory shared object 的简写吧，共享内存对象和 off-heap 对象应该是同一个意思

# Resources

* [Inside the Erlang VM](https://erlang.org/euc/08/euc_smp.pdf)
* [erlang-scheduler-details](https://hamidreza-s.github.io/erlang/scheduling/real-time/preemptive/migration/2016/02/09/erlang-scheduler-details.html)
* [Erlang Scheduler: What Does It Do](http://erlang.org/pipermail/erlang-questions/2001-April/003132.html)
* [erlang_eterm_implementation_5_binary](https://www.cnblogs.com/zhengsyao/p/erlang_eterm_implementation_5_binary.html)
