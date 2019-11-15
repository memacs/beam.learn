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

参见[spawn_opt_option](http://erlang.org/doc/man/erlang.html#spawn_opt-5)
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
```
