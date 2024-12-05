Configure the item condition of any item in any trader's inventory. Uses trader_autoinject, making it very compatible.

Add support for any trader config profile (for example, "trader_stalker_loris.ltx"), or just use trader_autoinject's basic 4 tradertypes.
Add any item or item type, vanilla or mod.

# Adding a trader
When you open trade with a trader, their trade profile path is printed to the console (i.e. "items\trade\trade_generic_mechanic.ltx"). 
Take the config filename (just the "trade_generic_mechanic" part) and add it to:
```
[trader_configs]
trade_generic_mechanic = true
```

# Adding an item
Then, add a new section with that trader profile as the name, and add any item _section_ or _item type_
```
[trade_generic_mechanic]
i_device = 20, 1, 30 \ 10, 31, 35 \ 10, 36, 40 \ 60, 41, 100 ; you can change an entire item type
detector_simple = 90, 1, 90 \ 10, 91, 100 ; or you can just change a single item section
```

Set each item equal to the condition ranges you want to set. This mod allows the use of a weighted random distribution, meaning you can certain ranges of conditions more common than others. For example, a normal randomization means there is a 1/100 chance devices will be any condition, 0% or 100%. But with the above config, there is now a 60% that devices will be between 41% and 100%. Its just like lootboxes; certain items are more rare, and thus harder to get; same principle here. You can make good conditioned items rarer, or whatever you want really.

Pay attention to the syntax.
```
item or item_type = weight_1, min_cond1, max_cond1 \ weight_2, min_cond2, max_cond2 \ etc...
```
You can have, technically, an infinite number of weights and ranges, it doesn't matter. The weights **do not** have to equal 100; the weights can sum to any total you want, you just need to understand the statistics of that. For that reason, I suggest sticking to a weight-sum of 100.

Additionally, if you only have *one* weight and range:
```
item or item_type = weight_1, min_cond1, max_cond1 
```
obviously, the weight doesn't matter, and you only need to use the min_cond and max_cond variables

**Weights need to be **positive** and **greater than zero****

If you want to add normal, equal-chance random conditions to items, you can do that:
```
item or item_type = 100, 1, 100
```
Now, there's a 1/100 chance of getting an item in any condition between 1% and 100%.

Again, the weight doesn't matter if there is only one weight and range.
## I updated the config file but the traders' items' conditions haven't changed!!
Changes only take affect after trader inventory refresh. Either wait until inventories refresh, or use debug menu to refresh each trader inventory manually.
# Removing a trader
You can simple remove the trader name or tradertype from `trader_configs`, or if you want to *blacklist* a specific trader you can set that trader's name to false
```
[trader_configs]
SUPPLIER = true
trade_duty = false ; Patrenko will be ignored
```

Trader_autoinject relates the generic trade profiles to "BARMAN, MECHANIC, MEDIC" and everything else to "SUPPLIER", so if you want to blaclist a mechanic, chances are you just remove "MECHANIC." In Anomaly and GAMMA, there are no mechanics with their own, unique trade profiles.

# Installation
Install with Mod Organizer, put it anywhere in load order.

This mod includes a copy of `trader_autoinject.script`, but you should always have the most up-to-date versions overwriting all your mods. Get the depencencies [here](https://github.com/ahuyn/anomaly-dependencies/tree/master/trader_autoinject).

# The formula for weighted distribution, if you care
I feed a big ol' table of weights and min/max ranges in, then all the weights are summed. Next, a random number is chosen between 1 and the poolsize (the summed weights), I iterate through the weights, subtract a weight from the randomly selected number, then check if that subtraction is less than/equal to 0; if it is, that weight and pair are chosen; if not, I repeat, until it is so.
```
local function weighted_random(pool)
    -- Adding up weights
    local poolsize = 0
    for k, v in pairs(pool) do
        poolsize = poolsize + v["weight"]
    end

    local selection = math.random(1, poolsize)
    for k, v in pairs(pool) do
        selection = selection - v["weight"]
        -- Spit out the range to use
        if (selection <= 0) then
            return pool[k]
        end
    end
end

```

# Compatibility
Compatible with Trader Destockifier; does a little trickery with that mod to make sure this mod runs **after** it. If you notice some items not having their conditions set after destockifying, let me know.
Compatible with any 