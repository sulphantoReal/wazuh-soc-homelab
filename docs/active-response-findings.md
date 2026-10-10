# Active-Response Investigation

## Goal
Configure Wazuh active-response to automatically block (via `iptables`) any
source IP that triggers the brute-force correlation rule (5763).

## Configuration
Added a `firewall-drop` command bound to rule 5763 with a 600-second timeout
in `ossec.conf`. Config validated, `wazuh-manager` restarted cleanly, and
`wazuh-execd` confirmed connection to the active-response queue at startup.

## Observed behavior
On live triggers, `active-responses.log` showed the script starting
(`firewall-drop: Starting`) but no corresponding `iptables` rule was ever
inserted, and no error was logged.

## Root cause
Manually piping a correctly-formed active-response JSON payload into the
script reproduced a deeper issue: the script internally issues a `check_keys`
request back to `execd` over stdin, and that stdin read hung
(`Cannot read input from stdin`). In a real Wazuh dispatch this handshake is
normally handled transparently by `execd` — the stall observed here points to
the handshake not completing in this all-in-one, single-manager deployment
with no registered agent keys for the attacking host.

## Outcome
Documented as a known limitation rather than resolved. Detection (5763)
worked reliably and consistently across every test run — only the automated
*response* leg was incomplete. A production fix would likely involve running
the active-response on the agent side (VM2) instead of the manager, or
auditing `execd`'s key-store handling for standalone managers.
