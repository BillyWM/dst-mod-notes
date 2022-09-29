Notes for modding Don't Starve Together. Mapping out the source

First goal: disabling smoldering of structures in summer 
(Will be fine if this is a server-only code change, not a proper standalone mod)

## Questions
* Which parts are server-authoritative; client-server sync
* Which parts are overrideable in a stand-alone mod vs which must be changed directly in source
     * Presumably a mod overrides objects in scope (plus also uses mod-specific hooks)

## Navigating source

* `ms_` event prefixes for "master sim" events
* search for `assert(TheWorld.ismastersim, ....)` asserts to find server-only code
    * many other statements using `TheWorld.ismastersim`, functions which are nilled out if not, etc
    (Also in other forms; locals like `_ismastersim`)
* Note from `weather.lua:767`
    > --[[
    > Client updates temperature, moisture, precipitation effects, and snow
    > level on its own while server force syncs values periodically. Client
    > cannot start, stop, or change precipitation on its own, and must wait
    > for server syncs to trigger these events.
    > --]]
* Weather related:
    * `components/weather.lua`, `components/wildfires.lua`

    