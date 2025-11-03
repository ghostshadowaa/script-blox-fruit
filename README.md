# script-blox-fruit
-- DARK Hub - Blox Fruits Script
-- By: YourNameHere

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local VirtualInputManager = game:GetService("VirtualInputManager")
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TeleportService = game:GetService("TeleportService")

local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()

-- Configurações
getgenv().Settings = {
    AutoFarm = false,
    AutoQuest = false,
    AutoEvents = false,
    AutoBoss = false,
    AutoSell = false,
    AutoCollectFruits = false,
    AutoRedeemCodes = false,
    WebhookEnabled = false,
    WebhookURL = "",
    SelectedWeapon = "Combat",
    SelectedFruit = "",
    FarmSpeed = 1,
    AttackDistance = 10
}

-- DARK Hub GUI
local Window = Rayfield:CreateWindow({
    Name = "🔮 DARK Hub | Blox Fruits",
    LoadingTitle = "DARK Hub Loading...",
    LoadingSubtitle = "by Dark Developers",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "DARKHub",
        FileName = "BloxFruits"
    },
    Discord = {
        Enabled = true,
        Invite = "invitelink", -- Discord invite link
        RememberJoins = true
    },
    KeySystem = false, -- Set to true to use key system
    KeySettings = {
        Title = "DARK Hub",
        Subtitle = "Key System",
        Note = "Join discord for key",
        FileName = "Key",
        SaveKey = false,
        GrabKeyFromSite = false,
        Key = "KEYHERE"
    }
})

-- Main Tab
local MainTab = Window:CreateTab("🏠 Main", "rbxassetid://4483345998")
local FarmingSection = MainTab:CreateSection("Auto Farming")

local AutoFarmToggle = MainTab:CreateToggle({
    Name = "⚔️ Auto Farm NPCs",
    CurrentValue = false,
    Flag = "AutoFarm",
    Callback = function(Value)
        Settings.AutoFarm = Value
        if Value then
            StartAutoFarm()
        end
    end,
})

local AutoQuestToggle = MainTab:CreateToggle({
    Name = "📜 Auto Quest",
    CurrentValue = false,
    Flag = "AutoQuest",
    Callback = function(Value)
        Settings.AutoQuest = Value
        if Value then
            StartAutoQuest()
        end
    end,
})

local AutoBossToggle = MainTab:CreateToggle({
    Name = "👹 Auto Boss",
    CurrentValue = false,
    Flag = "AutoBoss",
    Callback = function(Value)
        Settings.AutoBoss = Value
        if Value then
            StartAutoBoss()
        end,
    },
})

-- Weapons Section
local WeaponsSection = MainTab:CreateSection("Weapon Settings")

local WeaponDropdown = MainTab:CreateDropdown({
    Name = "🎯 Selected Weapon",
    Options = {"Combat", "Sword", "Gun", "Fruit"},
    CurrentOption = "Combat",
    Flag = "WeaponDropdown",
    Callback = function(Option)
        Settings.SelectedWeapon = Option
    end,
})

local FarmSpeedSlider = MainTab:CreateSlider({
    Name = "⚡ Farm Speed",
    Range = {1, 10},
    Increment = 1,
    Suffix = "x",
    CurrentValue = 1,
    Flag = "FarmSpeed",
    Callback = function(Value)
        Settings.FarmSpeed = Value
    end,
})

-- Teleport Tab
local TeleportTab = Window:CreateTab("📍 Teleports", "rbxassetid://4483345998")

local IslandsSection = TeleportTab:CreateSection("Islands Teleport")

local Islands = {
    "Starter Island",
    "Jungle",
    "Pirate Village",
    "Desert",
    "Snow Mountain",
    "Marine Fortress",
    "Sky Island 1",
    "Sky Island 2",
    "Prison",
    "Colosseum",
    "Magma Village",
    "Underwater City",
    "Fountain City"
}

for _, island in pairs(Islands) do
    TeleportTab:CreateButton({
        Name = "🚩 " .. island,
        Callback = function()
            TeleportToIsland(island)
        end,
    })
end

-- Bosses Teleport Section
local BossesSection = TeleportTab:CreateSection("Bosses Teleport")

local Bosses = {
    "The Gorilla King",
    "Bobby",
    "Yeti",
    "Vice Admiral",
    "Warden",
    "Chief Warden",
    "Swan",
    "Magma Admiral"
}

for _, boss in pairs(Bosses) do
    TeleportTab:CreateButton({
        Name = "👹 " .. boss,
        Callback = function()
            TeleportToBoss(boss)
        end,
    })
end

-- Misc Tab
local MiscTab = Window:CreateTab("⚙️ Miscellaneous", "rbxassetid://4483345998")

local AutoSection = MiscTab:CreateSection("Auto Features")

local AutoSellToggle = MiscTab:CreateToggle({
    Name = "💰 Auto Sell",
    CurrentValue = false,
    Flag = "AutoSell",
    Callback = function(Value)
        Settings.AutoSell = Value
        if Value then
            StartAutoSell()
        end,
    },
})

local AutoCollectFruitsToggle = MiscTab:CreateToggle({
    Name = "🍎 Auto Collect Fruits",
    CurrentValue = false,
    Flag = "AutoCollectFruits",
    Callback = function(Value)
        Settings.AutoCollectFruits = Value
        if Value then
            StartAutoCollectFruits()
        end,
    },
})

local AutoRedeemCodesToggle = MiscTab:CreateToggle({
    Name = "🎫 Auto Redeem Codes",
    CurrentValue = false,
    Flag = "AutoRedeemCodes",
    Callback = function(Value)
        Settings.AutoRedeemCodes = Value
        if Value then
            RedeemAllCodes()
        end,
    },
})

-- Player Tab
local PlayerTab = Window:CreateTab("👤 Player", "rbxassetid://4483345998")

local CharacterSection = PlayerTab:CreateSection("Character Modifications")

local WalkSpeedSlider = PlayerTab:CreateSlider({
    Name = "👟 Walk Speed",
    Range = {16, 200},
    Increment = 1,
    Suffix = "studs",
    CurrentValue = 16,
    Flag = "WalkSpeed",
    Callback = function(Value)
        if Character and Character:FindFirstChild("Humanoid") then
            Character.Humanoid.WalkSpeed = Value
        end
    end,
})

local JumpPowerSlider = PlayerTab:CreateSlider({
    Name = "🦘 Jump Power",
    Range = {50, 200},
    Increment = 1,
    Suffix = "studs",
    CurrentValue = 50,
    Flag = "JumpPower",
    Callback = function(Value)
        if Character and Character:FindFirstChild("Humanoid") then
            Character.Humanoid.JumpPower = Value
        end
    end,
})

-- Funções principais
function StartAutoFarm()
    spawn(function()
        while Settings.AutoFarm do
            task.wait(1/Settings.FarmSpeed)
            pcall(function()
                local enemies = Workspace.Enemies:GetChildren()
                for _, npc in pairs(enemies) do
                    if not Settings.AutoFarm then break end
                    if npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 then
                        -- Teleport to NPC
                        Player.Character.HumanoidRootPart.CFrame = npc.HumanoidRootPart.CFrame * CFrame.new(0, 0, Settings.AttackDistance)
                        
                        -- Attack NPC
                        AttackNPC(npc)
                        
                        -- Collect drops
                        CollectDrops()
                    end
                end
            end)
        end
    end)
end

function StartAutoQuest()
    spawn(function()
        while Settings.AutoQuest do
            task.wait(5)
            pcall(function()
                -- Accept quests automatically
                local quests = Workspace:FindFirstChild("QuestGivers")
                if quests then
                    for _, quest in pairs(quests:GetChildren()) do
                        if quest:FindFirstChild("ProximityPrompt") then
                            fireproximityprompt(quest.ProximityPrompt)
                        end
                    end
                end
            end)
        end
    end)
end

function StartAutoBoss()
    spawn(function()
        while Settings.AutoBoss do
            task.wait(10)
            pcall(function()
                local bosses = Workspace:FindFirstChild("Bosses")
                if bosses then
                    for _, boss in pairs(bosses:GetChildren()) do
                        if boss:FindFirstChild("Humanoid") and boss.Humanoid.Health > 0 then
                            Player.Character.HumanoidRootPart.CFrame = boss.HumanoidRootPart.CFrame * CFrame.new(0, 0, 15)
                            AttackNPC(boss)
                        end
                    end
                end
            end)
        end
    end)
end

function StartAutoSell()
    spawn(function()
        while Settings.AutoSell do
            task.wait(30)
            pcall(function()
                -- Teleport to shop and sell items
                local shop = Workspace:FindFirstChild("Shop")
                if shop then
                    Player.Character.HumanoidRootPart.CFrame = shop.HumanoidRootPart.CFrame
                    -- Add selling logic here
                end
            end)
        end
    end)
end

function StartAutoCollectFruits()
    spawn(function()
        while Settings.AutoCollectFruits do
            task.wait(5)
            pcall(function()
                local fruits = Workspace:GetChildren()
                for _, fruit in pairs(fruits) do
                    if string.find(fruit.Name:lower(), "fruit") and fruit:IsA("Part") then
                        Player.Character.HumanoidRootPart.CFrame = fruit.CFrame
                        task.wait(0.5)
                    end
                end
            end)
        end
    end)
end

function AttackNPC(npc)
    -- Combat attacks
    if Settings.SelectedWeapon == "Combat" then
        local args = {
            [1] = npc.HumanoidRootPart.Position,
            [2] = npc
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("Combat"):FireServer(unpack(args))
    
    -- Sword attacks
    elseif Settings.SelectedWeapon == "Sword" then
        local args = {
            [1] = npc.HumanoidRootPart.Position,
            [2] = npc
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("Sword"):FireServer(unpack(args))
    
    -- Gun attacks
    elseif Settings.SelectedWeapon == "Gun" then
        local args = {
            [1] = npc.HumanoidRootPart.Position,
            [2] = npc
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("Gun"):FireServer(unpack(args))
    
    -- Fruit attacks
    elseif Settings.SelectedWeapon == "Fruit" then
        local args = {
            [1] = npc.HumanoidRootPart.Position,
            [2] = npc
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("Fruit"):FireServer(unpack(args))
    end
    
    -- Melee click
    VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 0)
    task.wait(0.1)
    VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 0)
end

function CollectDrops()
    pcall(function()
        local drops = Workspace:GetChildren()
        for _, item in pairs(drops) do
            if item:IsA("Tool") or item:IsA("Part") then
                if (item.Position - Player.Character.HumanoidRootPart.Position).Magnitude < 50 then
                    firetouchinterest(Player.Character.HumanoidRootPart, item, 0)
                    firetouchinterest(Player.Character.HumanoidRootPart, item, 1)
                end
            end
        end
    end)
end

function TeleportToIsland(islandName)
    local islands = {
        ["Starter Island"] = CFrame.new(100, 50, 100),
        ["Jungle"] = CFrame.new(500, 100, 500),
        -- Add more island coordinates
    }
    
    if islands[islandName] then
        Player.Character.HumanoidRootPart.CFrame = islands[islandName]
    end
end

function TeleportToBoss(bossName)
    local bosses = {
        ["The Gorilla King"] = CFrame.new(1000, 150, 1000),
        ["Bobby"] = CFrame.new(1200, 100, 800),
        -- Add more boss coordinates
    }
    
    if bosses[bossName] then
        Player.Character.HumanoidRootPart.CFrame = bosses[bossName]
    end
end

function RedeemAllCodes()
    local codes = {
        "KITT_RESET",
        "SUB2GAMERROBOT_EXP1",
        "StrawHatMaine",
        "Sub2OfficialNoobie",
        "THEGREATACE",
        "Sub2Daigrock",
        "Axiore",
        "TantaiGaming",
        "Starcodeheo"
    }
    
    for _, code in pairs(codes) do
        local args = {
            [1] = code
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):WaitForChild("Redeem"):InvokeServer(unpack(args))
        task.wait(1)
    end
end

-- Anti-AFK
Player.Idled:Connect(function()
    VirtualInputManager:SendKeyEvent(true, "F", false, game)
    task.wait(0.1)
    VirtualInputManager:SendKeyEvent(false, "F", false, game)
end)

-- Auto Rejoin if kicked
game:GetService("Players").PlayerRemoving:Connect(function(player)
    if player == Player then
        TeleportService:Teleport(game.PlaceId, Player)
    end
end)

-- Notificação de inicialização
Rayfield:Notify({
    Title = "🔮 DARK Hub Loaded",
    Content = "Welcome to Blox Fruits Script!",
    Duration = 6.5,
    Image = "rbxassetid://4483345998",
})

print("🎮 DARK Hub - Blox Fruits Script Loaded Successfully!")
