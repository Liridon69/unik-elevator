# Unik Elevator


**This is just some snip code for qb-core**


**In qb-smallresources/client/teleports.lua replace existing code with this**

```
local QBCore = exports['qb-core']:GetCoreObject()

for k, v in pairs(Config.locations) do
    for i, location in pairs(v) do
        exports['qb-target']:AddBoxZone(k..i, vector3(location.button.x, location.button.y, location.button.z), 1.0, 1.0, {
            name = location.name,
            heading = location.button.w,
            debugPoly = false,
            minZ = location.button.z-1,
            maxZ = location.button.z+1,
        }, {
            options = {
                {
                    icon = 'las la-sort',
                    label = "Elevator",
                    action = function(entity) 
                        print(entity)
                        if IsPedAPlayer(entity) then return false end
                        TriggerEvent("qb-smallresources:client:showElevatorMenu", {["location"] = k,["index"]=i})
                      end,
                },
            },
            distance = 2
        })
    end
end



RegisterNetEvent('qb-smallresources:client:showElevatorMenu', function(data)
    local Locations = Config.locations[data['location']]
    local Elv = {{
        header = data['location'].." Elevator",
        isMenuHeader = true, -- Set to true to make a nonclickable title
    },}
    for i, v in pairs(Locations) do
        local txt =""
        local isDisable = false
        if i ==data['index'] then
            txt="You Are Here"
            isDisable=true
        else
            txt=""
            isDisable=false
        end
        local tempData= {
            header = v.name,
            txt = txt,
            disabled = isDisable,
			params = {
                isServer = false,
                
                event = "qb-smallresources:client:teleportTo",
                args = {
                    loc = v['teleport'],
                }
            }
        }
        table.insert(Elv, tempData)
    end
    
    exports['qb-menu']:openMenu(Elv)
end)

RegisterNetEvent('qb-smallresources:client:teleportTo', function(data)
    local ped = PlayerPedId()
    if not IsPedInAnyVehicle(ped) then
        QBCore.Functions.Progressbar("Call_The_Lift", "Please Wait for Elevator", 5000, false, false, {
            disableMovement = true,
            disableCarMovement = true,
            disableMouse = false,
            disableCombat = true,
        }, {
            animDict = "anim@apt_trans@elevator",
            anim = "elev_1",
            flags = 16,
        }, {}, {}, function() -- Done
            StopAnimTask(ped, "anim@apt_trans@elevator", "elev_1", 1.0)
            DoScreenFadeOut(500)
            Wait(1000)
            if Config.UseSoundEffect then
                TriggerServerEvent("InteractSound_SV:PlayOnSource", "LiftSoundBellRing", 0.05)
            end
            SetEntityCoords(ped, data.loc.x, data.loc.y, data.loc.z, 0, 0, 0, false)
            SetEntityHeading(ped, data.loc.w)
            Wait(1000)
            DoScreenFadeIn(600)
            
        end)
    end
    
end)
```










**In qb-smallresources/config.lua replace  this code** 


```
Config.Teleports = {
    --Elevator @ labs
    [1] = {
        [1] = {
            coords = vector4(3540.74, 3675.59, 20.99, 167.5),
            ["AllowVehicle"] = false,
            drawText = '[E] Take Elevator Up'
        },
        [2] = {
            coords = vector4(3540.74, 3675.59, 28.11, 172.5),
            ["AllowVehicle"] = false,
            drawText = '[E] Take Elevator Down'
        },

    },
    --Coke Processing Enter/Exit
    [2] = {
        [1] = {
            coords = vector4(909.49, -1589.22, 30.51, 92.24),
            ["AllowVehicle"] = false,
            drawText = '[E] Enter Coke Processing'
        },
        [2] = {
            coords = vector4(1088.81, -3187.57, -38.99, 181.7),
            ["AllowVehicle"] = false,
            drawText = '[E] Leave'
        },
    },
}
```


**Whit this**

```
Config.locations = {
    ["Casino"] = {
        [1] = {
            ["name"] = "Casino",
            ["teleport"] = vector4(960.39, 42.34, 71.7, 98.11),
            ["button"] = vector4(960.39, 42.34, 71.7, 98.11)
        },
        [2] = {
            ["name"] = "Casino Roof",
            ["teleport"] = vector4(964.27, 57.67, 112.55, 246.65),
            ["button"] = vector4(964.27, 57.67, 112.55, 246.65)
        },
        [3] = {
            ["name"] = "Casino Helipad",
            ["teleport"] = vector4(971.87, 51.41, 120.24, 145.8),
            ["button"] = vector4(971.87, 51.41, 120.24, 145.8)
        },
    },
}
```
**Drag and drop LiftSoundRingBell from sounds folder into interact-sound\client\html\sounds**