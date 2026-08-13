# [PLATFORM_NAME] Compute & Platform Environment Guidelines (Tier 2)

This file describes the default compute environment, physical resource limits, storage filesystem layout, and centralized assets for the [PLATFORM_NAME] platform. All agents must operate within these infrastructure boundaries to ensure optimal performance, resource packing, and cost-control.

---

## 1. Compute Node Boundaries

### Shared/Login Nodes vs. Compute Nodes
- **Shared/Head Nodes (Interactive/Login)**: Shared login systems are strictly for read-only research, browsing layouts, small greps, editing configurations, and git operations. **No heavy processing, compiling, pipeline runs, or long scripts may be run here**, as they can degrade the shell experience for other users.
- **Compute Nodes**: All pipeline execution, testing, large search operations, resource-intensive compilations, and model training **must** be submitted to compute nodes via the local scheduler.

---

## 2. Resource Scheduler: [SCHEDULER_TYPE]

All compute jobs must carry explicit resource requests. Jobs without specific limits may be auto-rejected or incorrectly scheduled.

### Default Allocation Syntax
When submitting a script or wrapping a command, always include the following resource flags as appropriate:
```bash
# [SCHEDULER_TYPE] submission example:
[JOB_SUBMIT_CMD] \
  --account=[DEFAULT_ACCOUNT] \
  --partition=[DEFAULT_PARTITION] \
  --cpus-per-task=[CPU_COUNT] \
  --mem=[MEMORY_LIMIT] \
  --time=[TIME_LIMIT] \
  [ADDITIONAL_SCHEDULER_FLAGS]
```

### Partition Allocations
| Partition Name       | Use Cases                                                 | Max Wall-Time |
|----------------------|-----------------------------------------------------------|---------------|
| `[TEST_PARTITION]`   | Quick pre-flight tests, smoke tests, tiny script trials   | `[TEST_TIME]` |
| `[CORE_PARTITION]`   | Production execution, batch processing, standard runs    | `[CORE_TIME]` |
| `[SPECIAL_PARTITION]`| Specialized runs (e.g. GPU, high-memory, multi-node)      | `[SPEC_TIME]` |

---

## 3. Filesystem Layout & Storage Quotas

### Home Directory ($HOME) limitations
- **Quota**: `$HOME` has a highly restricted storage quota of `[HOME_QUOTA_GB]` and `[HOME_INODE_LIMIT]` file counts.
- **Critical Mandate**: Never run large pipeline outputs, build databases, or save deep-learning weights in `$HOME`. Filling `$HOME` will break active developer terminal sessions and corrupt bash histories.
- **Cache Redirection**: Redirect all high-file-count package managers (pip, NPM, Conda, HuggingFace) and temp folders off `$HOME` into your designated persistent directories:
  ```bash
  export TMPDIR="[TEMP_DIR_DEFAULT]"
  export XDG_CACHE_HOME="[CACHE_DIR_DEFAULT]"
  ```

### Canonical Storage Locations
- **Scratch Space**: `[SCRATCH_DIR_DEFAULT]` (High-speed, temporary scratch data).
- **Persistent Storage**: `[PERSISTENT_DIR_DEFAULT]` (Primary workspace for active projects and databases).

---

## 4. Shared Centralized Resources

Before downloading reference genomes, large models, or installer scripts, verify if they already exist in the centralized read-only directory:
- **Central Resources Root**: `[SHARED_RESOURCES_PATH]`
- **Central Annotations/Reference**: `[SHARED_ANNOTATIONS_PATH]`
- **Prebuilt Container Registry**: `[SHARED_CONTAINERS_PATH]`

---

## 5. Repository Mirrors & Network Proxies

This infrastructure sits behind an enterprise firewall. Standard external repositories may be blocked. Always use local mirror proxies:
- **Package Manager Proxy**: Configured via `[INTERNAL_PROXY_URL]`
- **Container Registry Mirror**: Configured via `[CONTAINER_MIRROR_URL]`
