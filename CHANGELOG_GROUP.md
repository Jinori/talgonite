# Group system – patch notes summary

Summary of group panel and protocol changes.

---

## Group panel UX

- **Leader at position 1**  
  The local player is always shown first in the group list (position 1). That row shows **Leave**; other members show **Kick** only when you are the group leader.

- **Leave vs Kick**  
  - **Leave** – Shown for your own row. Sends ToggleGroup (opcode `0x2F`). Available to everyone (leader or not).  
  - **Kick** – Shown only for other members **when you are the group leader**. Non-leaders do not see Kick on other members.

- **Leader from server**  
  The SelfProfile `group_string` marks the group leader with an asterisk (e.g. `* Tedders`). The UI uses this so that only the leader sees Kick on other members; if you joined via invite, you only see Leave for yourself.

- **Member count in header**  
  The panel shows e.g. `2 Group members` under the “Accepting Invites” button instead of a separate “Total N” row. The backend filters out the “Group members” and “Total N” lines from the member list.

- **No spouse in group list**  
  When not in a group, “Spouse: Name” no longer appears in the group panel; it’s filtered out so the list only shows actual group members.

- **Panel refresh on open**  
  Opening the Group panel requests a fresh SelfProfile so the member list is up to date without opening your profile first.

- **Refresh on group messages**  
  When the server sends system messages such as “Group disbanded.”, “X is joining/leaving this group.”, or “X has taken command of the group.”, the client requests SelfProfile so the group list stays in sync.

---

## Protocol (client packets)

- **Invite** – Opcode `0x2E` (46), subtype **02** (Request) + target name.  
- **Accept invite** – Opcode `0x2E`, subtype **03** (Forced) + inviter name.  
- **Kick member** – Opcode `0x2E`, subtype **02** (Request) + member name.  
- **Leave group** – Opcode **`0x2F`** (47, ToggleGroup) only; no payload. Used for both “accepting invites” toggle and leaving the group (leader or not).

---

## Technical notes

- **GroupState** stores members as `(display_name, is_leader_from_server)` so the UI can hide Kick when the local player is not the server-designated leader.  
- **GameState** has `is-group-leader`; the group panel shows the Kick button for other members only when this is true.  
- Slint callbacks: `leave-group()` for Leave, `kick-group-member(name)` for Kick; separate packets as above.
