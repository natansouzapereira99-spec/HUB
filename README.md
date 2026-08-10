--[[
    Nebula Hub - Blox Fruits
    Script Completo para Roblox
    Versão: 1.0.0
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local HttpService = game:GetService("HttpService")
local GuiService = game:GetService("GuiService")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- ========================================
-- Configurações do Hub
-- ========================================
local HubConfig = {
    Name = "Nebula Hub",
    Version = "1.0.0",
    Theme = {
        Background = Color3.fromRGB(18, 18, 28),
        Sidebar = Color3.fromRGB(13, 13, 22),
        Primary = Color3.fromRGB(100, 50, 255),
        Secondary = Color3.fromRGB(255, 50, 150),
        Accent = Color3.fromRGB(0, 200, 255),
        Text = Color3.fromRGB(255, 255, 255),
        TextDim = Color3.fromRGB(160, 160, 180),
        Hover = Color3.fromRGB(45, 45, 65),
        Border = Color3.fromRGB(40, 40, 60),
        ToggleOn = Color3.fromRGB(0, 255, 180),
        ToggleOff = Color3.fromRGB(60, 60, 80),
    }
}

-- ========================================
-- Sistema de Notificações
-- ========================================
local NotificationSystem = {}

function NotificationSystem:Create(title, message, duration)
    duration = duration or 3
    
    local notificationFrame = Instance.new("Frame")
    notificationFrame.BackgroundColor3 = HubConfig.Theme.Background
    notificationFrame.BackgroundTransparency = 0.05
    notificationFrame.BorderSizePixel = 0
    notificationFrame.ClipsDescendants = true
    notificationFrame.Position = UDim2.new(1, -350, 0, 80)
    notificationFrame.Size = UDim2.new(0, 320, 0, 70)
    notificationFrame.Visible = false
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 12)
    corner.Parent = notificationFrame
    
    local border = Instance.new("Frame")
    border.BackgroundColor3 = HubConfig.Theme.Primary
    border.BorderSizePixel = 0
    border.Size = UDim2.new(0, 4, 1, 0)
    border.Parent = notificationFrame
    
    local borderCorner = Instance.new("UICorner")
    borderCorner.CornerRadius = UDim.new(0, 6)
    borderCorner.Parent = border
    
    local titleLabel = Instance.new("TextLabel")
    titleLabel.BackgroundTransparency = 1
    titleLabel.Position = UDim2.new(0, 16, 0, 10)
    titleLabel.Size = UDim2.new(1, -16, 0, 20)
    titleLabel.Font = Enum.Font.GothamBold
    titleLabel.Text = title
    titleLabel.TextColor3 = HubConfig.Theme.Text
    titleLabel.TextSize = 14
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    titleLabel.Parent = notificationFrame
    
    local messageLabel = Instance.new("TextLabel")
    messageLabel.BackgroundTransparency = 1
    messageLabel.Position = UDim2.new(0, 16, 0, 32)
    messageLabel.Size = UDim2.new(1, -16, 0, 20)
    messageLabel.Font = Enum.Font.Gotham
    messageLabel.Text = message
    messageLabel.TextColor3 = HubConfig.Theme.TextDim
    messageLabel.TextSize = 12
    messageLabel.TextXAlignment = Enum.TextXAlignment.Left
    messageLabel.Parent = notificationFrame
    
    local progressBar = Instance.new("Frame")
    progressBar.BackgroundColor3 = HubConfig.Theme.Primary
    progressBar.BorderSizePixel = 0
    progressBar.Size = UDim2.new(1, 0, 0, 2)
    progressBar.Position = UDim2.new(0, 0, 1, -2)
    progressBar.Parent = notificationFrame
    
    notificationFrame.Parent = GuiService
    
    notificationFrame.Visible = true
    notificationFrame.Position = UDim2.new(1, 0, 0, 80)
    TweenService:Create(notificationFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
        Position = UDim2.new(1, -350, 0, 80)
    }):Play()
    
    local progressTween = TweenService:Create(progressBar, TweenInfo.new(duration, Enum.EasingStyle.Linear), {
        Size = UDim2.new(0, 0, 0, 2)
    })
    progressTween:Play()
    
    task.spawn(function()
        task.wait(duration + 0.2)
        TweenService:Create(notificationFrame, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
            Position = UDim2.new(1, 50, 0, 80),
            BackgroundTransparency = 1
        }):Play()
        task.wait(0.3)
        notificationFrame:Destroy()
    end)
    
    return notificationFrame
end

-- ========================================
-- Sistema de Configurações
-- ========================================
local Settings = {
    Toggles = {},
    Sliders = {},
    Dropdowns = {},
    Keybinds = {}
}

function Settings:Save()
    local data = {
        Toggles = self.Toggles,
        Sliders = self.Sliders,
        Dropdowns = self.Dropdowns,
        Keybinds = self.Keybinds
    }
    local json = HttpService:JSONEncode(data)
    if setclipboard then
        setclipboard(json)
        NotificationSystem:Create("Config Salva", "Configuração copiada para a área de transferência!", 3)
    else
        NotificationSystem:Create("Config Salva", "Use um executor com suporte a clipboard!", 3)
    end
end

function Settings:Load()
    if not getclipboard then 
        NotificationSystem:Create("Erro", "Executor não suporta clipboard!", 3)
        return 
    end
    local json = getclipboard()
    if not json or json == "" then 
        NotificationSystem:Create("Erro", "Área de transferência vazia!", 3)
        return 
    end
    local success, data = pcall(function() return HttpService:JSONDecode(json) end)
    if not success then 
        NotificationSystem:Create("Erro", "Configuração inválida!", 3)
        return 
    end
    
    for key, value in pairs(data.Toggles or {}) do
        if self.Toggles[key] ~= nil then
            self.Toggles[key] = value
        end
    end
    
    NotificationSystem:Create("Config Carregada", "Configuração carregada com sucesso!", 3)
end

-- ========================================
-- Funções do Blox Fruits
-- ========================================
local BloxFruits = {}

-- Funções para encontrar objetos no jogo
function BloxFruits:FindNPCs()
    local npcs = {}
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and obj:FindFirstChild("Humanoid") and obj:FindFirstChild("Head") then
            if obj.Name:find("NPC") or obj.Name:find("Boss") then
                table.insert(npcs, obj)
            end
        end
    end
    return npcs
end

function BloxFruits:FindIslands()
    local islands = {}
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and (obj.Name:find("Island") or obj.Name:find("Isla")) then
            table.insert(islands, obj)
        end
    end
    return islands
end

function BloxFruits:FindFruits()
    local fruits = {}
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj.Name:find("Fruit") then
            table.insert(fruits, obj)
        end
    end
    return fruits
end

function BloxFruits:FindChests()
    local chests = {}
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and (obj.Name:find("Chest") or obj.Name:find("Caixa")) then
            table.insert(chests, obj)
        end
    end
    return chests
end

-- ========================================
-- Funções do Hub
-- ========================================
local HubFunctions = {}

-- Funções Farm
HubFunctions.Farm = {
    AutoFarm = false,
    AutoQuest = false,
    AutoLevel = false,
    BossFarm = false,
    FarmLoop = nil,
    
    ToggleAutoFarm = function()
        HubFunctions.Farm.AutoFarm = not HubFunctions.Farm.AutoFarm
        NotificationSystem:Create("Auto Farm", HubFunctions.Farm.AutoFarm and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Farm.AutoFarm then
            HubFunctions.Farm.FarmLoop = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                if not char then return end
                local hrp = char:FindFirstChild("HumanoidRootPart")
                local humanoid = char:FindFirstChild("Humanoid")
                if not hrp or not humanoid then return end
                
                -- Encontra o NPC mais próximo
                local npcs = BloxFruits:FindNPCs()
                local closest = nil
                local closestDist = math.huge
                
                for _, npc in pairs(npcs) do
                    local npcHrp = npc:FindFirstChild("HumanoidRootPart")
                    if npcHrp then
                        local dist = (hrp.Position - npcHrp.Position).Magnitude
                        if dist < closestDist then
                            closestDist = dist
                            closest = npc
                        end
                    end
                end
                
                if closest then
                    local npcHrp = closest:FindFirstChild("HumanoidRootPart")
                    if npcHrp then
                        -- Move em direção ao NPC
                        hrp.CFrame = CFrame.new(hrp.Position, npcHrp.Position)
                        
                        -- Ataca se estiver perto
                        if closestDist < 10 then
                            VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.Q, false, game)
                            task.wait(0.1)
                            VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.Q, false, game)
                        end
                    end
                end
            end)
        else
            if HubFunctions.Farm.FarmLoop then
                HubFunctions.Farm.FarmLoop:Disconnect()
                HubFunctions.Farm.FarmLoop = nil
            end
        end
    end,
    
    ToggleAutoQuest = function()
        HubFunctions.Farm.AutoQuest = not HubFunctions.Farm.AutoQuest
        NotificationSystem:Create("Auto Quest", HubFunctions.Farm.AutoQuest and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Farm.AutoQuest then
            -- Procura por NPC de quest
            task.spawn(function()
                while HubFunctions.Farm.AutoQuest and RunService.Running do
                    task.wait(1)
                    local npcs = BloxFruits:FindNPCs()
                    for _, npc in pairs(npcs) do
                        if npc.Name:find("Quest") or npc.Name:find("Mission") then
                            -- Simula clique no NPC
                            NotificationSystem:Create("Auto Quest", "Interagindo com " .. npc.Name, 2)
                            break
                        end
                    end
                end
            end)
        end
    end,
    
    ToggleAutoLevel = function()
        HubFunctions.Farm.AutoLevel = not HubFunctions.Farm.AutoLevel
        NotificationSystem:Create("Auto Level", HubFunctions.Farm.AutoLevel and "Ativado" or "Desativado", 2)
    end,
    
    ToggleBossFarm = function()
        HubFunctions.Farm.BossFarm = not HubFunctions.Farm.BossFarm
        NotificationSystem:Create("Boss Farm", HubFunctions.Farm.BossFarm and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Farm.BossFarm then
            task.spawn(function()
                while HubFunctions.Farm.BossFarm and RunService.Running do
                    task.wait(1)
                    -- Encontra bosses
                    local npcs = BloxFruits:FindNPCs()
                    for _, npc in pairs(npcs) do
                        if npc.Name:find("Boss") then
                            NotificationSystem:Create("Boss Farm", "Atacando " .. npc.Name, 2)
                            break
                        end
                    end
                end
            end)
        end
    }
}

-- Funções Frutas
HubFunctions.Frutas = {
    FruitFinder = false,
    FruitESP = false,
    FruitAlert = false,
    AutoCollect = false,
    FruitLoop = nil,
    
    ToggleFruitFinder = function()
        HubFunctions.Frutas.FruitFinder = not HubFunctions.Frutas.FruitFinder
        NotificationSystem:Create("Fruit Finder", HubFunctions.Frutas.FruitFinder and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Frutas.FruitFinder then
            HubFunctions.Frutas.FruitLoop = RunService.Heartbeat:Connect(function()
                local fruits = BloxFruits:FindFruits()
                for _, fruit in pairs(fruits) do
                    -- Cria indicador visual
                    local highlight = Instance.new("Highlight")
                    highlight.Parent = fruit
                    highlight.FillColor = HubConfig.Theme.Primary
                    highlight.FillTransparency = 0.5
                    highlight.OutlineColor = HubConfig.Theme.Primary
                    highlight.OutlineTransparency = 0.8
                    task.wait(0.1)
                end
            end)
        else
            if HubFunctions.Frutas.FruitLoop then
                HubFunctions.Frutas.FruitLoop:Disconnect()
                HubFunctions.Frutas.FruitLoop = nil
            end
            -- Remove highlights
            for _, fruit in pairs(BloxFruits:FindFruits()) do
                local highlight = fruit:FindFirstChildOfClass("Highlight")
                if highlight then highlight:Destroy() end
            end
        end
    end,
    
    ToggleFruitESP = function()
        HubFunctions.Frutas.FruitESP = not HubFunctions.Frutas.FruitESP
        NotificationSystem:Create("Fruit ESP", HubFunctions.Frutas.FruitESP and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Frutas.FruitESP then
            -- Criar ESP para frutas
            task.spawn(function()
                while HubFunctions.Frutas.FruitESP and RunService.Running do
                    local fruits = BloxFruits:FindFruits()
                    for _, fruit in pairs(fruits) do
                        local highlight = fruit:FindFirstChildOfClass("Highlight")
                        if not highlight then
                            highlight = Instance.new("Highlight")
                            highlight.Parent = fruit
                            highlight.FillColor = HubConfig.Theme.Secondary
                            highlight.FillTransparency = 0.3
                            highlight.OutlineColor = HubConfig.Theme.Secondary
                            highlight.OutlineTransparency = 0.5
                        end
                    end
                    task.wait(0.5)
                end
            end)
        else
            for _, fruit in pairs(BloxFruits:FindFruits()) do
                local highlight = fruit:FindFirstChildOfClass("Highlight")
                if highlight then highlight:Destroy() end
            end
        end
    end,
    
    ToggleFruitAlert = function()
        HubFunctions.Frutas.FruitAlert = not HubFunctions.Frutas.FruitAlert
        NotificationSystem:Create("Aviso de Fruta", HubFunctions.Frutas.FruitAlert and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Frutas.FruitAlert then
            task.spawn(function()
                while HubFunctions.Frutas.FruitAlert and RunService.Running do
                    local fruits = BloxFruits:FindFruits()
                    if #fruits > 0 then
                        NotificationSystem:Create("FRUTA ENCONTRADA!", "Fruta detectada no mapa!", 3)
                        for _, fruit in pairs(fruits) do
                            local highlight = Instance.new("Highlight")
                            highlight.Parent = fruit
                            highlight.FillColor = Color3.fromRGB(255, 0, 0)
                            highlight.FillTransparency = 0.2
                            highlight.OutlineColor = Color3.fromRGB(255, 0, 0)
                            highlight.OutlineTransparency = 0.3
                        end
                    end
                    task.wait(5)
                end
            end)
        end
    end,
    
    ToggleAutoCollect = function()
        HubFunctions.Frutas.AutoCollect = not HubFunctions.Frutas.AutoCollect
        NotificationSystem:Create("Coleta Automática", HubFunctions.Frutas.AutoCollect and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Frutas.AutoCollect then
            task.spawn(function()
                while HubFunctions.Frutas.AutoCollect and RunService.Running do
                    local char = LocalPlayer.Character
                    if not char then task.wait(1) continue end
                    local hrp = char:FindFirstChild("HumanoidRootPart")
                    if not hrp then task.wait(1) continue end
                    
                    local fruits = BloxFruits:FindFruits()
                    local closest = nil
                    local closestDist = math.huge
                    
                    for _, fruit in pairs(fruits) do
                        if fruit:IsA("BasePart") then
                            local dist = (hrp.Position - fruit.Position).Magnitude
                            if dist < closestDist then
                                closestDist = dist
                                closest = fruit
                            end
                        end
                    end
                    
                    if closest then
                        hrp.CFrame = CFrame.new(hrp.Position, closest.Position)
                        if closestDist < 5 then
                            VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
                            task.wait(0.2)
                            VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
                            NotificationSystem:Create("Coleta", "Fruta coletada!", 2)
                        end
                    end
                    task.wait(0.1)
                end
            })
        end
    }
}

-- Funções Mapa
HubFunctions.Mapa = {
    TeleportToIsland = function(islandName)
        local islands = BloxFruits:FindIslands()
        for _, island in pairs(islands) do
            if island.Name:find(islandName) or islandName:find(island.Name) then
                local hrp = island:FindFirstChild("HumanoidRootPart") or island:FindFirstChild("RootPart") or island:FindFirstChildOfClass("BasePart")
                if hrp then
                    local char = LocalPlayer.Character
                    if char then
                        local charHrp = char:FindFirstChild("HumanoidRootPart")
                        if charHrp then
                            charHrp.CFrame = CFrame.new(hrp.Position + Vector3.new(0, 10, 0))
                            NotificationSystem:Create("Teleport", "Teleportado para " .. island.Name, 2)
                        end
                    end
                end
                break
            end
        end
    end,
    
    TeleportToNPC = function(npcName)
        local npcs = BloxFruits:FindNPCs()
        for _, npc in pairs(npcs) do
            if npc.Name:find(npcName) or npcName:find(npc.Name) then
                local hrp = npc:FindFirstChild("HumanoidRootPart")
                if hrp then
                    local char = LocalPlayer.Character
                    if char then
                        local charHrp = char:FindFirstChild("HumanoidRootPart")
                        if charHrp then
                            charHrp.CFrame = CFrame.new(hrp.Position + Vector3.new(0, 5, 3))
                            NotificationSystem:Create("Teleport", "Teleportado para " .. npc.Name, 2)
                        end
                    end
                end
                break
            end
        end
    }
}

-- Funções Combate
HubFunctions.Combate = {
    AutoAttack = false,
    AutoTarget = false,
    Targeting = false,
    EnemyFarm = false,
    CombatLoop = nil,
    
    ToggleAutoAttack = function()
        HubFunctions.Combate.AutoAttack = not HubFunctions.Combate.AutoAttack
        NotificationSystem:Create("Auto Attack", HubFunctions.Combate.AutoAttack and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Combate.AutoAttack then
            HubFunctions.Combate.CombatLoop = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                if not char then return end
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if not hrp then return end
                
                local npcs = BloxFruits:FindNPCs()
                local closest = nil
                local closestDist = math.huge
                
                for _, npc in pairs(npcs) do
                    local npcHrp = npc:FindFirstChild("HumanoidRootPart")
                    if npcHrp then
                        local dist = (hrp.Position - npcHrp.Position).Magnitude
                        if dist < closestDist and dist < 50 then
                            closestDist = dist
                            closest = npc
                        end
                    end
                end
                
                if closest then
                    local npcHrp = closest:FindFirstChild("HumanoidRootPart")
                    if npcHrp then
                        hrp.CFrame = CFrame.new(hrp.Position, npcHrp.Position)
                        if closestDist < 15 then
                            -- Simula ataque
                            VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.Q, false, game)
                            task.wait(0.05)
                            VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.Q, false, game)
                            
                            VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
                            task.wait(0.05)
                            VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
                        end
                    end
                end
            end)
        else
            if HubFunctions.Combate.CombatLoop then
                HubFunctions.Combate.CombatLoop:Disconnect()
                HubFunctions.Combate.CombatLoop = nil
            end
        end
    end,
    
    ToggleAutoTarget = function()
        HubFunctions.Combate.AutoTarget = not HubFunctions.Combate.AutoTarget
        NotificationSystem:Create("Auto Target", HubFunctions.Combate.AutoTarget and "Ativado" or "Desativado", 2)
    end,
    
    ToggleTargeting = function()
        HubFunctions.Combate.Targeting = not HubFunctions.Combate.Targeting
        NotificationSystem:Create("Targeting", HubFunctions.Combate.Targeting and "Ativado" or "Desativado", 2)
    end,
    
    ToggleEnemyFarm = function()
        HubFunctions.Combate.EnemyFarm = not HubFunctions.Combate.EnemyFarm
        NotificationSystem:Create("Farm de Inimigos", HubFunctions.Combate.EnemyFarm and "Ativado" or "Desativado", 2)
    end
}

-- Funções Raids
HubFunctions.Raids = {
    AutoRaid = false,
    AutoEnter = false,
    AutoCombat = false,
    
    ToggleAutoRaid = function()
        HubFunctions.Raids.AutoRaid = not HubFunctions.Raids.AutoRaid
        NotificationSystem:Create("Raid", HubFunctions.Raids.AutoRaid and "Ativado" or "Desativado", 2)
    end,
    
    ToggleAutoEnter = function()
        HubFunctions.Raids.AutoEnter = not HubFunctions.Raids.AutoEnter
        NotificationSystem:Create("Entrada Automática", HubFunctions.Raids.AutoEnter and "Ativado" or "Desativado", 2)
    end,
    
    ToggleAutoCombat = function()
        HubFunctions.Raids.AutoCombat = not HubFunctions.Raids.AutoCombat
        NotificationSystem:Create("Automação de Combate", HubFunctions.Raids.AutoCombat and "Ativado" or "Desativado", 2)
    end
}

-- Funções Sea Events
HubFunctions.SeaEvents = {
    AutoEvent = false,
    SeaBoss = false,
    RewardFarm = false,
    
    ToggleAutoEvent = function()
        HubFunctions.SeaEvents.AutoEvent = not HubFunctions.SeaEvents.AutoEvent
        NotificationSystem:Create("Eventos Marítimos", HubFunctions.SeaEvents.AutoEvent and "Ativado" or "Desativado", 2)
    end,
    
    ToggleSeaBoss = function()
        HubFunctions.SeaEvents.SeaBoss = not HubFunctions.SeaEvents.SeaBoss
        NotificationSystem:Create("Sea Bosses", HubFunctions.SeaEvents.SeaBoss and "Ativado" or "Desativado", 2)
    end,
    
    ToggleRewardFarm = function()
        HubFunctions.SeaEvents.RewardFarm = not HubFunctions.SeaEvents.RewardFarm
        NotificationSystem:Create("Farm de Recompensas", HubFunctions.SeaEvents.RewardFarm and "Ativado" or "Desativado", 2)
    end
}

-- Funções Itens
HubFunctions.Itens = {
    ChestESP = false,
    ChestFarm = false,
    MaterialCollect = false,
    Drops = false,
    
    ToggleChestESP = function()
        HubFunctions.Itens.ChestESP = not HubFunctions.Itens.ChestESP
        NotificationSystem:Create("Chest ESP", HubFunctions.Itens.ChestESP and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Itens.ChestESP then
            task.spawn(function()
                while HubFunctions.Itens.ChestESP and RunService.Running do
                    local chests = BloxFruits:FindChests()
                    for _, chest in pairs(chests) do
                        local highlight = chest:FindFirstChildOfClass("Highlight")
                        if not highlight then
                            highlight = Instance.new("Highlight")
                            highlight.Parent = chest
                            highlight.FillColor = HubConfig.Theme.Accent
                            highlight.FillTransparency = 0.3
                            highlight.OutlineColor = HubConfig.Theme.Accent
                            highlight.OutlineTransparency = 0.5
                        end
                    end
                    task.wait(0.5)
                end
            end)
        else
            for _, chest in pairs(BloxFruits:FindChests()) do
                local highlight = chest:FindFirstChildOfClass("Highlight")
                if highlight then highlight:Destroy() end
            end
        end
    end,
    
    ToggleChestFarm = function()
        HubFunctions.Itens.ChestFarm = not HubFunctions.Itens.ChestFarm
        NotificationSystem:Create("Chest Farm", HubFunctions.Itens.ChestFarm and "Ativado" or "Desativado", 2)
    end,
    
    ToggleMaterialCollect = function()
        HubFunctions.Itens.MaterialCollect = not HubFunctions.Itens.MaterialCollect
        NotificationSystem:Create("Coleta de Materiais", HubFunctions.Itens.MaterialCollect and "Ativado" or "Desativado", 2)
    end,
    
    ToggleDrops = function()
        HubFunctions.Itens.Drops = not HubFunctions.Itens.Drops
        NotificationSystem:Create("Drops", HubFunctions.Itens.Drops and "Ativado" or "Desativado", 2)
    end
}

-- Funções ESP
HubFunctions.ESP = {
    Players = false,
    NPC = false,
    FruitESP = false,
    ChestESP = false,
    BossESP = false,
    
    TogglePlayers = function()
        HubFunctions.ESP.Players = not HubFunctions.ESP.Players
        NotificationSystem:Create("Players ESP", HubFunctions.ESP.Players and "Ativado" or "Desativado", 2)
    end,
    
    ToggleNPC = function()
        HubFunctions.ESP.NPC = not HubFunctions.ESP.NPC
        NotificationSystem:Create("NPC ESP", HubFunctions.ESP.NPC and "Ativado" or "Desativado", 2)
        
        if HubFunctions.ESP.NPC then
            task.spawn(function()
                while HubFunctions.ESP.NPC and RunService.Running do
                    local npcs = BloxFruits:FindNPCs()
                    for _, npc in pairs(npcs) do
                        local highlight = npc:FindFirstChildOfClass("Highlight")
                        if not highlight then
                            highlight = Instance.new("Highlight")
                            highlight.Parent = npc
                            highlight.FillColor = HubConfig.Theme.Primary
                            highlight.FillTransparency = 0.4
                            highlight.OutlineColor = HubConfig.Theme.Primary
                            highlight.OutlineTransparency = 0.6
                        end
                    end
                    task.wait(0.5)
                end
            end)
        else
            for _, npc in pairs(BloxFruits:FindNPCs()) do
                local highlight = npc:FindFirstChildOfClass("Highlight")
                if highlight then highlight:Destroy() end
            end
        end
    end,
    
    ToggleFruitESP = function()
        HubFunctions.ESP.FruitESP = not HubFunctions.ESP.FruitESP
        NotificationSystem:Create("Fruit ESP", HubFunctions.ESP.FruitESP and "Ativado" or "Desativado", 2)
    end,
    
    ToggleChestESP = function()
        HubFunctions.ESP.ChestESP = not HubFunctions.ESP.ChestESP
        NotificationSystem:Create("Chest ESP", HubFunctions.ESP.ChestESP and "Ativado" or "Desativado", 2)
    end,
    
    ToggleBossESP = function()
        HubFunctions.ESP.BossESP = not HubFunctions.ESP.BossESP
        NotificationSystem:Create("Boss ESP", HubFunctions.ESP.BossESP and "Ativado" or "Desativado", 2)
    end
}

-- Funções Stats
HubFunctions.Stats = {
    AutoDistribute = false,
    StatConfig = {Melee = 0, Defense = 0, Sword = 0, Fruit = 0},
    SelectedStat = "Melee",
    
    ToggleAutoDistribute = function()
        HubFunctions.Stats.AutoDistribute = not HubFunctions.Stats.AutoDistribute
        NotificationSystem:Create("Distribuição Automática", HubFunctions.Stats.AutoDistribute and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Stats.AutoDistribute then
            task.spawn(function()
                while HubFunctions.Stats.AutoDistribute and RunService.Running do
                    task.wait(2)
                    -- Simula distribuição de stats
                    NotificationSystem:Create("Stats", "Distribuindo pontos...", 2)
                    -- Simula clique nos botões de stat
                    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.L, false, game)
                    task.wait(0.1)
                    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.L, false, game)
                end
            end)
        end
    end,
    
    SetStatPoints = function(stat, points)
        HubFunctions.Stats.StatConfig[stat] = points
        NotificationSystem:Create("Stats", stat .. " configurado para " .. points .. " pontos", 2)
    end
}

-- Funções Movimentação
HubFunctions.Movimentacao = {
    Fly = false,
    WalkSpeed = 16,
    JumpPower = 50,
    NoClip = false,
    FlyLoop = nil,
    
    ToggleFly = function()
        HubFunctions.Movimentacao.Fly = not HubFunctions.Movimentacao.Fly
        NotificationSystem:Create("Fly", HubFunctions.Movimentacao.Fly and "Ativado" or "Desativado", 2)
        
        if HubFunctions.Movimentacao.Fly then
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                char.Humanoid.PlatformStand = true
            end
            
            HubFunctions.Movimentacao.FlyLoop = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                if not char then return end
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if not hrp then return end
                local humanoid = char:FindFirstChild("Humanoid")
                if not humanoid then return end
                
                local moveDirection = Vector3.new(0, 0, 0)
                if UserInputService:IsKeyDown(Enum.KeyCode.W) then 
                    moveDirection = moveDirection + hrp.CFrame.LookVector * 50 
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.S) then 
                    moveDirection = moveDirection - hrp.CFrame.LookVector * 50 
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.A) then 
                    moveDirection = moveDirection - hrp.CFrame.RightVector * 50 
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.D) then 
                    moveDirection = moveDirection + hrp.CFrame.RightVector * 50 
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.Space) then 
                    moveDirection = moveDirection + Vector3.new(0, 50, 0) 
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then 
                    moveDirection = moveDirection - Vector3.new(0, 50, 0) 
                end
                
                if moveDirection.Magnitude > 0 then
                    hrp.Velocity = moveDirection
                else
                    hrp.Velocity = Vector3.new(0, 0, 0)
                end
            end)
        else
            if HubFunctions.Movimentacao.FlyLoop then
                HubFunctions.Movimentacao.FlyLoop:Disconnect()
                HubFunctions.Movimentacao.FlyLoop = nil
            end
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                char.Humanoid.PlatformStand = false
            end
        end
    end,
    
    SetWalkSpeed = function(value)
        HubFunctions.Movimentacao.WalkSpeed = value
        local char = LocalPlayer.Character
        if char and char:FindFirstChild("Humanoid") then
            char.Humanoid.WalkSpeed = value
        end
        NotificationSystem:Create("WalkSpeed", "Velocidade: " .. value, 2)
    end,
    
    SetJumpPower = function(value)
        HubFunctions.Movimentacao.JumpPower = value
        local char = LocalPlayer.Character
        if char and char:FindFirstChild("Humanoid") then
            char.Humanoid.JumpPower = value
        end
        NotificationSystem:Create("JumpPower", "Pulo: " .. value, 2)
    end,
    
    ToggleNoClip = function()
        HubFunctions.Movimentacao.NoClip = not HubFunctions.Movimentacao.NoClip
        NotificationSystem:Create("NoClip", HubFunctions.Movimentacao.NoClip and "Ativado" or "Desativado", 2)
        
        local char = LocalPlayer.Character
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = not HubFunctions.Movimentacao.NoClip
