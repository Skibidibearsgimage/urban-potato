-- ===================== Services =====================
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Remotes / Events
local ItemShop = ReplicatedStorage.Remotes.ItemShop -- RemoteFunction
local EnterCode = ReplicatedStorage.Remotes.EnterCode -- RemoteFunction
local OpenWindow = ReplicatedStorage.ClientEvents.OpenWindow -- BindableEvent

-- ===================== Track Shop Data =====================
local ShopItems = {
	["Straight Track"] = { price = 25 },
    ["Turn Track"] = { price = 50 },
    ["Bump Track"] = { price = 250 },
    ["Climb Track"] = { price = 1500 },
    ["Jump Track"] = { price = 2500 },
    ["Switch Track"] = { price = 10000 },
    ["Long Climb Track"] = { price = 12500 },
    ["Stop Track"] = { price = 17500 },
    ["Seesaw Track"] = { price = 25000 },
    ["Spike Track"] = { price = 50000 },
    ["Railed Turn Track"] = { price = 75000 },
    ["Lift Track"] = { price = 150000 },
    ["Long Straight Track"] = { price = 275000 },
    ["Curve Track"] = { price = 450000 },
    ["Speed Boost Track"] = { price = 750000 },
    ["Banked Turn Track"] = { price = 1000000 },
    ["Laser Track"] = { price = 1500000 },
    ["Crossing Track"] = { price = 2250000 },
    ["Banked U-Turn Track"] = { price = 3000000 },
    ["Merge Left Track"] = { price = 4250000 },
    ["Merge Right Track"] = { price = 4250000 },
    ["Brake Track"] = { price = 5000000 },
    ["Flamethrower Track"] = { price = 7750000 },
    ["Curved Climb Track"] = { price = 10000000 },
    ["Healing Track"] = { price = 12500000 },
    ["Railed Climb Track"] = { price = 17000000 },
    ["Banked Climb Track"] = { price = 20000000 },
    ["Small Spiral Track"] = { price = 25000000 },
    ["Disappearing Track"] = { price = 30000000 },
    ["Super Long Climb Track"] = { price = 35000000 },
    ["S Track"] = { price = 40000000 },
    ["Hammer Track"] = { price = 45000000 },
    ["Steep Track"] = { price = 50000000 },
    ["Camera Track"] = { price = 66000000 },
    ["Fireworks Track"] = { price = 75000000 },
    ["Elevator Track"] = { price = 100000000 },
    ["Drop Track"] = { price = 90000000 },
    ["Spiral Track"] = { price = 150000000 },
    ["Loop Track"] = { price = 250000000 },
    ["Portal Track"] = { price = 500000000 },
}

-- ===================== Gear Shop Data =====================
local GearItems = {
	["Wrench"] = { price = 50000, order = 1 },
	["Gold Wrench"] = { price = 250000, order = 2 },
    ["Teleport Gear"] = { price = 475000, order = 3 },
    ["Paint Bucket"] = { price = 1000000, order = 4 },
    ["Defroster"] = { price = 1500000, order = 5 },
    ["Time Dial"] = { price = 2500000, order = 6 },
	["Port-A-Cart"] = { price = 5000000, order = 7 },
    ["Careful Remover"] = { price = 10000000, order = 8 },
	["Diamond Wrench"] = { price = 25000000, order = 9 },
    ["Magnet"] = { price = 75000000, order = 10 },
    ["Magic Sponge"] = { price = 150000000, order = 11 },
    ["Galaxy Time Dial"] = { price = 1000000, order = 12 },


}



-- ===================== Load Rayfield =====================
local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()

local Window = Rayfield:CreateWindow({
	Name = "Auto Buy",
	LoadingTitle = "Script Loading..",
	LoadingSubtitle = "Auto Buyer",
	Theme = "Default",
	ShowText = "Frosty The Snowman",
	ToggleUIKeybind = "K",
	KeySystem = false,
})

-- ==========================================================
--  Tracks Tab
-- ==========================================================
local AutoBuyTab = Window:CreateTab("Auto Buy", "shopping-cart")

AutoBuyTab:CreateButton({
	Name = "Open Shop Window",
	Callback = function()
		local ReplicatedStorage = game:GetService("ReplicatedStorage")
		local OpenWindow = ReplicatedStorage.ClientEvents.OpenWindow
		OpenWindow:Fire("ItemShop")
		print("[SRBX] Fired OpenWindow('ItemShop') for Merchant John")
	end,
})

local AutoBuyToggles = {}

local function buyItem(itemName)
	local success, result = pcall(function()
		return ItemShop:InvokeServer("buy", "Tracks", itemName)
	end)
	if success then
		print("[SRBX] Bought:", itemName)
	else
		warn("[SRBX] Failed to buy:", itemName, result)
	end
end

task.spawn(function()
	while true do
		for name, enabled in pairs(AutoBuyToggles) do
			if enabled then
				buyItem(name)
				task.wait(0)
			end
		end
		task.wait(0)
	end
end)

local sortedItems = {}
for name, info in pairs(ShopItems) do
	table.insert(sortedItems, { name = name, price = info.price })
end
table.sort(sortedItems, function(a, b) return a.price < b.price end)

for _, item in ipairs(sortedItems) do
	AutoBuyTab:CreateToggle({
		Name = string.format("%s  %s", item.name, item.price),
		CurrentValue = false,
		Callback = function(v)
			AutoBuyToggles[item.name] = v
			print(string.format("[SRBX] AutoBuy %s: %s", v and "enabled" or "disabled", item.name))
		end,
	})
end



-- ==========================================================
-- Gear Shop Tab
-- ==========================================================
local GearTab = Window:CreateTab("Gear Shop", "hammer")

GearTab:CreateButton({
	Name = "Open Gear Shop Window",
	Callback = function()
		local ReplicatedStorage = game:GetService("ReplicatedStorage")
		local OpenWindow = ReplicatedStorage.ClientEvents.OpenWindow
		OpenWindow:Fire("ItemShop")
		print("[SRBX] Fired OpenWindow('ItemShop') for Gary Gear Shop")
	end,
})

local AutoBuyGears = {}

local function buyGear(gearName)
	local success, result = pcall(function()
		return ItemShop:InvokeServer("buy", "Gears", gearName)
	end)
	if success then
		print("[SRBX] Bought Gear:", gearName)
	else
		warn("[SRBX] Failed to buy gear:", gearName, result)
	end
end

task.spawn(function()
	while true do
		for name, enabled in pairs(AutoBuyGears) do
			if enabled then
				buyGear(name)
				task.wait(0)
			end
		end
		task.wait(0)
	end
end)

local sortedGears = {}
for name, info in pairs(GearItems) do
	table.insert(sortedGears, { name = name, price = info.price })
end
table.sort(sortedGears, function(a, b) return a.price < b.price end)

for _, gear in ipairs(sortedGears) do
	GearTab:CreateToggle({
		Name = string.format("%s  %s", gear.name, gear.price),
		CurrentValue = false,
		Callback = function(v)
			AutoBuyGears[gear.name] = v
			print(string.format("[SRBX] AutoBuy %s: %s", v and "enabled" or "disabled", gear.name))
		end,
	})
end
