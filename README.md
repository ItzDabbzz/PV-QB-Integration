# WIP
# enc0ded's Persisitant Vehicle - QBCore Framework Integration

Hi, this will walk you though integrating enc0ded's [Persistent Vehicles 2.0](https://github.com/enc0ded/enc0ded-persistent-vehicles-2.0) into [QBCore Framework](https://github.com/qbcore-framework?q=&type=&language=&sort=)


# QB-Core

### Client/Functions.lua - Function QBCore.Functions.SetVehicleProperties

After the end [@L629](https://github.com/qbcore-framework/qb-core/blob/fffbb41840c3269ff83c22e8d93b8269fa16661d/client/functions.lua#L629) and before [@L630](https://github.com/qbcore-framework/qb-core/blob/fffbb41840c3269ff83c22e8d93b8269fa16661d/client/functions.lua#L630)

    TriggerEvent('persistent-vehicles/update-vehicle', vehicle)
---
### Client/Events.lua
##### QBCore:Command:SpawnVehicle Event :

>  ❗❗  1 main issue with this method, is that some other resources set the vehicle plate after they spawn the vehicle, rendering the register for persistent vehicles useless and wont work. More on this down below

At the SpawnVehicle event after the event trigger "vehiclekeys:client:SetOwner" [@L59](https://github.com/qbcore-framework/qb-core/blob/fffbb41840c3269ff83c22e8d93b8269fa16661d/client/events.lua#L59) 

    RegisterNetEvent('QBCore:Command:SpawnVehicle', function(vehName)
		local ped = PlayerPedId()
		local hash = GetHashKey(vehName)
		if not IsModelInCdimage(hash) then
			return
		end
		RequestModel(hash)
		while not HasModelLoaded(hash) do
			Wait(10)
		end
		local vehicle = CreateVehicle(hash, GetEntityCoords(ped), GetEntityHeading(ped), true, false)
		TaskWarpPedIntoVehicle(ped, vehicle, -1)
		SetModelAsNoLongerNeeded(vehicle)
		TriggerEvent("vehiclekeys:client:SetOwner", GetVehicleNumberPlateText(vehicle))
		-- Persistant Vehicle Vehicle Event
		TriggerEvent('persistent-vehicles/register-vehicle', vehicle)
	end)

##### QBCore:Command:DeleteVehicle Event :
At the SpawnVehicle event after the event trigger "vehiclekeys:client:SetOwner" [@L59](https://github.com/qbcore-framework/qb-core/blob/fffbb41840c3269ff83c22e8d93b8269fa16661d/client/events.lua#L59) 

    RegisterNetEvent('QBCore:Command:DeleteVehicle', function()
	    local ped = PlayerPedId()
	    local veh = GetVehiclePedIsUsing(ped)
	    if veh ~= 0 then
		    SetEntityAsMissionEntity(veh, true, true)
		    -- Persistant Vehicle Vehicle Event
		    TriggerEvent('persistent-vehicles/forget-vehicle', veh)
		    DeleteVehicle(veh)
	    else
		    local pcoords = GetEntityCoords(ped)
		    local vehicles = GetGamePool('CVehicle')
		    for k, v in pairs(vehicles) do
			    if #(pcoords - GetEntityCoords(v)) <= 5.0 then
				    SetEntityAsMissionEntity(v, true, true)
				    -- Persistant Vehicle Vehicle Event
				    TriggerEvent('persistent-vehicles/forget-vehicle', v)
				    DeleteVehicle(v)
			    end
		    end
	    end
    end)
      
## Other Changes

     Add these events after the lines I linked below

- qb-vehicleshop
> [@L595](https://github.com/qbcore-framework/qb-vehicleshop/blob/2c72f286a36c614a6664958643d6f03f17672235/client.lua#L595)
> `TriggerEvent('persistent-vehicles/register-vehicle', veh)`
---
- qb-vehiclekeys 
> [@L182](https://github.com/qbcore-framework/qb-vehiclekeys/blob/e5bf8f955bb3cfb0a5e165d0e498059700f45a23/client/main.lua#L182)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

> [@L197](https://github.com/qbcore-framework/qb-vehiclekeys/blob/e5bf8f955bb3cfb0a5e165d0e498059700f45a23/client/main.lua#L197)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

> [@L249](https://github.com/qbcore-framework/qb-vehiclekeys/blob/e5bf8f955bb3cfb0a5e165d0e498059700f45a23/client/main.lua#L249)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`
---
- qb-vehiclefailure
> [@L87](https://github.com/qbcore-framework/qb-vehiclefailure/blob/8fb18c1eca951b5c9cfcf3c8a4dfbcc70a5e49c2/client.lua#L87)
> `TriggerEvent('persistent-vehicles/update-vehicle', vehicle)`

>[@L103](https://github.com/qbcore-framework/qb-vehiclefailure/blob/8fb18c1eca951b5c9cfcf3c8a4dfbcc70a5e49c2/client.lua#L103)
> `TriggerEvent('persistent-vehicles/update-vehicle', vehicle)`

>[@L169](https://github.com/qbcore-framework/qb-vehiclefailure/blob/8fb18c1eca951b5c9cfcf3c8a4dfbcc70a5e49c2/client.lua#L169)
> `TriggerEvent('persistent-vehicles/update-vehicle', vehicle)`

>[@L212](https://github.com/qbcore-framework/qb-vehiclefailure/blob/8fb18c1eca951b5c9cfcf3c8a4dfbcc70a5e49c2/client.lua#L212)
> `TriggerEvent('persistent-vehicles/update-vehicle', vehicle)`

>[@L329](https://github.com/qbcore-framework/qb-vehiclefailure/blob/8fb18c1eca951b5c9cfcf3c8a4dfbcc70a5e49c2/client.lua#L329)
> `TriggerEvent('persistent-vehicles/update-vehicle', vehicle)`

>[@L361](https://github.com/qbcore-framework/qb-vehiclefailure/blob/8fb18c1eca951b5c9cfcf3c8a4dfbcc70a5e49c2/client.lua#L361)
> `TriggerEvent('persistent-vehicles/update-vehicle', vehicle)`

>[@L391](https://github.com/qbcore-framework/qb-vehiclefailure/blob/8fb18c1eca951b5c9cfcf3c8a4dfbcc70a5e49c2/client.lua#L391)
> `TriggerEvent('persistent-vehicles/update-vehicle', vehicle)`

- qb-vehiclesales
>[@L216](https://github.com/qbcore-framework/qb-vehiclesales/blob/66883a59c0d7465be83a72738498d78a0cbe96f8/client/main.lua#L216)
> `TriggerEvent('persistent-vehicles/update-vehicle', oSlot[i]["occasionid"])`

>[@L294](https://github.com/qbcore-framework/qb-vehiclesales/blob/66883a59c0d7465be83a72738498d78a0cbe96f8/client/main.lua#L294)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

>[@L315](https://github.com/qbcore-framework/qb-vehiclesales/blob/66883a59c0d7465be83a72738498d78a0cbe96f8/client/main.lua#L315)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

- qb-tunerchip
>[@L15](https://github.com/qbcore-framework/qb-tunerchip/blob/bbd42dcd54dd37c19bce4d94ba717a3ab5b0cb5e/client/main.lua#L15)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

>[@L23](https://github.com/qbcore-framework/qb-tunerchip/blob/bbd42dcd54dd37c19bce4d94ba717a3ab5b0cb5e/client/main.lua#L23)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

>[@L160](https://github.com/qbcore-framework/qb-tunerchip/blob/bbd42dcd54dd37c19bce4d94ba717a3ab5b0cb5e/client/main.lua#L160)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

>[@L166](https://github.com/qbcore-framework/qb-tunerchip/blob/bbd42dcd54dd37c19bce4d94ba717a3ab5b0cb5e/client/main.lua#L166)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

>[@L240](https://github.com/qbcore-framework/qb-tunerchip/blob/bbd42dcd54dd37c19bce4d94ba717a3ab5b0cb5e/client/main.lua#L240)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

>[@L248](https://github.com/qbcore-framework/qb-tunerchip/blob/bbd42dcd54dd37c19bce4d94ba717a3ab5b0cb5e/client/main.lua#L248)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

- qb-customs
>[@L57](https://github.com/qbcore-framework/qb-customs/blob/e1e27886fdde53b22165110234ac470b0e5b57e9/client/cl_bennys.lua#L57)
> `TriggerEvent('persistent-vehicles/update-vehicle', veh)`

>[@L88](https://github.com/qbcore-framework/qb-customs/blob/e1e27886fdde53b22165110234ac470b0e5b57e9/client/cl_bennys.lua#L88)
> `TriggerEvent('persistent-vehicles/update-vehicle', plyVeh)`




>[@L]()
> `TriggerEvent('persistent-vehicles/update-vehicle', vehicle)`
