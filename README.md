--// ANIME MYSTERY - ADMIN / TEST HUB
--// Coloque em StarterPlayer > StarterPlayerScripts
--// Interface criada 100% por código

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

--==================================================
-- CONFIG
--==================================================

local Config = {
	WalkSpeed = 16,
	JumpPower = 50,

	AutoFarm = false,
	AutoQuest = false,
	BossFarm = false,
	AutoAttack = false,
	AutoStats = false,

	PlayerESP = false,
	NPCESP = false,
	FruitESP = false,
	ChestESP = false,
	BossESP = false,

	Fly = false,
	NoClip = false,

	Notifications = true,
	AntiAFK = true,
}

--==================================================
-- GUI
--==================================================

local old = playerGui:FindFirstChild("MysteryHub")
if old then
	old:Destroy()
end

local gui = Instance.new("ScreenGui")
gui.Name = "MysteryHub"
gui.ResetOnSpawn = false
gui.Parent = playerGui

--==================================================
-- MAIN
--==================================================

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.fromOffset(850, 520)
Main.Position = UDim2.new(0.5, -425, 0.5, -260)
Main.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
Main.BorderSizePixel = 0
Main.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = Main

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(100, 50, 180)
stroke.Thickness = 2
stroke.Parent = Main

--==================================================
-- TOP BAR
--==================================================

local Top = Instance.new("Frame")
Top.Size = UDim2.new(1, 0, 0, 60)
Top.BackgroundColor3 = Color3.fromRGB(22, 22, 30)
Top.BorderSizePixel = 0
Top.Parent = Main

local TopCorner = Instance.new("UICorner")
TopCorner.CornerRadius = UDim.new(0, 12)
TopCorner.Parent = Top

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -140, 1, 0)
Title.Position = UDim2.fromOffset(20, 0)
Title.BackgroundTransparency = 1
Title.Text = "⚡ ANIME MYSTERY HUB"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 22
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Top

local SubTitle = Instance.new("TextLabel")
SubTitle.Size = UDim2.fromOffset(250, 20)
SubTitle.Position = UDim2.fromOffset(22, 36)
SubTitle.BackgroundTransparency = 1
SubTitle.Text = "ADMIN / TEST SYSTEM"
SubTitle.TextColor3 = Color3.fromRGB(150, 150, 165)
SubTitle.Font = Enum.Font.Gotham
SubTitle.TextSize = 10
SubTitle.TextXAlignment = Enum.TextXAlignment.Left
SubTitle.Parent = Top

--==================================================
-- CLOSE / MINIMIZE
--==================================================

local Close = Instance.new("TextButton")
Close.Size = UDim2.fromOffset(40, 40)
Close.Position = UDim2.new(1, -50, 0, 10)
Close.BackgroundColor3 = Color3.fromRGB(180, 45, 60)
Close.Text = "×"
Close.TextColor3 = Color3.new(1,1,1)
Close.Font = Enum.Font.GothamBold
Close.TextSize = 24
Close.Parent = Top

Instance.new("UICorner", Close).CornerRadius = UDim.new(0, 8)

Close.MouseButton1Click:Connect(function()
	Main.Visible = false
end)

--==================================================
-- SIDEBAR
--==================================================

local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.fromOffset(180, 450)
Sidebar.Position = UDim2.fromOffset(10, 65)
Sidebar.BackgroundColor3 = Color3.fromRGB(19, 19, 25)
Sidebar.BorderSizePixel = 0
Sidebar.Parent = Main

Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 10)

local SideLayout = Instance.new("UIListLayout")
SideLayout.Padding = UDim.new(0, 6)
SideLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
SideLayout.SortOrder = Enum.SortOrder.LayoutOrder
SideLayout.Parent = Sidebar

local SidePadding = Instance.new("UIPadding")
SidePadding.PaddingTop = UDim.new(0, 10)
SidePadding.Parent = Sidebar

--==================================================
-- CONTENT
--==================================================

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -205, 1, -75)
Content.Position = UDim2.fromOffset(195, 65)
Content.BackgroundColor3 = Color3.fromRGB(19, 19, 25)
Content.BorderSizePixel = 0
Content.Parent = Main

Instance.new("UICorner", Content).CornerRadius = UDim.new(0, 10)

--==================================================
-- NOTIFICATION
--==================================================

local Notification = Instance.new("TextLabel")
Notification.Size = UDim2.fromOffset(300, 55)
Notification.Position = UDim2.new(1, -320, 1, -75)
Notification.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
Notification.TextColor3 = Color3.new(1,1,1)
Notification.Font = Enum.Font.GothamBold
Notification.TextSize = 14
Notification.Text = ""
Notification.Visible = false
Notification.Parent = gui

Instance.new("UICorner", Notification).CornerRadius = UDim.new(0, 10)

local function Notify(text)
	if not Config.Notifications then return end

	Notification.Text = "  🔔 " .. text
	Notification.Visible = true

	task.delay(2.5, function()
		Notification.Visible = false
	end)
end

--==================================================
-- TAB SYSTEM
--==================================================

local Tabs = {}
local CurrentTab

local function CreateTab(name, icon)
	local button = Instance.new("TextButton")
	button.Name = name
	button.Size = UDim2.fromOffset(160, 42)
	button.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
	button.Text = icon .. "  " .. name
	button.TextColor3 = Color3.fromRGB(190, 190, 200)
	button.Font = Enum.Font.GothamBold
	button.TextSize = 13
	button.BorderSizePixel = 0
	button.Parent = Sidebar

	Instance.new("UICorner", button).CornerRadius = UDim.new(0, 8)

	local page = Instance.new("ScrollingFrame")
	page.Name = name .. "Page"
	page.Size = UDim2.new(1, -20, 1, -20)
	page.Position = UDim2.fromOffset(10, 10)
	page.BackgroundTransparency = 1
	page.BorderSizePixel = 0
	page.ScrollBarThickness = 4
	page.Visible = false
	page.CanvasSize = UDim2.new(0,0,0,0)
	page.Parent = Content

	local layout = Instance.new("UIListLayout")
	layout.Padding = UDim.new(0, 8)
	layout.SortOrder = Enum.SortOrder.LayoutOrder
	layout.Parent = page

	local padding = Instance.new("UIPadding")
	padding.PaddingBottom = UDim.new(0, 15)
	padding.Parent = page

	layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
		page.CanvasSize = UDim2.fromOffset(
			0,
			layout.AbsoluteContentSize.Y + 20
		)
	end)

	Tabs[name] = {
		Button = button,
		Page = page
	}

	button.MouseButton1Click:Connect(function()
		for _, tab in pairs(Tabs) do
			tab.Page.Visible = false
			tab.Button.BackgroundColor3 = Color3.fromRGB(28,28,36)
			tab.Button.TextColor3 = Color3.fromRGB(190,190,200)
		end

		page.Visible = true
		button.BackgroundColor3 = Color3.fromRGB(90,45,160)
		button.TextColor3 = Color3.new(1,1,1)

		CurrentTab = name
	end)

	return page
end

--==================================================
-- COMPONENTS
--==================================================

local function Section(parent, text)
	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -10, 0, 35)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.fromRGB(180, 100, 255)
	label.Font = Enum.Font.GothamBold
	label.TextSize = 16
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = parent

	return label
end

local function Toggle(parent, text, configName, callback)
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1, -10, 0, 45)
	button.BackgroundColor3 = Color3.fromRGB(28,28,36)
	button.Text = ""
	button.BorderSizePixel = 0
	button.Parent = parent

	Instance.new("UICorner", button).CornerRadius = UDim.new(0, 8)

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, -70, 1, 0)
	label.Position = UDim2.fromOffset(15,0)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.new(1,1,1)
	label.Font = Enum.Font.Gotham
	label.TextSize = 13
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = button

	local state = Instance.new("TextLabel")
	state.Size = UDim2.fromOffset(45,25)
	state.Position = UDim2.new(1,-58,0.5,-12)
	state.BackgroundColor3 = Color3.fromRGB(60,60,70)
	state.Text = "OFF"
	state.TextColor3 = Color3.fromRGB(180,180,190)
	state.Font = Enum.Font.GothamBold
	state.TextSize = 10
	state.Parent = button

	Instance.new("UICorner", state).CornerRadius = UDim.new(1,0)

	local enabled = Config[configName] or false

	local function update()
		Config[configName] = enabled

		if enabled then
			state.Text = "ON"
			state.BackgroundColor3 = Color3.fromRGB(110,55,190)
			state.TextColor3 = Color3.new(1,1,1)
		else
			state.Text = "OFF"
			state.BackgroundColor3 = Color3.fromRGB(60,60,70)
			state.TextColor3 = Color3.fromRGB(180,180,190)
		end
	end

	button.MouseButton1Click:Connect(function()
		enabled = not enabled
		update()

		if callback then
			callback(enabled)
		end
	end)

	update()

	return button
end

local function Slider(parent, text, configName, min, max)
	local holder = Instance.new("Frame")
	holder.Size = UDim2.new(1,-10,0,65)
	holder.BackgroundColor3 = Color3.fromRGB(28,28,36)
	holder.BorderSizePixel = 0
	holder.Parent = parent

	Instance.new("UICorner", holder).CornerRadius = UDim.new(0,8)

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1,-80,0,30)
	label.Position = UDim2.fromOffset(15,5)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.new(1,1,1)
	label.Font = Enum.Font.Gotham
	label.TextSize = 13
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = holder

	local value = Instance.new("TextLabel")
	value.Size = UDim2.fromOffset(60,30)
	value.Position = UDim2.new(1,-70,0,5)
	value.BackgroundTransparency = 1
	value.Text = tostring(Config[configName])
	value.TextColor3 = Color3.fromRGB(180,100,255)
	value.Font = Enum.Font.GothamBold
	value.TextSize = 13
	value.Parent = holder

	local bar = Instance.new("Frame")
	bar.Size = UDim2.new(1,-30,0,6)
	bar.Position = UDim2.fromOffset(15,45)
	bar.BackgroundColor3 = Color3.fromRGB(55,55,65)
	bar.BorderSizePixel = 0
	bar.Parent = holder

	Instance.new("UICorner",bar).CornerRadius = UDim.new(1,0)

	local fill = Instance.new("Frame")
	fill.Size = UDim2.new(
		(Config[configName]-min)/(max-min),
		0,1,0
	)
	fill.BackgroundColor3 = Color3.fromRGB(120,60,210)
	fill.BorderSizePixel = 0
	fill.Parent = bar

	Instance.new("UICorner",fill).CornerRadius = UDim.new(1,0)

	local dragging = false

	local function update(x)
		local percent = math.clamp(
			(x - bar.AbsolutePosition.X) / bar.AbsoluteSize.X,
			0,
			1
		)

		local newValue = math.floor(min + (max-min)*percent)

		Config[configName] = newValue
		value.Text = tostring(newValue)

		fill.Size = UDim2.new(percent,0,1,0)
	end

	bar.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			update(input.Position.X)
		end
	end)

	UIS.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)

	UIS.InputChanged:Connect(function(input)
		if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
			update(input.Position.X)
		end
	end)

	return holder
end

--==================================================
-- CREATE TABS
--==================================================

local Home = CreateTab("Home","🏠")
local Farm = CreateTab("Farm","⚔️")
local Combat = CreateTab("Combat","💥")
local ESP = CreateTab("ESP","👁️")
local Teleport = CreateTab("Teleport","🌀")
local Items = CreateTab("Items","📦")
local Raids = CreateTab("Raids","🏴‍☠️")
local Settings = CreateTab("Settings","⚙️")

--==================================================
-- HOME
--==================================================

Section(Home,"⚡ Anime Mystery")

local welcome = Instance.new("TextLabel")
welcome.Size = UDim2.new(1,-10,0,80)
welcome.BackgroundColor3 = Color3.fromRGB(28,28,36)
welcome.TextColor3 = Color3.new(1,1,1)
welcome.Font = Enum.Font.GothamBold
welcome.TextSize = 18
welcome.Text = "Bem-vindo ao Mystery Hub!\n\nAdmin / Testing Panel"
welcome.Parent = Home

Instance.new("UICorner",welcome).CornerRadius = UDim.new(0,10)

Toggle(Home,"🔔 Notifications","Notifications")

Toggle(Home,"💤 Anti-AFK","AntiAFK",function(value)
	Notify("Anti-AFK: "..tostring(value))
end)

--==================================================
-- FARM
--==================================================

Section(Farm,"⚔️ Farm")

Toggle(Farm,"🤖 Auto Farm","AutoFarm",function(value)
	Notify("Auto Farm: "..tostring(value))
end)

Toggle(Farm,"📜 Auto Quest","AutoQuest",function(value)
	Notify("Auto Quest: "..tostring(value))
end)

Toggle(Farm,"👹 Boss Farm","BossFarm",function(value)
	Notify("Boss Farm: "..tostring(value))
end)

Toggle(Farm,"📈 Auto Level","AutoStats",function(value)
	Notify("Auto Level: "..tostring(value))
end)

--==================================================
-- COMBAT
--==================================================

Section(Combat,"💥 Combat")

Toggle(Combat,"⚔️ Auto Attack","AutoAttack")

Toggle(Combat,"🎯 Target Selection","AutoAttack")

Toggle(Combat,"💥 Targeting","AutoAttack")

Toggle(Combat,"👹 Enemy Farm","AutoFarm")

--==================================================
-- ESP
--==================================================

Section(ESP,"👁️ ESP")

Toggle(ESP,"👤 Players ESP","PlayerESP")

Toggle(ESP,"👹 NPC ESP","NPCESP")

Toggle(ESP,"🍎 Fruit ESP","FruitESP")

Toggle(ESP,"📦 Chest ESP","ChestESP")

Toggle(ESP,"👑 Boss ESP","BossESP")

--==================================================
-- TELEPORT
--==================================================

Section(Teleport,"🌀 Teleport")

local teleportLocations = {
	"🏠 Lobby",
	"🌊 Marine",
	"🌋 Volcano",
	"🏝️ Island",
	"🏰 Castle",
	"⚔️ Arena",
	"👑 Boss Area"
}

for _, location in ipairs(teleportLocations) do
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1,-10,0,42)
	button.BackgroundColor3 = Color3.fromRGB(28,28,36)
	button.Text = location
	button.TextColor3 = Color3.new(1,1,1)
	button.Font = Enum.Font.Gotham
	button.TextSize = 13
	button.BorderSizePixel = 0
	button.Parent = Teleport

	Instance.new("UICorner",button).CornerRadius = UDim.new(0,8)

	button.MouseButton1Click:Connect(function()
		Notify("Teleport selecionado: "..location)

		--================================================
		-- COLOQUE AQUI O SISTEMA DE TELEPORTE DO SEU JOGO
		--================================================
	end)
end

--==================================================
-- ITEMS
--==================================================

Section(Items,"📦 Items")

Toggle(Items,"🔎 Item Finder","FruitESP")

Toggle(Items,"🍎 Fruit ESP","FruitESP")

Toggle(Items,"📦 Chest ESP","ChestESP")

Toggle(Items,"🧰 Auto Collect","AutoFarm")

Toggle(Items,"🎁 Drop Finder","FruitESP")

--==================================================
-- RAIDS
--==================================================

Section(Raids,"🏴‍☠️ Raids")

Toggle(Raids,"🏴‍☠️ Raid Mode","AutoFarm")

Toggle(Raids,"🚪 Auto Enter","AutoFarm")

Toggle(Raids,"⚔️ Auto Combat","AutoAttack")

--==================================================
-- SETTINGS
--==================================================

Section(Settings,"🎛️ Movement")

Slider(Settings,"💨 WalkSpeed","WalkSpeed",16,100)

Slider(Settings,"🦘 JumpPower","JumpPower",50,150)

Toggle(Settings,"🕊️ Fly","Fly",function(value)
	Notify("Fly: "..tostring(value))
end)

Toggle(Settings,"👻 NoClip","NoClip",function(value)
	Notify("NoClip: "..tostring(value))
end)

Section(Settings,"💾 Configuration")

local function ConfigButton(text, callback)
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1,-10,0,42)
	button.BackgroundColor3 = Color3.fromRGB(28,28,36)
	button.Text = text
	button.TextColor3 = Color3.new(1,1,1)
	button.Font = Enum.Font.GothamBold
	button.TextSize = 13
	button.BorderSizePixel = 0
	button.Parent = Settings

	Instance.new("UICorner",button).CornerRadius = UDim.new(0,8)

	button.MouseButton1Click:Connect(callback)

	return button
end

ConfigButton("💾 Save Config",function()
	Notify("Config salva nesta sessão.")
end)

ConfigButton("📂 Load Config",function()
	Notify("Config carregada.")
end)

ConfigButton("🔄 Reset Config",function()
	for key,value in pairs(Config) do
		if typeof(value) == "boolean" then
			Config[key] = false
		end
	end

	Config.WalkSpeed = 16
	Config.JumpPower = 50

	Notify("Config resetada.")
end)

ConfigButton("🔄 Rejoin",function()
	Notify("Sistema de Rejoin preparado.")
end)

ConfigButton("🌐 Server Hop",function()
	Notify("Sistema de Server Hop preparado.")
end)

--==================================================
-- DEFAULT TAB
--==================================================

Tabs.Home.Page.Visible = true
Tabs.Home.Button.BackgroundColor3 = Color3.fromRGB(90,45,160)
Tabs.Home.Button.TextColor3 = Color3.new(1,1,1)
CurrentTab = "Home"

--==================================================
-- DRAG
--==================================================

local dragging = false
local dragStart
local startPosition

Top.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPosition = Main.Position
	end
end)

UIS.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = false
	end
end)

UIS.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart

		Main.Position = UDim2.new(
			startPosition.X.Scale,
			startPosition.X.Offset + delta.X,
			startPosition.Y.Scale,
			startPosition.Y.Offset + delta.Y
		)
	end
end)

--==================================================
-- KEYBIND
-- RIGHT SHIFT = OPEN/CLOSE
--==================================================

UIS.InputBegan:Connect(function(input, processed)
	if processed then return end

	if input.KeyCode == Enum.KeyCode.RightShift then
		Main.Visible = not Main.Visible
	end
end)

--==================================================
-- WALK SPEED / JUMP POWER
--==================================================

local function ApplyCharacterSettings(character)
	local humanoid = character:WaitForChild("Humanoid")

	humanoid.WalkSpeed = Config.WalkSpeed
	humanoid.JumpPower = Config.JumpPower
end

if player.Character then
	task.spawn(ApplyCharacterSettings,player.Character)
end

player.CharacterAdded:Connect(ApplyCharacterSettings)

task.spawn(function()
	while task.wait(0.5) do
		local character = player.Character
		local humanoid = character and character:FindFirstChildOfClass("Humanoid")

		if humanoid then
			humanoid.WalkSpeed = Config.WalkSpeed
			humanoid.JumpPower = Config.JumpPower
		end
	end
end)

Notify("Mystery Hub carregado!")
