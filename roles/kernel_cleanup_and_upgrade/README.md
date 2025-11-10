# - This role standardizes and prunes kernels on RHEL/Oracle (dnf-based) hosts.
# - Intended for controlled maintenance windows, NOT blind production rollouts.
# - Behavior (from vars in this include):
#     * kernel_stream="{{ kernel_stream | default('both') }}":
#         - "rhck" => manage only RHCK kernels
#         - "uek"  => manage only UEK (and optionally remove RHCK safely)
#         - "both" => keep minimal set per stream (newest + running)
#     * installonly_limit_value={{ installonly_limit_value | default(2) }}:
#         - Enforced via /etc/dnf/dnf.conf to cap retained kernels.
#     * boot_min_free_mb={{ boot_min_free_mb | default(360) }}:
#         - Require at least this much free space in /boot before upgrade.
# - What this role does (high level):
#     * Disables dnf-automatic timers/services to avoid background upgrades.
#     * Cleans DNF metadata/caches and stale /boot temp files.
#     * Previews and then prunes old installonly kernels (safe, never removes running).
#     * Optionally removes RHCK when kernel_stream=="uek" (but never running kernel).
#     * Manages rescue images to reclaim /boot space.
#     * Kicks off 'dnf upgrade -y --refresh' inside tmux session 'anticimex'
#     * attach to the session using tmux attach -t anticimex
#       so upgrades survive SSH disconnects.
#     * Waits for dnf/yum locks to clear, then does post-upgrade kernel pruning.
# - Safety:
#     * Aborts on Ubuntu/Debian.
#     * Aborts if VERSION_ID already 9.6 (per guardrail).
#     * Ensures at least one kernel per active stream remains.
# - Recommended:
#     * Run on TEST/UAT first.
#     * Document your chosen kernel_stream per environment.
