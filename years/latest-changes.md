# 2020

## 6/28/2020

**Akkadius**

* Fixed an issue where when a client first enters a zone; an NPC may not be aware of their presence immediately
* Fixed a UCS issue where a crash or another UCS connection would delete connection pointers; this should keep UCS connected more reliably
* Lowered the rate in which animation packets are throttled to 500ms from 1000ms; this should keep faster animations from being gated from being sent

**Kinglykrab**

* Fix $npc-&gt;RecalculateSkills\(\) in Perl.
* Add $client-&gt;MoveZone\(zone\_short\_name\) to Perl.
* Add client:MoveZone\(zone\_short\_name\) to Lua.
* Add $client-&gt;MoveZoneGroup\(zone\_short\_name\) to Perl.
* Add client:MoveZoneGroup\(zone\_short\_name\) to Lua.
* Add $client-&gt;MoveZoneRaid\(zone\_short\_name\) to Perl.
* Add client:MoveZoneRaid\(zone\_short\_name\) to Lua.
* Add quest::crosszoneassigntaskbycharid\(character\_id, task\_id, enforce\_level\_requirement\) to Perl.
* Add eq.cross\_zone\_assign\_task\_by\_char\_id\(character\_id, task\_id, enforce\_level\_requirement\) to Lua.
* Add quest::crosszoneassigntaskbygroupid\(group\_id, task\_id, enforce\_level\_requirement\) to Perl.
* Add eq.cross\_zone\_assign\_task\_by\_group\_id\(group\_id, task\_id, enforce\_level\_requirement\) to Lua.
* Add quest::crosszoneassigntaskbyraidid\(raid\_id, task\_id, enforce\_level\_requirement\) to Perl.
* Add eq.cross\_zone\_assign\_task\_by\_raid\_id\(raid\_id, task\_id, enforce\_level\_requirement\) to Lua.
* Add quest::crosszoneassigntaskbyguildid\(guild\_id, task\_id, enforce\_level\_requirement\) to Perl.
* Add eq.cross\_zone\_assign\_task\_by\_guild\_id\(guild\_id, task\_id, enforce\_level\_requirement\) to Lua.
* Add $client-&gt;GetDisciplineTimer\(timer\_id\) to Perl.
* Add client:GetDisciplineTimer\(timer\_id\) to Lua.
* Add $client-&gt;ResetDisciplineTimer\(timer\_id\) to Perl.
* Add client:ResetDisciplineTimer\(timer\_id\) to Lua.

## 6/23/2020

**Noudess**

Updated \#grid show command to use alphabetic names so names display properly \(numerics were getting stripped\) and stacked the names of nodes that are in the same \#loc.

Added code to make sure that mobs that call for help, complete that activity before starting to move toward target.  As written \(previously\) a mob could be out of earshot of his neighbors because movement started immediately.  It was a race condition that nearly always resulted in mobs within the attacked mobs assist radius not hearing the call.

**Kinglykrab**

Added command \#findrace \[name\|id\].

![](../.gitbook/assets/image%20%283%29.png)

Added zone ID to \#findzone.

![](../.gitbook/assets/image%20%284%29.png)

Added eq.zone\(short\_name\) to Lua.

Added eq.zone\_group\(short\_name\) to Lua.

Added eq.zone\_raid\(short\_name\) to Lua.

Added quest::zonegroup\(short\_name\) to Perl.

Added quest::zoneraid\(short\_name\) to Perl.

## 5/26/2020

**Noudess**

SQL to load correct mods into faction\_list\_mod for Drakkin and Guktan races as well as Agnostic deity mods. Only to be used on databases converted to using the client faction ids.SQL found in optional SQL area as: utils/sql/git/optional/drakkin\_guktan\_faction\_data.sql



## 5/11/2020

**Kinglykrab**

### Added GetNPCBySpawnID\(\) to Perl/Lua.

{% tabs %}
{% tab title="Perl" %}
```perl
$entity_list->GetNPCBySpawnID(spawn_id);
```
{% endtab %}

{% tab title="Lua" %}
```lua
eq.get_entity_list():GetNPCBySpawnID(spawn_id);
```
{% endtab %}
{% endtabs %}

### Add several cross zone methods to Perl/Lua.

{% tabs %}
{% tab title="Perl" %}
```perl
quest::crosszonemessageplayerbygroupid(type, group_id, message);
quest::crosszonemessageplayerbyraidid(type, raid_id, message);
quest::crosszonemessageplayerbyguildid(type, guild_id, message);
quest::crosszonesignalclientbygroupid(group_id, value);
quest::crosszonesignalclientbyraidid(raid_id, value);
quest::crosszonesignalclientbyguildid(guild_id, value);
quest::crosszonesetentityvariablebygroupid(group_id, value);
quest::crosszonesetentityvariablebyraidid(raid_id, value);
quest::crosszonesetentityvariablebyguildid(guild_id, value);
```
{% endtab %}

{% tab title="Lua" %}
```lua
eq.cross_zone_message_player_by_group_id(type, group_id, message);
eq.cross_zone_message_player_by_raid_id(type, raid_id, message);
eq.cross_zone_message_player_by_guild_id(type, guild_id, message);
eq.cross_zone_signal_client_by_group_id(group_id, value);
eq.cross_zone_signal_client_by_raid_id(group_id, value);
eq.cross_zone_signal_client_by_guild_id(guild_id, value);
eq.cross_zone_set_entity_variable_by_group_id(group_id, value);
eq.cross_zone_set_entity_variable_by_raid_id(raid_id, value);
eq.cross_zone_set_entity_variable_by_guild_id(guild_id, value);
```
{% endtab %}
{% endtabs %}

## 4/14/2020

**Akkadius**

### UCS / Raid / Zone Fixes

**Raid::GetRaidByClient**

On PEQ we noticed via zone with high CPU footprint spending tons of time in in function `Raid::GetRaidByClient` where we excessively loop through all raids in zone, all members in the raid to return back a simple raid pointer. Now, we cache the pointer lookup instead of running the entire loop constantly. For perspective, with 100 toons in a zone all in a raid, you will easily see 4k/s calls to this function which adds up on a CPU profile \(nuts\)

**CPU Profile** [https://gist.github.com/Akkadius/a5c55654d4b729697964f938c824030f](https://gist.github.com/Akkadius/a5c55654d4b729697964f938c824030f)  
**Call Measurement Example**

```text
eqemu@ce0931611614:~$ tail -f ~/server/logs/**/*.log | grep "returning cached raid" | pv -lr 2>&1 >/dev/null
[4.04k/s]
```

**UCS**

We also noticed UCS sucking up a lot of CPU with 1,500 players online

**CPU Profile** [https://gist.github.com/Akkadius/14efe565caf468cc892293fd95cc0377](https://gist.github.com/Akkadius/14efe565caf468cc892293fd95cc0377)

* Converted to a less aggressive loop frequency 31.25 FPS and converted to using UV library
* No longer load characters into UCS context that are soft deleted
* Fixed UCS bug where it would not reconnect properly after a crash / improper shutdown
* Fixed UCS bug where it was not registering a new connection properly, and sending a "Not Available" to all zones right after it is in fact "Available"
* Added Logging in zone that clearly says whether or not UCS is online or not
* Properly removing UCS connected clients even if the /q /ex connections resolving by sending keepalive packets [\#735](https://github.com/EQEmu/Server/issues/735) this prevents UCS from looping over ghosted clients making unnecessary database calls and sending improper connected client counts
* Removed tons of verbose LogInfo logging and replaced the calls for LogDebug as it was writing to disk excessively for mostly debugging related information

**Zone**

* We are now properly shutting down zone processes by killing the UV loop instead of doing a hard process exit, allowing the zone to shut everything else down gracefully

## 4/9/2020

**Akkadius**

### Implement SendToGuildHall and Improve Instance ID Cycling

Instead of purging immediately expired instances every 7.5 minutes, we will purge expired instances from the database table after they've been completely expired for an entire day every 7.5 minutes. The reason for this is that the code will still determine that an instance is expired when it needs to; but in order to not re-use the same instance ID not long after it had been freed while a zone is still online with the same instance ID, we're simply going to wait a grace period of 1 day to purge those entries as it will not harm anything

Client::SendToGuildHall\(\) will send a client to a Guild Hall instance for 90 days based on a configurable rule. This will send the player to a Guild Hall instance regardless of whether they are in a Guild or not and it is up to the discretion of the server operator to check for guild id before making the call. If you are not guilded, you share an instance with other non-guilded players

We pull instance remaining time out of the database once on zone bootup because the timer class is returning the value in milliseconds which causes the values to overflow and improperly show remaining instance time for instances that are open for long period of time

Created new rule category `Instances`

```text
RULE_INT(Instances, ReservedInstances, 30, "Will reserve this many instance ids for globals... probably not a good idea to change this while a server is running")
RULE_BOOL(Instances, RecycleInstanceIds, true, "Will recycle free instance ids instead of gradually running out at 32k")
RULE_INT(Instances, GuildHallExpirationDays, 90, "Amount of days before a Guild Hall instance expires")
```

This exports Lua / Perl method `client->SendToGuildHall`

## 4/5/2020

**Kinglykrab**

* Added getcharidbyname\(name\) to Perl/Lua.

{% tabs %}
{% tab title="Perl" %}
```perl
quest::getcharidbyname(name);
```
{% endtab %}

{% tab title="Lua" %}
```lua
eq.get_char_id_by_name(name);
```
{% endtab %}
{% endtabs %}

* Added getcharnamebyid\(char\_id\) to Perl/Lua.

{% tabs %}
{% tab title="Perl" %}
```perl
quest::getcharnamebyid(char_id);
```
{% endtab %}

{% tab title="Lua" %}
```lua
eq.get_char_name_by_id(char_id);
```
{% endtab %}
{% endtabs %}

## 3/31/2020

**Kinglykrab**

* Added getitemname\(item\_id\) to Perl/Lua.

{% tabs %}
{% tab title="Perl" %}
```perl
quest::getitemname(item_id);
```
{% endtab %}

{% tab title="Lua" %}
```lua
eq.get_item_name(item_id);
```
{% endtab %}
{% endtabs %}

* Added getspellname\(spell\_id\) to Perl/Lua.

{% tabs %}
{% tab title="Perl" %}
```perl
quest::getspellname(spell_id);
```
{% endtab %}

{% tab title="Lua" %}
```lua
eq.get_spell_name(item_id);
```
{% endtab %}
{% endtabs %}

* Added gettaskname\(task\_id\) to Perl/Lua.

{% tabs %}
{% tab title="Perl" %}
```perl
quest::gettaskname(task_id);
```
{% endtab %}

{% tab title="Lua" %}
```lua
eq.get_task_name(task_id);
```
{% endtab %}
{% endtabs %}

* Added CheckInstanceByCharID\(instance\_id, char\_id\) to Perl/Lua.

{% tabs %}
{% tab title="Perl" %}
```perl
quest::CheckInstanceByCharID(instance_id, char_id);
```
{% endtab %}

{% tab title="Lua" %}
```lua
eq.check_instance_by_char_id(instance_id, char_id);
```
{% endtab %}
{% endtabs %}

## 3/5/2020

**Noudess**

* Added a new field \(always\_aggro\_foes\) to npc\_types.  If non-zero, the npc/mob will always aggro foes regardless of intelligence or level.  This was so mobs such as the int casters in Befallen, who will agro their enemies regardless of level and I didn't want to have to reduce the \_int field of INT caster NPCs to achieve the aggro.

## 3/2/2020

#### Uleat

### Monk Bot Changes

* Added a 'monk' class special attack timer to class NPC
* Monk bots will now use the ability `Tiger Claw` on a seperate timer from the other disciplines - resulting in an additional attack
* Bot usage of `Master Wu's Technique` now falls inline with current client behavior \(rules and all\)
* Added bot owner option `monkwumessage` to toggle a notification message to the bot's owner whenever `MWT` triggers \(off by default .. use `^oo monkwumessage` to enable/disable\)

**Special Note:**

By default, all classes process class attacks based on active timers. This involves checking attackability of target with every call - which is VERY expensive when done every server processing cycle since the timer is always 'ready.'

I added code to disable 'class' timers in classes that don't use them or are inactive because they don't have a skill. Class-based timers will not reactivate..but, skill-based ones will once a `CalcBotStats` call is made during the 'leveling' process. \(If the skill is not available after leveling, the timer will disable itself again.\)

Hopefully, this will help reduce bot cpu usage.

Please report any issues on our forums or in Discord - Thanks!

## 2/4/2020

#### hg

### **Auto Consent Implemented with Group / Raid / Guild Consent**

* Auto Consent client options are now supported. Toggling these options results in consenting \(or denying\) a player's current corpses.
  * These options are now also saved and restored for characters.
* The /consent and /deny commands for group, raid, and guild are now implemented. These commands are not case sensitive.
  * `/consent group` - adds current group id to all character corpses
  * `/consent raid` - adds current raid id to all character corpses
  * `/consent guild` - adds current guild id to all corpses and updates db
  * `/deny group|raid|guild` - sets the consent type id to 0 on all corpses
* Consent and deny commands are throttled to once every two seconds
* When consenting a player by name, both the corpse owner and the player being consented receive a message for every \(loaded\) zone a corpse is in.
* Corpses now store the list of consented player names. Previously clients stored a list of players they could drag corpses for.

Guild consent is persistent. The `character_corpses` table has a new `guild_consent_id`column which is updated when a player issues a guild consent command. This means guild consented corpses may be dragged by guild members after a corpse is loaded \(summoning the corpse to another zone or loading the corpse in a previously unloaded zone\)

Group and raid consent are not persistent. Corpses will lose these consents when a corpse is summoned or if the corpse is loaded by a zone after consent was issued.

## 1/15/2020

#### Uleat

### Update to EVENT\_COMMAND and EVENT\_BOT\_COMMAND

The handlers for EVENT\_COMMAND and EVENT\_BOT\_COMMAND have been updated to allow a 'no change' behavior.

Script files using these event types will perform as before without the need to move the command scripting into them .. so long as there is no defined function for them.

Once a definition is given, the old method of using EVENT\_SAY to handle them will no longer work and the new formatting must be applied.

If you have any questions, please direct them to our forums or the `#support-quest-scripts` channel in discord.

## 1/10/2020

**Akkadius**

### Character Soft Deletes

Unless new system rule **Character:SoftDeletes** \(default: true\) is disabled, characters will now no longer hard delete from all respective character tables and will only be marked as deleted via the newly created `deleted_at` column that can be found in the `character_data` table. This can make it far easier to recover accidentally deleted characters

![](../.gitbook/assets/image%20%281%29.png)

## 1/7/2020

#### Uleat

### EVENT\_COMMAND and EVENT\_BOT\_COMMAND

* EVENT\_COMMAND now processes its own code and is no longer shared with EVENT\_SAY
* EVENT\_BOT\_COMMAND was added..but, will have limited functionality until more of the Bot class is exposed in the scripting apis
* Both event types have new or updated entries on the [Events](https://eqemu.gitbook.io/quest-api/methods/events/) page
* In the template scripts, just replace the example command data with your own
* Perl: `"command_name" => [<status_required>, "command_description"]`
* Lua: `["command_name"] = {<status_required>, "command_description"}`
* Anything in the `command_data` hash will be automatically parsed upon any `#help` call - to include partial matching criteria
* The same is true for `bot_command_data`
* These templates are not required for use..but, do provide a basic 'use' structure so that command coding is simplified and easier to implement
* Older 'say' command code may still work as-is..though, it has not been tested






