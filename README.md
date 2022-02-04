---
description: >-
  Hello, world! The documentation available here outlines the uses of Genesis's
  Lua API.
---

# Genesis API

In development.

```
--References
local ui_get = ui.get
local ui_set = ui.set
local ui_set_visible = ui.set_visible
local console_cmd = client.exec

--Primary Weapon List
local primary_weapons = {"-", "AWP", "SCAR20/G3SG1", "Scout", "M4/AK47", "Famas/Galil", "Aug/SG553", "M249/Negev", "Mag7/SawedOff", "Nova", "XM1014", "MP9/Mac10", "UMP45", "PPBizon", "MP7"}

--Secondary Weapon List
local secondary_weapons = {"-", "CZ75/Tec9/FiveSeven", "P250", "Deagle/Revolver", "Dualies"}

--Grenade List
local grenades = {"HE Grenade", "Molotov", "Smoke", "Flash", "Flash", "Decoy", "Decoy"}

--Utility List
local utilities = {"Armor", "Helmet", "Zeus", "Defuser"}

--Logged grenade selection for limiting selections to 4
local logged_grenades = {}

--Table of commands
local translated_for_command = {
	["AWP"] = "buy awp",
	["SCAR20/G3SG1"] = "buy scar20",
	["Scout"] = "buy ssg08",
	["M4/AK47"] = "buy m4a1",
	["Famas/Galil"] = "buy famas",
	["Aug/SG553"] = "buy aug",
	["M249/Negev"] = "buy m249",
	["Mag7/SawedOff"] = "buy mag7",
	["Nova"] = "buy nova",
	["XM1014"] = "buy xm1014",
	["MP9/Mac10"] = "buy mp9",
	["UMP45"] = "buy ump45",
	["PPBizon"] = "buy bizon",
	["MP7"] = "buy mp7",
	["CZ75/Tec9/FiveSeven"] = "buy tec9",
	["P250"] = "buy p250",
	["Deagle/Revolver"] = "buy deagle",
	["Dualies"] = "buy elite",
	["HE Grenade"] = "buy hegrenade",
	["Molotov"] = "buy molotov",
	["Smoke"] = "buy smokegrenade",
	["Flash"] = "buy flashbang",
	["Decoy"] = "buy decoy",
	["Armor"] = "buy vest",
	["Helmet"] = "buy vesthelm",
	["Zeus"] = "buy taser 34",
	["Defuser"] = "buy defuser"
}

--Main Checkbox
local checkbox_auto_buy = ui.new_checkbox("MISC", "Miscellaneous", "Auto Buy")

--Primary Components
local combobox_primary_weapons = ui.new_combobox("MISC", "Miscellaneous", "Primary", primary_weapons)

--Secondary Components
local combobox_secondary_weapons = ui.new_combobox("MISC", "Miscellaneous", "Secondary", secondary_weapons)

--Grenade Components
local multiselect_grenades = ui.new_multiselect("MISC", "Miscellaneous", "Grenades", grenades)

--Utility Components
local multiselect_utilities = ui.new_multiselect("MISC", "Miscellaneous", "Utilities", utilities)

--Set visibility
ui_set_visible(combobox_primary_weapons, ui_get(checkbox_auto_buy))
ui_set_visible(combobox_secondary_weapons, ui_get(checkbox_auto_buy))
ui_set_visible(multiselect_grenades, ui_get(checkbox_auto_buy))
ui_set_visible(multiselect_utilities, ui_get(checkbox_auto_buy))

local function on_round_prestart(e)

	if ui_get(checkbox_auto_buy) then

		--Secondary Weapon
		for k, v in pairs(translated_for_command) do
			
			if k == ui_get(combobox_secondary_weapons) then
				
				console_cmd(v)
				
			end
			
		end


		--Utility
		local utility_value = ui_get(multiselect_utilities)

		for uindex = 1, table.getn(utility_value) do

			local value_at_index = utility_value[uindex]

			for k, v in pairs(translated_for_command) do

				if k == value_at_index then

					console_cmd(v)

				end

			end

		end

		--Primary Weapon
		for k, v in pairs(translated_for_command) do

			if k == ui_get(combobox_primary_weapons) then

				console_cmd(v)

			end

		end
			

	    --Grenades
		local grenade_value = ui_get(multiselect_grenades)
		
			for gindex = 1, table.getn(grenade_value) do
		
				local value_at_index = grenade_value[gindex]
		
				for k, v in pairs(translated_for_command) do
		
					if k == value_at_index then
		
						console_cmd(v)

					end

				end
		
			end
		
		end

    end

--Set component visibility if auto buy is enabled
local function visibility_callback()

	local general_checked = ui_get(checkbox_auto_buy)
    ui_set_visible(combobox_primary_weapons, general_checked)
	ui_set_visible(combobox_secondary_weapons, general_checked)
	ui_set_visible(multiselect_grenades, general_checked)
	ui_set_visible(multiselect_utilities, general_checked)

end

--Limit the amount of options selected in the grenade multiselect component
local function grenade_limit_callback(val)

	local value = ui_get(val)

	if table.getn(value) >= 5 then

		ui_set(val, logged_grenades)

		return

	end

	logged_grenades = value

end

ui.set_callback(checkbox_auto_buy, visibility_callback)
ui.set_callback(multiselect_grenades, grenade_limit_callback)

client.set_event_callback("round_prestart", on_round_prestart)
```



