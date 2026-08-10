```lua
--// GTVZ KEY SYSTEM - versão corrigida
--// Sistema de interface e validação local da chave

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

--==================================================
-- CONFIG
--==================================================

local VALID_KEY = "GTVZ_FREEHS62BEU746"
local KEY_FILE = "GTVZ_Key.txt"

--==================================================
-- FUNÇÕES DE ARQUIVO
--==================================================

local function SaveKey(key)
    if writefile then
        pcall(function()
            writefile(KEY_FILE, key)
        end)
    end
end

local function LoadSavedKey()
    if isfile and readfile then
        local success, result = pcall(function()
            if isfile(KEY_FILE) then
                return readfile(KEY_FILE)
            end
        end)

        if success then
            return result
        end
    end

    return nil
end

local function IsKeyValid(key)
    return key == VALID_KEY
end

--==================================================
-- LIMPAR GUI ANTIGA
--==================================================

local oldGui = PlayerGui:FindFirstChild("GTVZ_KeySystem")

if oldGui then
    oldGui:Destroy()
end

--==================================================
-- NOTIFICAÇÃO
--==================================================

local function Notification(title, message, duration)
    local gui = Instance.new("ScreenGui")
    gui.Name = "GTVZ_Notification"
    gui.ResetOnSpawn = false
    gui.Parent = PlayerGui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 300, 0, 80)
    frame.Position = UDim2.new(1, 20, 0.1, 0)
    frame.BackgroundColor3 = Color3.fromRGB(8, 8, 8)
    frame.Parent = gui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = frame

    local stroke = Instance.new("UIStroke")
    stroke.Color = Color3.fromRGB(0, 255, 0)
    stroke.Thickness = 1
    stroke.Parent = frame

    local titleLabel = Instance.new("TextLabel")
    titleLabel.BackgroundTransparency = 1
    titleLabel.Position = UDim2.new(0, 15, 0, 8)
    titleLabel.Size = UDim2.new(1, -30, 0, 22)
    titleLabel.Font = Enum.Font.GothamBold
    titleLabel.Text = title
    titleLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    titleLabel.TextSize = 14
    titleLabel.TextXAlignment = Enum.TextXAlignment.Left
    titleLabel.Parent = frame

    local messageLabel = Instance.new("TextLabel")
    messageLabel.BackgroundTransparency = 1
    messageLabel.Position = UDim2.new(0, 15, 0, 32)
    messageLabel.Size = UDim2.new(1, -30, 0, 35)
    messageLabel.Font = Enum.Font.Gotham
    messageLabel.Text = message
    messageLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
    messageLabel.TextSize = 12
    messageLabel.TextWrapped = true
    messageLabel.TextXAlignment = Enum.TextXAlignment.Left
    messageLabel.Parent = frame

    TweenService:Create(
        frame,
        TweenInfo.new(0.35, Enum.EasingStyle.Quart),
        {Position = UDim2.new(1, -320, 0.1, 0)}
    ):Play()

    task.delay(duration or 3, function()
        if not gui.Parent then
            return
        end

        local tween = TweenService:Create(
            frame,
            TweenInfo.new(0.3, Enum.EasingStyle.Quart),
            {Position = UDim2.new(1, 20, 0.1, 0)}
        )

        tween:Play()
        tween.Completed:Wait()

        gui:Destroy()
    end)
end

--==================================================
-- KEY GUI
--==================================================

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "GTVZ_KeySystem"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = PlayerGui

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.new(0, 404, 0, 206)
Main.Position = UDim2.new(0.5, -202, 0.5, -103)
Main.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Main.Active = true
Main.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 8)
MainCorner.Parent = Main

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(0, 255, 0)
MainStroke.Thickness = 2
MainStroke.Parent = Main

--==================================================
-- TÍTULO
--==================================================

local Title = Instance.new("TextLabel")
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 0, 0, 5)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Font = Enum.Font.GothamBold
Title.Text = "GTVZ KEY SYSTEM"
Title.TextColor3 = Color3.fromRGB(0, 255, 0)
Title.TextSize = 17
Title.Parent = Main

local Line = Instance.new("Frame")
Line.BorderSizePixel = 0
Line.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
Line.Position = UDim2.new(0, 10, 0, 42)
Line.Size = UDim2.new(1, -20, 0, 2)
Line.Parent = Main

--==================================================
-- TEXTBOX
--==================================================

local KeyBox = Instance.new("TextBox")
KeyBox.Name = "KeyBox"
KeyBox.BackgroundColor3 = Color3.fromRGB(8, 8, 8)
KeyBox.Position = UDim2.new(0, 100, 0, 60)
KeyBox.Size = UDim2.new(0, 204, 0, 42)
KeyBox.Font = Enum.Font.Gotham
KeyBox.PlaceholderText = "CHAVE 🔑"
KeyBox.Text = ""
KeyBox.TextColor3 = Color3.fromRGB(0, 255, 0)
KeyBox.PlaceholderColor3 = Color3.fromRGB(100, 100, 100)
KeyBox.TextSize = 13
KeyBox.ClearTextOnFocus = false
KeyBox.Parent = Main

local KeyCorner = Instance.new("UICorner")
KeyCorner.CornerRadius = UDim.new(0, 5)
KeyCorner.Parent = KeyBox

local KeyStroke = Instance.new("UIStroke")
KeyStroke.Color = Color3.fromRGB(0, 255, 0)
KeyStroke.Thickness = 1
KeyStroke.Parent = KeyBox

--==================================================
-- BOTÃO PEGAR CHAVE
--==================================================

local GetKey = Instance.new("TextButton")
GetKey.Name = "GetKey"
GetKey.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
GetKey.Position = UDim2.new(0, 100, 0, 112)
GetKey.Size = UDim2.new(0, 98, 0, 38)
GetKey.Font = Enum.Font.GothamBold
GetKey.Text = "PEGAR CHAVE"
GetKey.TextColor3 = Color3.fromRGB(0, 255, 0)
GetKey.TextSize = 11
GetKey.Parent = Main

local GetCorner = Instance.new("UICorner")
GetCorner.CornerRadius = UDim.new(0, 5)
GetCorner.Parent = GetKey

local GetStroke = Instance.new("UIStroke")
GetStroke.Color = Color3.fromRGB(0, 255, 0)
GetStroke.Parent = GetKey

GetKey.MouseButton1Click:Connect(function()
    local link = "https://link-target.net/1344304/gtvz-hub"

    if setclipboard then
        pcall(function()
            setclipboard(link)
        end)

        Notification(
            "CHAVE 🔑",
            "Link copiado para a área de transferência.",
            3
        )
    else
        Notification(
            "CHAVE 🔑",
            "Copie o link manualmente: " .. link,
            5
        )
    end
end)

--==================================================
-- BOTÃO VALIDAR
--==================================================

local Submit = Instance.new("TextButton")
Submit.Name = "Submit"
Submit.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Submit.Position = UDim2.new(0, 206, 0, 112)
Submit.Size = UDim2.new(0, 98, 0, 38)
Submit.Font = Enum.Font.GothamBold
Submit.Text = "CHECAR CHAVE"
Submit.TextColor3 = Color3.fromRGB(0, 255, 0)
Submit.TextSize = 11
Submit.Parent = Main

local SubmitCorner = Instance.new("UICorner")
SubmitCorner.CornerRadius = UDim.new(0, 5)
SubmitCorner.Parent = Submit

local SubmitStroke = Instance.new("UIStroke")
SubmitStroke.Color = Color3.fromRGB(0, 255, 0)
SubmitStroke.Parent = Submit

Submit.MouseButton1Click:Connect(function()
    local inputKey = KeyBox.Text

    if IsKeyValid(inputKey) then
        SaveKey(inputKey)

        Notification(
            "Key System",
            "CHAVE VÁLIDA! Salva com sucesso.",
            3
        )

        task.wait(2)

        ScreenGui:Destroy()

        -- Coloque aqui somente uma função legítima
        -- do seu próprio projeto, se necessário.

    else
        Notification(
            "CHAVE 🔑",
            "CHAVE INVÁLIDA!",
            4
        )
    end
end)

--==================================================
-- DISCORD
--==================================================

local Discord = Instance.new("TextButton")
Discord.Name = "Discord"
Discord.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Discord.Position = UDim2.new(0, 119, 0, 158)
Discord.Size = UDim2.new(0, 166, 0, 31)
Discord.Font = Enum.Font.GothamBold
Discord.Text = "DISCORD"
Discord.TextColor3 = Color3.fromRGB(0, 255, 0)
Discord.TextSize = 13
Discord.Parent = Main

local DiscordCorner = Instance.new("UICorner")
DiscordCorner.CornerRadius = UDim.new(0, 5)
DiscordCorner.Parent = Discord

local DiscordStroke = Instance.new("UIStroke")
DiscordStroke.Color = Color3.fromRGB(0, 255, 0)
DiscordStroke.Parent = Discord

Discord.MouseButton1Click:Connect(function()
    local link = "https://discord.com/invite/Cg4fDkn6un"

    if setclipboard then
        pcall(function()
            setclipboard(link)
        end)

        Notification(
            "DISCORD",
            "Link do Discord copiado!",
            4
        )
    else
        Notification(
            "DISCORD",
            "Link: " .. link,
            5
        )
    end
end)

--==================================================
-- CARREGAR CHAVE SALVA
--==================================================

local savedKey = LoadSavedKey()

if savedKey and IsKeyValid(savedKey) then
    Notification(
        "Key System",
        "KEY VÁLIDA ENCONTRADA!",
        4
    )

    task.wait(2)

    ScreenGui:Destroy()

    -- Chave válida.
    -- O carregamento de código externo foi removido.
end
```
