Notes for modding Don't Starve Together. Mapping out the source

First goal: disabling smoldering of structures in summer 
(Will be fine if this is a server-only code change, not a proper standalone mod)

## Questions
* Which parts are server-authoritative; client-server sync
* Which parts are overrideable in a stand-alone mod vs which must be changed directly in source
     * Presumably a mod overrides objects in scope (plus also uses mod-specific hooks)
* Can we hook into default menus in a standalone mod, or must they stay in their own menu in the mods tab?
    * Would like to add world options to summer

## Navigating source

* `ms_` event prefixes for "master sim" events
* search for `assert(TheWorld.ismastersim, ....)` asserts to find server-only code
    * many other statements using `TheWorld.ismastersim`, functions which are nilled out if not, etc
    (Also in other forms; locals like `_ismastersim`)
    * Some other notes in code about what works in master sim or client, e.g in `entityscript.lua`:

            --only works on master sim
            function EntityScript:GetPlatformFollowers()
                return self.platformfollowers
            end
        ... but this doesn't seem to be referenced anywhere
* Note from `weather.lua:767`
    > --[[
    > Client updates temperature, moisture, precipitation effects, and snow
    > level on its own while server force syncs values periodically. Client
    > cannot start, stop, or change precipitation on its own, and must wait
    > for server syncs to trigger these events.
    > --]]
* Weather related:
    * `components/weather.lua`, `components/wildfires.lua`
* **Tags**: Game logic makes heavy use of tags to denote intrinsic properties or current status
    * `HasTag`, `AddTag`, `RemoveTag`
* **Components**: Various classes that define behaviors such as being a 'burnable'. Classes are added as components to objects; appears to
originate in `EntityScript.lua` with `EntityScript:AddComponent(name)`
* **Event system**: Also appears to be defined in `EntityScript.lua`
    * `EntityScript:PushEvent`, `EntityScript:ListenForEvent`, `EntityScript:RemoveEventCallback`, `EntityScript:RemoveAllEventCallbacks`     
* **Constants**: `tuning.lua`, `constants.lua`
    * `tuning.lua` has `AddTuningModifier` but this isn't referenced elsewhere

## Unused

* `local BEEF_HASTAGS = {"beefalo"}` in `componentactions.lua`

## ETC

* `brains/abigailbrain.lua` and `brains/shadowmaxwellbrain.lua` have a `DanceParty` function that makes them dance when the player dances

        --#1 priority is dancing beside your leader. Obviously.
        local dance = WhileNode(function() return ShouldDanceParty(self.inst) end, "Dance Party",
            PriorityNode({
                Leash(self.inst, GetLeaderPos, TUNING.ABIGAIL_DEFENSIVE_MED_FOLLOW, TUNING.ABIGAIL_DEFENSIVE_MED_FOLLOW),
                ActionNode(function() DanceParty(self.inst) end),
        }, .25))

    