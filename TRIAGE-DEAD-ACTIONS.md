# Unreachable reducer actions

58 of 252 reducer cases are never dispatched.

A `case` with no dispatcher is a feature with an engine and no door. Most
appear in app.js exactly once - as the case label - and are referenced only
from tests, which means **the suite certifies them as working**.

Unreachable is not the same as missing: some are superseded by a path that
does the same job.

## Two lessons this table has already cost

**Check for a sibling path before choosing a bucket.** GRAPPLE_ATTEMPT and
SHOVE_ATTEMPT were bucketed "unfinished - engine, no door". The Actions
drawer reached both through CONTEST_RESOLVE. Building the recommended door
would have made a third path to grappling.

**Before deleting a superseded case, diff it against the survivor CLAUSE BY
CLAUSE, not test by test.** The deleted grapple and shove paths had four
advantages over the live one. Retargeting their tests found two, because
only two had tests: the live path also applied conditions as a TOGGLE (so a
second application removed them) and ignored a refused economy spend (so a
creature that had used its action could grapple for free). Both shipped, and
both were found by a reader, not by the port. A green suite after a
retarget means the clauses that had tests survived. It says nothing about
the ones that did not.

Regenerate with `node tools/list-dead-actions.js`.

| action | in app.js | referenced by tests | bucket |
|---|---|---|---|
| `CHAT_CLEAR` | 1 | - | **unfinished** - nothing in the UI offers to clear the log |
| `CORRECTION_UNDO` | 1 | turn-corrections.test.js | untriaged (tests certify it) |
| `ECONOMY_ATTACK` | 1 | action-economy.test.js, scoped-actions.test.js, weapon-effects.test.js | **superseded-pending** - equivalent to commitAction; its tests still dispatch it |
| `ECONOMY_CAST_SPELL` | 1 | casting-time.test.js | untriaged (tests certify it) |
| `ECONOMY_RESET` | 1 | - | **superseded** - INIT_ADVANCE refreshes the incoming creature inline - verified |
| `ECONOMY_TAKE_ACTION` | 1 | action-economy.test.js, standard-actions.test.js | untriaged (tests certify it) |
| `ECONOMY_TAKE_ACTION_LEGACY` | 1 | - | untriaged (no tests) |
| `ECONOMY_TRIGGER_READY` | 1 | action-economy.test.js, action-service.test.js | untriaged (tests certify it) |
| `ECONOMY_USE_SLOT` | 1 | action-economy.test.js | untriaged (tests certify it) |
| `EFFECT_CLEAR_CASTER` | 1 | effect-scheduler.test.js | **superseded** - endConcentration clears a caster's areas inline - verified |
| `EFFECT_CLEAR_FIRES` | 1 | - | untriaged (no tests) |
| `EFFECT_MOVE_AREA` | 1 | control-spells.test.js | **unfinished** - moves a movable spell area; no UI reads scheduledEffects |
| `EFFECT_UNSCHEDULE` | 1 | - | untriaged (no tests) |
| `ENTITY_REPAIR` | 1 | hp-invariants.test.js | **unfinished** - no control exists yet |
| `ENTITY_RESURRECT` | 1 | hp-invariants.test.js | **unfinished** - no control exists yet |
| `ENTITY_TEMP_HP_GRANT` | 1 | temp-hp.test.js | **internal** - granted by spell effects |
| `ENTITY_TEMP_HP_SET` | 1 | temp-hp.test.js | **internal** - applied by rest and spell effects |
| `EXHAUSTION_SET` | 2 | exhaustion.test.js | **shared** - falls through with EXHAUSTION_ADJUST, which is dispatched - the block is live |
| `INTENT_CANCEL` | 1 | intent-lifecycle.test.js, scoped-actions.test.js | **unfinished-needs-protocol** - client-local only; cannot withdraw from the host |
| `INTENT_CLEAR_SETTLED` | 1 | intent-lifecycle.test.js, scoped-actions.test.js | **unfinished** - prunes settled intents; nothing calls it |
| `INTENT_FORGET` | 1 | scoped-actions.test.js | **unfinished** - drops one intent; nothing calls it |
| `MAP_PATCH` | 1 | - | **superseded** - MAP_UPSERT is dispatched and produces the same map - verified |
| `MEDICINE_STABILIZE` | 1 | followups.test.js, stabilize-nonlethal.test.js | untriaged (tests certify it) |
| `MODIFIER_CLEAR_SOURCE` | 1 | roll-modifiers.test.js | untriaged (tests certify it) |
| `MODIFIER_CONSUME` | 1 | - | untriaged (no tests) |
| `MODIFIER_REMOVE` | 1 | - | untriaged (no tests) |
| `MONSTER_ABILITY` | 1 | monsters.test.js | untriaged (tests certify it) |
| `MOUNT_CLEAR` | 1 | movement-transaction.test.js, positioning.test.js | untriaged (tests certify it) |
| `MOUNT_SET` | 1 | movement-transaction.test.js, positioning.test.js | untriaged (tests certify it) |
| `OPPORTUNITY_PENDING` | 1 | live-combat.test.js | untriaged (tests certify it) |
| `REMINDER_MOVE` | 1 | - | untriaged (no tests) |
| `SPELL_AID` | 1 | healing-spells.test.js | **needs-diff** - generic SPELL_CAST exists; max-HP bonus unchecked |
| `SPELL_APPLY_MODIFIERS` | 1 | named-spells.test.js | **needs-diff** - generic SPELL_CAST exists |
| `SPELL_CONSUME_MATERIAL` | 1 | spell-requirements.test.js | **needs-diff** - commitSpellCast spends materials - compare |
| `SPELL_DELAYED_BLAST_DETONATE` | 1 | remaining-spells.test.js | **needs-diff** - generic SPELL_CAST exists |
| `SPELL_DELAYED_BLAST_GROW` | 1 | remaining-spells.test.js | **needs-diff** - generic SPELL_CAST exists |
| `SPELL_HEAL` | 1 | healing-spells.test.js | **needs-diff** - generic SPELL_CAST exists |
| `SPELL_MAGE_ARMOR` | 1 | followups.test.js, timed-effects.test.js | **needs-diff** - generic SPELL_CAST exists; AC rule unchecked |
| `SPELL_MAGIC_MISSILE` | 1 | remaining-spells.test.js | **needs-diff** - dart assignment is unreachable; whether the generic path damages correctly is UNVERIFIED |
| `SPELL_SLEEP` | 1 | sleep-spell.test.js | **needs-diff** - generic SPELL_CAST exists; HP-order rule unchecked |
| `SPELL_VAMPIRIC_TOUCH` | 1 | remaining-spells.test.js | **needs-diff** - generic SPELL_CAST exists; half-healing unchecked |
| `STAND_UP` | 1 | followups.test.js | untriaged (tests certify it) |
| `SURPRISE_CLEAR` | 1 | - | untriaged (no tests) |
| `SURPRISE_SET` | 1 | action-economy.test.js | untriaged (tests certify it) |
| `TOKEN_FORCE_MOVE` | 1 | effect-scheduler.test.js, positioning.test.js, scheduler-integration.test.js | untriaged (tests certify it) |
| `TOKEN_GROUP_SET_MEMBERS` | 1 | - | untriaged (no tests) |
| `TOKEN_TELEPORT` | 1 | effect-scheduler.test.js, positioning.test.js, scheduler-integration.test.js | untriaged (tests certify it) |
| `UI_CHAT_RECOUNT` | 1 | dock-panels.test.js, player-ui-state.test.js | untriaged (tests certify it) |
| `UI_DISCARD_DRAFT` | 1 | - | untriaged (no tests) |
| `UI_DRAFT_REQUEST` | 1 | player-ui-state.test.js | untriaged (tests certify it) |
| `UI_HOVER` | 1 | player-ui-state.test.js | untriaged (tests certify it) |
| `UI_RESET` | 1 | - | untriaged (no tests) |
| `UI_SET_FOCUS` | 1 | - | untriaged (no tests) |
| `UI_SET_SHEET_MODE` | 1 | player-ui-state.test.js | untriaged (tests certify it) |
| `UI_SUBMIT_DRAFT` | 1 | player-ui-state.test.js | untriaged (tests certify it) |
| `UI_UPDATE_MOVEMENT` | 1 | player-ui-state.test.js | untriaged (tests certify it) |
| `UI_WORKFLOW_CLEAR_RESULT` | 1 | - | untriaged (no tests) |
| `WAKE_CREATURE` | 1 | sleep-spell.test.js | untriaged (tests certify it) |

## Live actions no test dispatches

87 of 194 reachable actions are never
dispatched by any test. They may be covered through a helper - which is how a
reducer can silently drop a field while the suite stays green.

- `ACTION_COMMIT`
- `AREA_RESOLVE_CLEAR`
- `ATTACK_SET`
- `BLOCK_ZONE_DELETE`
- `BLOCK_ZONE_UPSERT`
- `BOOK_READ_RESULT`
- `CHAT_ADD`
- `CHECK_REQUEST_ADD`
- `CHECK_REQUEST_REMOVE`
- `CLAIM_FAMILIAR`
- `CLAIM_MIGRATE`
- `CLAIM_PC`
- `CLAIM_SPECTATOR`
- `CONCENTRATION_END`
- `DEATH_SAVE_ELIGIBILITY_SET`
- `DICE_LOG_CLEAR`
- `DM_KICK_PEER`
- `DM_UNCLAIM_FAMILIAR`
- `DM_UNCLAIM_PC`
- `DRAWING_DELETE`
- `DRAWING_UPSERT`
- `ECONOMY_TOGGLE`
- `ENTITY_REORDER`
- `ENTITY_UPSERT`
- `FORCED_VIEW`
- `FORCED_VIEW_PEER_CLEAR_ALL`
- `FORCED_VIEW_PEER_SET`
- `GRANT_PC_CONTROL`
- `GRAPPLE_END`
- `GROUP_CHECK_END`
- `GROUP_CHECK_RESULT`
- `GROUP_CHECK_SET_ADV`
- `GROUP_CHECK_START`
- `HAZARD_CLEAR_MAP`
- `HAZARD_DELETE`
- `HAZARD_PENDING_CLEAR`
- `HAZARD_PENDING_REMOVE`
- `HAZARD_UPSERT`
- `HYDRATE`
- `INIT_ADD`
- `MAP_IMAGE_RECEIVED`
- `MAP_SCALE_SET`
- `MAP_SWITCH`
- `MAP_VIEWPORT`
- `MOVEMENT_RESET`
- `PRESET_DELETE`
- `PRESET_SAVE`
- `RANGE_HIGHLIGHT_CLEAR`
- `RANGE_HIGHLIGHT_SET`
- `REMINDER_DELETE`
- `REMINDER_UPSERT`
- `REQUEST_ADD`
- `REQUEST_REMOVE`
- `REVOKE_PC_CONTROL`
- `SET_LOCK_OFF_TURN`
- `SET_PLAYER_NAME`
- `SET_PLAYER_THEME`
- `SET_SICKNESS`
- `SOUND_DEREGISTER`
- `SOUND_EVENT`
- `SOUND_REGISTER`
- `SPELL_EFFECT_ADD`
- `SPELL_EFFECT_END`
- `SPELL_REQUEST_ADD`
- `SPELL_REQUEST_REMOVE`
- `SPELL_SLOT_RESTORE`
- `SPELL_TEMPLATE_CLEAR`
- `SPELL_TEMPLATE_SET`
- `SUMMONABLE_TOGGLE`
- `SUMMON_DISMISS`
- `TIME_OF_DAY_SET`
- `TOKEN_GROUP_DELETE`
- `TOKEN_GROUP_SET_VISIBLE`
- `TOKEN_GROUP_UPDATE`
- `TOKEN_MOVE_EPHEMERAL`
- `TOKEN_MOVE_MANY`
- `TOKEN_PLACE`
- `TOKEN_PRESET_DELETE`
- `TOKEN_PRESET_UPSERT`
- `TOKEN_REMOVE`
- `TOKEN_REVEAL_ALL_ON_MAP`
- `TOKEN_SCALE`
- `TOKEN_VISIBILITY`
- `UI_CANCEL_TARGETING`
- `UI_SET_SHEET_TAB`
- `UNCLAIM_FAMILIAR`
- `UNCLAIM_PC`


## Progress

- unfinished: 6
- untriaged: 34
- superseded-pending: 1
- superseded: 3
- internal: 2
- shared: 1
- unfinished-needs-protocol: 1
- needs-diff: 10
