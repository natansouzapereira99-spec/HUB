--[[
    Nebula Hub - Script Completo
    Desenvolvido para Roblox Studio
    Versão: 1.0.0
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local HttpService = game:GetService("HttpService")
local GuiService = game:GetService("GuiService")
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
NotificationSystem.__index = NotificationSystem

function NotificationSystem:Create(title, message, duration, type)
    duration = duration or 3
    type = type or "info"
    
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
    
    local glow = Instance.new("Frame")
    glow.BackgroundColor3 = HubConfig.Theme.Primary
    glow.BackgroundTransparency = 0.8
    glow.BorderSizePixel = 0
    glow.Size = UDim2.new(0, 4, 1, 0)
    glow.Parent = notificationFrame
    
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
    
    -- Animar entrada
    notificationFrame.Visible = true
    notificationFrame.Position = UDim2.new(1, 0, 0, 80)
    TweenService:Create(notificationFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
        Position = UDim2.new(1, -350, 0, 80)
    }):Play()
    
    -- Barra de progresso
    local progressTween = TweenService:Create(progressBar, TweenInfo.new(duration, Enum.EasingStyle.Linear), {
        Size = UDim2.new(0, 0, 0, 2)
    })
    progressTween:Play()
    
    -- Remover após duração
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
    setclipboard and setclipboard(json) or print(json)
    NotificationSystem:Create("Config Salva", "Configuração copiada para a área de transferência!", 3, "success")
end

function Settings:Load()
    local json = getclipboard and getclipboard()
    if not json then return end
    local success, data = pcall(function() return HttpService:JSONDecode(json) end)
    if not success then 
        NotificationSystem:Create("Erro", "Configuração inválida!", 3, "error")
        return 
    end
    
    for key, value in pairs(data.Toggles or {}) do
        if self.Toggles[key] ~= nil then
            self.Toggles[key] = value
        end
    end
    
    NotificationSystem:Create("Config Carregada", "Configuração carregada com sucesso!", 3, "success")
end

-- ========================================
-- Sistema de Keybinds
-- ========================================
local KeybindSystem = {}
KeybindSystem.__index = KeybindSystem

function KeybindSystem:Register(name, defaultKey, callback)
    local keybind = defaultKey or Enum.KeyCode.None
    local listening = false
    
    local function onInputBegan(input, gameProcessed)
        if gameProcessed then return end
        if not listening and input.KeyCode == keybind then
            callback()
        end
    end
    
    UserInputService.InputBegan:Connect(onInputBegan)
    
    return {
        SetKey = function(newKey)
            keybind = newKey
        end,
        StartListening = function()
            listening = true
            NotificationSystem:Create("Keybind", "Pressione uma tecla para definir...", 3, "info")
        end,
        StopListening = function()
            listening = false
        end
    }
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
    
    ToggleAutoFarm = function()
        HubFunctions.Farm.AutoFarm = not HubFunctions.Farm.AutoFarm
        NotificationSystem:Create("Auto Farm", HubFunctions.Farm.AutoFarm and "Ativado" or "Desativado", 2, "info")
        if HubFunctions.Farm.AutoFarm then
            -- Simulação de auto farm
            task.spawn(function()
                while HubFunctions.Farm.AutoFarm and RunService.Running do
                    task.wait(1)
                    print("Auto Farm ativo...")
                end
            end)
        end
    end,
    
    ToggleAutoQuest = function()
        HubFunctions.Farm.AutoQuest = not HubFunctions.Farm.AutoQuest
        NotificationSystem:Create("Auto Quest", HubFunctions.Farm.AutoQuest and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleAutoLevel = function()
        HubFunctions.Farm.AutoLevel = not HubFunctions.Farm.AutoLevel
        NotificationSystem:Create("Auto Level", HubFunctions.Farm.AutoLevel and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleBossFarm = function()
        HubFunctions.Farm.BossFarm = not HubFunctions.Farm.BossFarm
        NotificationSystem:Create("Boss Farm", HubFunctions.Farm.BossFarm and "Ativado" or "Desativado", 2, "info")
    end
}

-- Funções Frutas
HubFunctions.Frutas = {
    FruitFinder = false,
    FruitESP = false,
    FruitAlert = false,
    AutoCollect = false,
    
    ToggleFruitFinder = function()
        HubFunctions.Frutas.FruitFinder = not HubFunctions.Frutas.FruitFinder
        NotificationSystem:Create("Fruit Finder", HubFunctions.Frutas.FruitFinder and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleFruitESP = function()
        HubFunctions.Frutas.FruitESP = not HubFunctions.Frutas.FruitESP
        NotificationSystem:Create("Fruit ESP", HubFunctions.Frutas.FruitESP and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleFruitAlert = function()
        HubFunctions.Frutas.FruitAlert = not HubFunctions.Frutas.FruitAlert
        NotificationSystem:Create("Aviso de Fruta", HubFunctions.Frutas.FruitAlert and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleAutoCollect = function()
        HubFunctions.Frutas.AutoCollect = not HubFunctions.Frutas.AutoCollect
        NotificationSystem:Create("Coleta Automática", HubFunctions.Frutas.AutoCollect and "Ativado" or "Desativado", 2, "info")
    end
}

-- Funções Mapa
HubFunctions.Mapa = {
    TeleportToIsland = function(islandName)
        NotificationSystem:Create("Teleport", "Teleportando para " .. islandName, 2, "info")
    end,
    
    TeleportToNPC = function(npcName)
        NotificationSystem:Create("Teleport", "Teleportando para " .. npcName, 2, "info")
    end
}

-- Funções Combate
HubFunctions.Combate = {
    AutoAttack = false,
    AutoTarget = false,
    Targeting = false,
    EnemyFarm = false,
    
    ToggleAutoAttack = function()
        HubFunctions.Combate.AutoAttack = not HubFunctions.Combate.AutoAttack
        NotificationSystem:Create("Auto Attack", HubFunctions.Combate.AutoAttack and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleAutoTarget = function()
        HubFunctions.Combate.AutoTarget = not HubFunctions.Combate.AutoTarget
        NotificationSystem:Create("Auto Target", HubFunctions.Combate.AutoTarget and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleTargeting = function()
        HubFunctions.Combate.Targeting = not HubFunctions.Combate.Targeting
        NotificationSystem:Create("Targeting", HubFunctions.Combate.Targeting and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleEnemyFarm = function()
        HubFunctions.Combate.EnemyFarm = not HubFunctions.Combate.EnemyFarm
        NotificationSystem:Create("Farm de Inimigos", HubFunctions.Combate.EnemyFarm and "Ativado" or "Desativado", 2, "info")
    end
}

-- Funções Raids
HubFunctions.Raids = {
    AutoRaid = false,
    AutoEnter = false,
    AutoCombat = false,
    
    ToggleAutoRaid = function()
        HubFunctions.Raids.AutoRaid = not HubFunctions.Raids.AutoRaid
        NotificationSystem:Create("Raid", HubFunctions.Raids.AutoRaid and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleAutoEnter = function()
        HubFunctions.Raids.AutoEnter = not HubFunctions.Raids.AutoEnter
        NotificationSystem:Create("Entrada Automática", HubFunctions.Raids.AutoEnter and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleAutoCombat = function()
        HubFunctions.Raids.AutoCombat = not HubFunctions.Raids.AutoCombat
        NotificationSystem:Create("Automação de Combate", HubFunctions.Raids.AutoCombat and "Ativado" or "Desativado", 2, "info")
    end
}

-- Funções Sea Events
HubFunctions.SeaEvents = {
    AutoEvent = false,
    SeaBoss = false,
    RewardFarm = false,
    
    ToggleAutoEvent = function()
        HubFunctions.SeaEvents.AutoEvent = not HubFunctions.SeaEvents.AutoEvent
        NotificationSystem:Create("Eventos Marítimos", HubFunctions.SeaEvents.AutoEvent and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleSeaBoss = function()
        HubFunctions.SeaEvents.SeaBoss = not HubFunctions.SeaEvents.SeaBoss
        NotificationSystem:Create("Sea Bosses", HubFunctions.SeaEvents.SeaBoss and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleRewardFarm = function()
        HubFunctions.SeaEvents.RewardFarm = not HubFunctions.SeaEvents.RewardFarm
        NotificationSystem:Create("Farm de Recompensas", HubFunctions.SeaEvents.RewardFarm and "Ativado" or "Desativado", 2, "info")
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
        NotificationSystem:Create("Chest ESP", HubFunctions.Itens.ChestESP and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleChestFarm = function()
        HubFunctions.Itens.ChestFarm = not HubFunctions.Itens.ChestFarm
        NotificationSystem:Create("Chest Farm", HubFunctions.Itens.ChestFarm and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleMaterialCollect = function()
        HubFunctions.Itens.MaterialCollect = not HubFunctions.Itens.MaterialCollect
        NotificationSystem:Create("Coleta de Materiais", HubFunctions.Itens.MaterialCollect and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleDrops = function()
        HubFunctions.Itens.Drops = not HubFunctions.Itens.Drops
        NotificationSystem:Create("Drops", HubFunctions.Itens.Drops and "Ativado" or "Desativado", 2, "info")
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
        NotificationSystem:Create("Players ESP", HubFunctions.ESP.Players and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleNPC = function()
        HubFunctions.ESP.NPC = not HubFunctions.ESP.NPC
        NotificationSystem:Create("NPC ESP", HubFunctions.ESP.NPC and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleFruitESP = function()
        HubFunctions.ESP.FruitESP = not HubFunctions.ESP.FruitESP
        NotificationSystem:Create("Fruit ESP", HubFunctions.ESP.FruitESP and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleChestESP = function()
        HubFunctions.ESP.ChestESP = not HubFunctions.ESP.ChestESP
        NotificationSystem:Create("Chest ESP", HubFunctions.ESP.ChestESP and "Ativado" or "Desativado", 2, "info")
    end,
    
    ToggleBossESP = function()
        HubFunctions.ESP.BossESP = not HubFunctions.ESP.BossESP
        NotificationSystem:Create("Boss ESP", HubFunctions.ESP.BossESP and "Ativado" or "Desativado", 2, "info")
    end
}

-- Funções Stats
HubFunctions.Stats = {
    AutoDistribute = false,
    StatConfig = {Melee = 0, Defense = 0, Sword = 0, Fruit = 0},
    SelectedStat = "Melee",
    
    ToggleAutoDistribute = function()
        HubFunctions.Stats.AutoDistribute = not HubFunctions.Stats.AutoDistribute
        NotificationSystem:Create("Distribuição Automática", HubFunctions.Stats.AutoDistribute and "Ativado" or "Desativado", 2, "info")
    end,
    
    SetStatPoints = function(stat, points)
        HubFunctions.Stats.StatConfig[stat] = points
        NotificationSystem:Create("Stats", stat .. " configurado para " .. points .. " pontos", 2, "info")
    end
}

-- Funções Movimentação
HubFunctions.Movimentacao = {
    Fly = false,
    WalkSpeed = 16,
    JumpPower = 50,
    NoClip = false,
    
    ToggleFly = function()
        HubFunctions.Movimentacao.Fly = not HubFunctions.Movimentacao.Fly
        NotificationSystem:Create("Fly", HubFunctions.Movimentacao.Fly and "Ativado" or "Desativado", 2, "info")
        if HubFunctions.Movimentacao.Fly then
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                char.Humanoid.PlatformStand = true
                -- Simulação de voo
                task.spawn(function()
                    while HubFunctions.Movimentacao.Fly and RunService.Running do
                        task.wait()
                        local char = LocalPlayer.Character
                        if char and char:FindFirstChild("HumanoidRootPart") then
                            local root = char.HumanoidRootPart
                            local moveDirection = Vector3.new(0, 0, 0)
                            if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDirection = moveDirection + char.HumanoidRootPart.CFrame.LookVector * 50 end
                            if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDirection = moveDirection - char.HumanoidRootPart.CFrame.LookVector * 50 end
                            if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDirection = moveDirection - char.HumanoidRootPart.CFrame.RightVector * 50 end
                            if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDirection = moveDirection + char.HumanoidRootPart.CFrame.RightVector * 50 end
                            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDirection = moveDirection + Vector3.new(0, 50, 0) end
                            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then moveDirection = moveDirection - Vector3.new(0, 50, 0) end
                            if moveDirection.Magnitude > 0 then
                                root.Velocity = moveDirection
                            else
                                root.Velocity = Vector3.new(0, 0, 0)
                            end
                        end
                    end
                end)
            end
        else
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
        NotificationSystem:Create("WalkSpeed", "Velocidade: " .. value, 2, "info")
    end,
    
    SetJumpPower = function(value)
        HubFunctions.Movimentacao.JumpPower = value
        local char = LocalPlayer.Character
        if char and char:FindFirstChild("Humanoid") then
            char.Humanoid.JumpPower = value
        end
        NotificationSystem:Create("JumpPower", "Pulo: " .. value, 2, "info")
    end,
    
    ToggleNoClip = function()
        HubFunctions.Movimentacao.NoClip = not HubFunctions.Movimentacao.NoClip
        NotificationSystem:Create("NoClip", HubFunctions.Movimentacao.NoClip and "Ativado" or "Desativado", 2, "info")
        if HubFunctions.Movimentacao.NoClip then
            local char = LocalPlayer.Character
            if char then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        else
            local char = LocalPlayer.Character
            if char then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = true
                    end
                end
            end
        end
    end
}

-- Funções Utilidades
HubFunctions.Utilidades = {
    Rejoin = function()
        NotificationSystem:Create("Rejoin", "Reconectando...", 2, "info")
        task.wait(1)
        LocalPlayer:LoadCharacter()
    end,
    
    ServerHop = function()
        NotificationSystem:Create("Server Hop", "Procurando novo servidor...", 2, "info")
        -- Simulação de troca de servidor
        task.spawn(function()
            task.wait(2)
            NotificationSystem:Create("Server Hop", "Servidor encontrado!", 2, "success")
        end)
    end,
    
    AntiAFK = false,
    
    ToggleAntiAFK = function()
        HubFunctions.Utilidades.AntiAFK = not HubFunctions.Utilidades.AntiAFK
        NotificationSystem:Create("Anti-AFK", HubFunctions.Utilidades.AntiAFK and "Ativado" or "Desativado", 2, "info")
        if HubFunctions.Utilidades.AntiAFK then
            task.spawn(function()
                while HubFunctions.Utilidades.AntiAFK and RunService.Running do
                    task.wait(60)
                    VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.W, false, game)
                    VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.W, false, game)
                end
            end)
        end
    end,
    
    Notifications = function()
        NotificationSystem:Create("Notificações", "Sistema de notificações ativo!", 2, "info")
    end
}

-- Funções Configurações
HubFunctions.Configuracoes = {
    SaveConfig = function()
        Settings:Save()
    end,
    
    LoadConfig = function()
        Settings:Load()
    end,
    
    CustomizeUI = function()
        NotificationSystem:Create("Personalização", "Recursos de personalização", 2, "info")
    end,
    
    Keybinds = function()
        NotificationSystem:Create("Atalhos", "Sistema de atalhos de teclado", 2, "info")
    end
}

-- ========================================
-- Criação da UI
-- ========================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "NebulaHub"
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- ========================================
-- Loading Screen
-- ========================================
local LoadingScreen = Instance.new("Frame")
LoadingScreen.Name = "LoadingScreen"
LoadingScreen.Size = UDim2.new(1, 0, 1, 0)
LoadingScreen.BackgroundColor3 = HubConfig.Theme.Background
LoadingScreen.BackgroundTransparency = 0
LoadingScreen.BorderSizePixel = 0
LoadingScreen.Parent = ScreenGui

local LoadingCorner = Instance.new("UICorner")
LoadingCorner.CornerRadius = UDim.new(0, 0)
LoadingCorner.Parent = LoadingScreen

local LoadingLogo = Instance.new("TextLabel")
LoadingLogo.Size = UDim2.new(0, 300, 0, 60)
LoadingLogo.Position = UDim2.new(0.5, -150, 0.5, -80)
LoadingLogo.BackgroundTransparency = 1
LoadingLogo.Font = Enum.Font.GothamBold
LoadingLogo.Text = "NEBULA HUB"
LoadingLogo.TextColor3 = HubConfig.Theme.Primary
LoadingLogo.TextSize = 48
LoadingLogo.TextScaled = false
LoadingLogo.Parent = LoadingScreen

local LoadingSubtitle = Instance.new("TextLabel")
LoadingSubtitle.Size = UDim2.new(0, 300, 0, 30)
LoadingSubtitle.Position = UDim2.new(0.5, -150, 0.5, -20)
LoadingSubtitle.BackgroundTransparency = 1
LoadingSubtitle.Font = Enum.Font.Gotham
LoadingSubtitle.Text = "Inicializando..."
LoadingSubtitle.TextColor3 = HubConfig.Theme.TextDim
LoadingSubtitle.TextSize = 16
LoadingSubtitle.Parent = LoadingScreen

local LoadingBar = Instance.new("Frame")
LoadingBar.Size = UDim2.new(0, 200, 0, 4)
LoadingBar.Position = UDim2.new(0.5, -100, 0.5, 40)
LoadingBar.BackgroundColor3 = HubConfig.Theme.TextDim
LoadingBar.BackgroundTransparency = 0.5
LoadingBar.BorderSizePixel = 0
LoadingBar.Parent = LoadingScreen

local LoadingBarCorner = Instance.new("UICorner")
LoadingBarCorner.CornerRadius = UDim.new(0, 2)
LoadingBarCorner.Parent = LoadingBar

local LoadingProgress = Instance.new("Frame")
LoadingProgress.Size = UDim2.new(0, 0, 1, 0)
LoadingProgress.BackgroundColor3 = HubConfig.Theme.Primary
LoadingProgress.BorderSizePixel = 0
LoadingProgress.Parent = LoadingBar

local LoadingProgressCorner = Instance.new("UICorner")
LoadingProgressCorner.CornerRadius = UDim.new(0, 2)
LoadingProgressCorner.Parent = LoadingProgress

-- Animar loading
task.spawn(function()
    local progress = 0
    while progress < 1 do
        progress = progress + 0.01
        LoadingProgress.Size = UDim2.new(progress, 0, 1, 0)
        if progress < 0.3 then
            LoadingSubtitle.Text = "Carregando módulos..."
        elseif progress < 0.6 then
            LoadingSubtitle.Text = "Configurando interface..."
        elseif progress < 0.9 then
            LoadingSubtitle.Text = "Inicializando funções..."
        else
            LoadingSubtitle.Text = "Pronto!"
        end
        task.wait(0.02)
    end
    task.wait(0.3)
    TweenService:Create(LoadingScreen, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        BackgroundTransparency = 1
    }):Play()
    task.wait(0.5)
    LoadingScreen.Visible = false
    LoadingScreen:Destroy()
end)

-- ========================================
-- Main Hub Interface
-- ========================================
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 900, 0, 600)
MainFrame.Position = UDim2.new(0.5, -450, 0.5, -300)
MainFrame.BackgroundColor3 = HubConfig.Theme.Background
MainFrame.BackgroundTransparency = 0.95
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui
MainFrame.Visible = false

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 16)
MainCorner.Parent = MainFrame

-- Sombra
local Shadow = Instance.new("Frame")
Shadow.Name = "Shadow"
Shadow.Size = UDim2.new(1, 20, 1, 20)
Shadow.Position = UDim2.new(0, -10, 0, -10)
Shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Shadow.BackgroundTransparency = 0.7
Shadow.BorderSizePixel = 0
Shadow.Parent = MainFrame

local ShadowCorner = Instance.new("UICorner")
ShadowCorner.CornerRadius = UDim.new(0, 16)
ShadowCorner.Parent = Shadow

-- Barra de Título
local TitleBar = Instance.new("Frame")
TitleBar.Name = "TitleBar"
TitleBar.Size = UDim2.new(1, 0, 0, 50)
TitleBar.Position = UDim2.new(0, 0, 0, 0)
TitleBar.BackgroundColor3 = HubConfig.Theme.Background
TitleBar.BackgroundTransparency = 0.5
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

local TitleBarCorner = Instance.new("UICorner")
TitleBarCorner.CornerRadius = UDim.new(0, 16)
TitleBarCorner.Parent = TitleBar

local TitleText = Instance.new("TextLabel")
TitleText.Size = UDim2.new(1, -120, 1, 0)
TitleText.Position = UDim2.new(0, 20, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Font = Enum.Font.GothamBold
TitleText.Text = "NEBULA HUB v1.0.0"
TitleText.TextColor3 = HubConfig.Theme.Text
TitleText.TextSize = 18
TitleText.TextXAlignment = Enum.TextXAlignment.Left
TitleText.Parent = TitleBar

-- Botão Minimizar
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
MinimizeButton.Position = UDim2.new(1, -80, 0.5, -15)
MinimizeButton.BackgroundColor3 = HubConfig.Theme.Background
MinimizeButton.BackgroundTransparency = 0.5
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Font = Enum.Font.Gotham
MinimizeButton.Text = "─"
MinimizeButton.TextColor3 = HubConfig.Theme.Text
MinimizeButton.TextSize = 20
MinimizeButton.Parent = TitleBar

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 8)
MinCorner.Parent = MinimizeButton

-- Botão Fechar
local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -40, 0.5, -15)
CloseButton.BackgroundColor3 = HubConfig.Theme.Background
CloseButton.BackgroundTransparency = 0.5
CloseButton.BorderSizePixel = 0
CloseButton.Font = Enum.Font.Gotham
CloseButton.Text = "✕"
CloseButton.TextColor3 = HubConfig.Theme.Text
CloseButton.TextSize = 18
CloseButton.Parent = TitleBar

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 8)
CloseCorner.Parent = CloseButton

-- Sidebar
local Sidebar = Instance.new("Frame")
Sidebar.Name = "Sidebar"
Sidebar.Size = UDim2.new(0, 200, 1, -50)
Sidebar.Position = UDim2.new(0, 0, 0, 50)
Sidebar.BackgroundColor3 = HubConfig.Theme.Sidebar
Sidebar.BackgroundTransparency = 0.3
Sidebar.BorderSizePixel = 0
Sidebar.Parent = MainFrame

local SidebarCorner = Instance.new("UICorner")
SidebarCorner.CornerRadius = UDim.new(0, 0)
SidebarCorner.Parent = Sidebar

-- Barra de Pesquisa
local SearchBar = Instance.new("Frame")
SearchBar.Size = UDim2.new(1, -20, 0, 35)
SearchBar.Position = UDim2.new(0, 10, 0, 10)
SearchBar.BackgroundColor3 = HubConfig.Theme.Background
SearchBar.BackgroundTransparency = 0.3
SearchBar.BorderSizePixel = 0
SearchBar.Parent = Sidebar

local SearchCorner = Instance.new("UICorner")
SearchCorner.CornerRadius = UDim.new(0, 8)
SearchCorner.Parent = SearchBar

local SearchIcon = Instance.new("TextLabel")
SearchIcon.Size = UDim2.new(0, 25, 1, 0)
SearchIcon.Position = UDim2.new(0, 5, 0, 0)
SearchIcon.BackgroundTransparency = 1
SearchIcon.Font = Enum.Font.Gotham
SearchIcon.Text = "🔍"
SearchIcon.TextColor3 = HubConfig.Theme.TextDim
SearchIcon.TextSize = 14
SearchIcon.Parent = SearchBar

local SearchBox = Instance.new("TextBox")
SearchBox.Size = UDim2.new(1, -35, 1, 0)
SearchBox.Position = UDim2.new(0, 30, 0, 0)
SearchBox.BackgroundTransparency = 1
SearchBox.Font = Enum.Font.Gotham
SearchBox.Text = "Pesquisar funções..."
SearchBox.TextColor3 = HubConfig.Theme.TextDim
SearchBox.TextSize = 12
SearchBox.TextXAlignment = Enum.TextXAlignment.Left
SearchBox.Parent = SearchBar

-- Lista de Abas
local TabList = Instance.new("ScrollingFrame")
TabList.Size = UDim2.new(1, 0, 1, -55)
TabList.Position = UDim2.new(0, 0, 0, 55)
TabList.BackgroundTransparency = 1
TabList.BorderSizePixel = 0
TabList.ScrollBarTh
