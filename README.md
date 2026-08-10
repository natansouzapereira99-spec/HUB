--// ANIME MYSTERY HUB
--// SINGLE SCRIPT
--// StarterPlayer > StarterPlayerScripts

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

local Player = Players.LocalPlayer

--==================================================
-- CONFIG
--==================================================

local Config = {
	AutoFarm = false,
	AutoQuest = false,
	BossFarm = false,
	AutoAttack = false,

	PlayerESP = false,
	NPCESP = false,
	FruitESP = false,
	ChestESP = false,
	BossESP = false,

	Fly = false,
	NoClip = false,

	WalkSpeed = 16,
	JumpPower = 50,

	TargetMode = "Nearest",
	Notifications = true,
}

local ESPObjects = {}
local Flying = false
local FlyVelocity
local FlyConnection

--==================================================
-- COLORS
--==================================================

local COLORS = {
	Background = Color3.fromRGB(9, 9, 14),
	Sidebar = Color3.fromRGB(13, 13, 21),
	Panel = Color3.fromRGB(18, 18, 28),
	Panel2 = Color3.fromRGB(24, 24, 36),

	Accent = Color3.fromRGB(150, 65, 255),
	Accent2 = Color3.fromRGB(205, 80, 255),

	Text = Color3.fromRGB(245, 245, 255),
	Muted = Color3.fromRGB(145, 145, 165),

	Success = Color3.fromRGB(70, 220, 130),
	Danger = Color3.fromRGB(240, 70, 90),
}

--==================================================
-- GUI
--==================================================

local old = Player.PlayerGui:FindFirstChild("AnimeMysteryHub")
if old then
	old:Destroy()
end

local Gui = Instance.new("ScreenGui")
Gui.Name = "AnimeMysteryHub"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.Parent = Player.PlayerGui

--==================================================
-- MAIN WINDOW
--==================================================

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.fromOffset(920, 570)
Main.Position = UDim2.new(.5, -460, .5, -285)
Main.BackgroundColor3 = COLORS.Background
Main.BorderSizePixel = 0
Main.Parent = Gui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 18)
MainCorner.Parent = Main

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = COLORS.Accent
MainStroke.Transparency = .35
MainStroke.Thickness = 1.5
MainStroke.Parent = Main

--==================================================
-- TOP BAR
--==================================================

local Top = Instance.new("Frame")
Top.Size = UDim2.new(1, 0, 0, 70)
Top.BackgroundColor3 = COLORS.Panel
Top.BorderSizePixel = 0
Top.Parent = Main

local TopCorner = Instance.new("UICorner")
TopCorner.CornerRadius = UDim.new(0, 18)
TopCorner.Parent = Top

local Logo = Instance.new("TextLabel")
Logo.Size = UDim2.fromOffset(350, 35)
Logo.Position = UDim2.fromOffset(25, 10)
Logo.BackgroundTransparency = 1
Logo.Text = "⚡ ANIME MYSTERY"
Logo.Font = Enum.Font.GothamBlack
Logo.TextSize = 23
Logo.TextColor3 = COLORS.Text
Logo.TextXAlignment = Enum.TextXAlignment.Left
Logo.Parent = Top

local Subtitle = Instance.new("TextLabel")
Subtitle.Size = UDim2.fromOffset(350, 20)
Subtitle.Position = UDim2.fromOffset(27, 40)
Subtitle.BackgroundTransparency = 1
Subtitle.Text = "MYSTERY HUB  •  ADMIN / TEST"
Subtitle.Font = Enum.Font.GothamBold
Subtitle.TextSize = 10
Subtitle.TextColor3 = COLORS.Accent2
Subtitle.TextXAlignment = Enum.TextXAlignment.Left
Subtitle.Parent = Top

local Status = Instance.new("TextLabel")
Status.Size = UDim2.fromOffset(120, 30)
Status.Position = UDim2.new(1, -170, 0, 20)
Status.BackgroundTransparency = 1
Status.Text = "● ONLINE"
Status.Font = Enum.Font.GothamBold
Status.TextSize = 12
Status.TextColor3 = COLORS.Success
Status.Parent = Top

local Close = Instance.new("TextButton")
Close.Size = UDim2.fromOffset(38, 38)
Close.Position = UDim2.new(1, -52, 0, 16)
Close.BackgroundColor3 = COLORS.Danger
Close.BackgroundTransparency = .1
Close.Text = "×"
Close.Font = Enum.Font.GothamBlack
Close.TextSize = 24
Close.TextColor3 = Color3.new(1,1,1)
Close.BorderSizePixel = 0
Close.Parent = Top

Instance.new("UICorner", Close).CornerRadius = UDim.new(0, 10)

Close.MouseButton1Click:Connect(function()
	Main.Visible = false
end)

--==================================================
-- SIDEBAR
--==================================================

local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.fromOffset(190, 480)
Sidebar.Position = UDim2.fromOffset(12, 78)
Sidebar.BackgroundColor3 = COLORS.Sidebar
Sidebar.BorderSizePixel = 0
Sidebar.Parent = Main

Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 14)

local SidePadding = Instance.new("UIPadding")
SidePadding.PaddingTop = UDim.new(0, 12)
SidePadding.PaddingLeft = UDim.new(0, 10)
SidePadding.PaddingRight = UDim.new(0, 10)
SidePadding.Parent = Sidebar

local SideLayout = Instance.new("UIListLayout")
SideLayout.Padding = UDim.new(0, 7)
SideLayout.SortOrder = Enum.SortOrder.LayoutOrder
SideLayout.Parent = Sidebar

--==================================================
-- CONTENT
--==================================================

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -220, 1, -90)
Content.Position = UDim2.fromOffset(205, 80)
Content.BackgroundColor3 = COLORS.Panel
Content.BorderSizePixel = 0
Content.Parent = Main

Instance.new("UICorner", Content).CornerRadius = UDim.new(0, 14)

--==================================================
-- NOTIFICATION
--==================================================

local Notification = Instance.new("Frame")
Notification.Size = UDim2.fromOffset(330, 65)
Notification.Position = UDim2.new(1, -350, 1, -85)
Notification.BackgroundColor3 = COLORS.Panel2
Notification.BorderSizePixel = 0
Notification.Visible = false
Notification.Parent = Gui

Instance.new("UICorner", Notification).CornerRadius = UDim.new(0, 12)

local NotificationText = Instance.new("TextLabel")
NotificationText.Size = UDim2.new(1,-20,1,0)
NotificationText.Position = UDim2.fromOffset(10,0)
NotificationText.BackgroundTransparency = 1
NotificationText.TextColor3 = COLORS.Text
NotificationText.Font = Enum.Font.GothamBold
NotificationText.TextSize = 13
NotificationText.TextXAlignment = Enum.TextXAlignment.Left
NotificationText.Parent = Notification

local function Notify(message)

	if not Config.Notifications then
		return
	end

	NotificationText.Text = "🔔  "..message
	Notification.Visible = true

	task.delay(2.5,function()
		Notification.Visible = false
	end)
end

--==================================================
-- TAB SYSTEM
--==================================================

local Tabs = {}

local function CreateTab(name, icon)

	local Button = Instance.new("TextButton")
	Button.Name = name
	Button.Size = UDim2.new(1,0,0,43)
	Button.BackgroundColor3 = COLORS.Sidebar
	Button.Text = icon.."  "..name
	Button.TextColor3 = COLORS.Muted
	Button.Font = Enum.Font.GothamBold
	Button.TextSize = 13
	Button.BorderSizePixel = 0
	Button.AutoButtonColor = false
	Button.Parent = Sidebar

	Instance.new("UICorner",Button).CornerRadius = UDim.new(0,10)

	local Page = Instance.new("ScrollingFrame")
	Page.Name = name.."Page"
	Page.Size = UDim2.new(1,-24,1,-24)
	Page.Position = UDim2.fromOffset(12,12)
	Page.BackgroundTransparency = 1
	Page.BorderSizePixel = 0
	Page.ScrollBarThickness = 3
	Page.ScrollBarImageColor3 = COLORS.Accent
	Page.Visible = false
	Page.Parent = Content

	local Layout = Instance.new("UIListLayout")
	Layout.Padding = UDim.new(0,9)
	Layout.Parent = Page

	local Padding = Instance.new("UIPadding")
	Padding.PaddingBottom = UDim.new(0,20)
	Padding.Parent = Page

	Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
		Page.CanvasSize = UDim2.fromOffset(
			0,
			Layout.AbsoluteContentSize.Y + 20
		)
	end)

	Tabs[name] = {
		Button = Button,
		Page = Page
	}

	Button.MouseButton1Click:Connect(function()

		for _,tab in pairs(Tabs) do
			tab.Page.Visible = false
			tab.Button.BackgroundColor3 = COLORS.Sidebar
			tab.Button.TextColor3 = COLORS.Muted
		end

		Page.Visible = true
		Button.BackgroundColor3 = COLORS.Accent
		Button.TextColor3 = COLORS.Text
	end)

	return Page
end

--==================================================
-- COMPONENTS
--==================================================

local function Section(parent,text)

	local Label = Instance.new("TextLabel")
	Label.Size = UDim2.new(1,-5,0,35)
	Label.BackgroundTransparency = 1
	Label.Text = text
	Label.TextColor3 = COLORS.Accent2
	Label.Font = Enum.Font.GothamBlack
	Label.TextSize = 16
	Label.TextXAlignment = Enum.TextXAlignment.Left
	Label.Parent = parent

	return Label
end

local function Toggle(parent,text,configName,callback)

	local Button = Instance.new("TextButton")
	Button.Size = UDim2.new(1,-5,0,52)
	Button.BackgroundColor3 = COLORS.Panel2
	Button.Text = ""
	Button.BorderSizePixel = 0
	Button.AutoButtonColor = false
	Button.Parent = parent

	Instance.new("UICorner",Button).CornerRadius = UDim.new(0,11)

	local Label = Instance.new("TextLabel")
	Label.Size = UDim2.new(1,-100,1,0)
	Label.Position = UDim2.fromOffset(16,0)
	Label.BackgroundTransparency = 1
	Label.Text = text
	Label.TextColor3 = COLORS.Text
	Label.Font = Enum.Font.GothamBold
	Label.TextSize = 13
	Label.TextXAlignment = Enum.TextXAlignment.Left
	Label.Parent = Button

	local State = Instance.new("TextLabel")
	State.Size = UDim2.fromOffset(55,28)
	State.Position = UDim2.new(1,-70,.5,-14)
	State.BackgroundColor3 = Color3.fromRGB(45,45,58)
	State.Text = "OFF"
	State.TextColor3 = COLORS.Muted
	State.Font = Enum.Font.GothamBlack
	State.TextSize = 10
	State.Parent = Button

	Instance.new("UICorner",State).CornerRadius = UDim.new(1,0)

	local enabled = Config[configName] == true

	local function Update()

		Config[configName] = enabled

		if enabled then
			State.Text = "ON"
			State.BackgroundColor3 = COLORS.Accent
			State.TextColor3 = Color3.new(1,1,1)
			Button.BackgroundColor3 = Color3.fromRGB(31,24,45)
		else
			State.Text = "OFF"
			State.BackgroundColor3 = Color3.fromRGB(45,45,58)
			State.TextColor3 = COLORS.Muted
			Button.BackgroundColor3 = COLORS.Panel2
		end
	end

	Button.MouseButton1Click:Connect(function()

		enabled = not enabled

		Update()

		if callback then
			callback(enabled)
		end

	end)

	Button.MouseEnter:Connect(function()
		TweenService:Create(
			Button,
			TweenInfo.new(.15),
			{BackgroundColor3 = Color3.fromRGB(32,32,47)}
		):Play()
	end)

	Button.MouseLeave:Connect(Update)

	Update()

	return Button
end

local function Slider(parent,text,configName,min,max)

	local Holder = Instance.new("Frame")
	Holder.Size = UDim2.new(1,-5,0,70)
	Holder.BackgroundColor3 = COLORS.Panel2
	Holder.BorderSizePixel = 0
	Holder.Parent = parent

	Instance.new("UICorner",Holder).CornerRadius = UDim.new(0,11)

	local Label = Instance.new("TextLabel")
	Label.Size = UDim2.new(1,-100,0,30)
	Label.Position = UDim2.fromOffset(15,5)
	Label.BackgroundTransparency = 1
	Label.Text = text
	Label.TextColor3 = COLORS.Text
	Label.Font = Enum.Font.GothamBold
	Label.TextSize = 13
	Label.TextXAlignment = Enum.TextXAlignment.Left
	Label.Parent = Holder

	local Value = Instance.new("TextLabel")
	Value.Size = UDim2.fromOffset(65,30)
	Value.Position = UDim2.new(1,-80,0,5)
	Value.BackgroundTransparency = 1
	Value.TextColor3 = COLORS.Accent2
	Value.Font = Enum.Font.GothamBlack
	Value.TextSize = 13
	Value.Text = tostring(Config[configName])
	Value.Parent = Holder

	local Bar = Instance.new("Frame")
	Bar.Size = UDim2.new(1,-30,0,7)
	Bar.Position = UDim2.fromOffset(15,48)
	Bar.BackgroundColor3 = Color3.fromRGB(50,50,62)
	Bar.BorderSizePixel = 0
	Bar.Parent = Holder

	Instance.new("UICorner",Bar).CornerRadius = UDim.new(1,0)

	local Fill = Instance.new("Frame")
	Fill.BackgroundColor3 = COLORS.Accent
	Fill.BorderSizePixel = 0
	Fill.Size = UDim2.new(
		(Config[configName]-min)/(max-min),
		0,1,0
	)
	Fill.Parent = Bar

	Instance.new("UICorner",Fill).CornerRadius = UDim.new(1,0)

	local dragging = false

	local function Update(x)

		local percent = math.clamp(
			(x-Bar.AbsolutePosition.X)/Bar.AbsoluteSize.X,
			0,1
		)

		local value = math.floor(
			min + ((max-min)*percent)
		)

		Config[configName] = value

		Value.Text = tostring(value)

		Fill.Size = UDim2.new(percent,0,1,0)

	end

	Bar.InputBegan:Connect(function(input)

		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true
			Update(input.Position.X)
		end

	end)

	UIS.InputEnded:Connect(function(input)

		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end

	end)

	UIS.InputChanged:Connect(function(input)

		if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
			Update(input.Position.X)
		end

	end)

	return Holder
end

--==================================================
-- TABS
--==================================================

local Home = CreateTab("Home","🏠")
local Farm = CreateTab("Farm","⚔️")
local Combat = CreateTab("Combat","💥")
local ESP = CreateTab("ESP","👁️")
local Teleport = CreateTab("Teleport","🌀")
local Items = CreateTab("Items","📦")
local Raids = CreateTab("Raids","🏴")
local Settings = CreateTab("Settings","⚙️")

--==================================================
-- HOME
--==================================================

Section(Home,"⚡ Dashboard")

local Welcome = Instance.new("Frame")
Welcome.Size = UDim2.new(1,-5,0,120)
Welcome.BackgroundColor3 = COLORS.Panel2
Welcome.BorderSizePixel = 0
Welcome.Parent = Home

Instance.new("UICorner",Welcome).CornerRadius = UDim.new(0,13)

local WelcomeText = Instance.new("TextLabel")
WelcomeText.Size = UDim2.new(1,-30,1,-20)
WelcomeText.Position = UDim2.fromOffset(15,10)
WelcomeText.BackgroundTransparency = 1
WelcomeText.Text =
	"⚡ ANIME MYSTERY HUB\n\n"..
	"Painel de administração e testes do seu jogo.\n"..
	"Use RightShift para abrir/fechar."
WelcomeText.TextColor3 = COLORS.Text
WelcomeText.Font = Enum.Font.GothamBold
WelcomeText.TextSize = 15
WelcomeText.TextXAlignment = Enum.TextXAlignment.Left
WelcomeText.TextYAlignment = Enum.TextYAlignment.Center
WelcomeText.Parent = Welcome

Section(Home,"📊 Status")

Toggle(Home,"🔔 Notifications","Notifications")

--==================================================
-- FARM
--==================================================

Section(Farm,"⚔️ FARM")

Toggle(Farm,"🤖 Auto Farm","AutoFarm",function(v)
	Notify("Auto Farm "..(v and "ativado" or "desativado"))
end)

Toggle(Farm,"📜 Auto Quest","AutoQuest",function(v)
	Notify("Auto Quest "..(v and "ativado" or "desativado"))
end)

Toggle(Farm,"👹 Boss Farm","BossFarm",function(v)
	Notify("Boss Farm "..(v and "ativado" or "desativado"))
end)

--==================================================
-- COMBAT
--==================================================

Section(Combat,"💥 COMBAT")

Toggle(Combat,"⚔️ Auto Attack","AutoAttack",function(v)
	Notify("Auto Attack "..(v and "ativado" or "desativado"))
end)

Toggle(Combat,"🎯 Nearest Target","AutoAttack")

--==================================================
-- ESP
--==================================================

Section(ESP,"👁️ ESP")

Toggle(ESP,"👤 Player ESP","PlayerESP")

Toggle(ESP,"👹 NPC ESP","NPCESP")

Toggle(ESP,"🍎 Fruit ESP","FruitESP")

Toggle(ESP,"📦 Chest ESP","ChestESP")

Toggle(ESP,"👑 Boss ESP","BossESP")

--==================================================
-- TELEPORT
--==================================================

Section(Teleport,"🌀 TELEPORT")

local function TeleportButton(name)

	local Button = Instance.new("TextButton")
	Button.Size = UDim2.new(1,-5,0,48)
	Button.BackgroundColor3 = COLORS.Panel2
	Button.Text = "🌀  "..name
	Button.TextColor3 = COLORS.Text
	Button.Font = Enum.Font.GothamBold
	Button.TextSize = 13
	Button.TextXAlignment = Enum.TextXAlignment.Left
	Button.BorderSizePixel = 0
	Button.Parent = Teleport

	Instance.new("UICorner",Button).CornerRadius = UDim.new(0,11)

	local pad = Instance.new("UIPadding")
	pad.PaddingLeft = UDim.new(0,15)
	pad.Parent = Button

	Button.MouseButton1Click:Connect(function()

		local folder = workspace:FindFirstChild("TeleportPoints")

		if not folder then
			Notify("Crie Workspace.TeleportPoints")
			return
		end

		local point = folder:FindFirstChild(name)

		if not point then
			Notify("Teleport não encontrado: "..name)
			return
		end

		local character = Player.Character
		local root = character and character:FindFirstChild("HumanoidRootPart")

		if root then
			root.CFrame = point.CFrame + Vector3.new(0,3,0)
			Notify("Teleportado para "..name)
		end

	end)

end

for _,name in ipairs({
	"Lobby",
	"Marine",
	"Volcano",
	"Castle",
	"Arena"
}) do
	TeleportButton(name)
end

--==================================================
-- ITEMS
--==================================================

Section(Items,"📦 ITEMS")

Toggle(Items,"🍎 Fruit ESP","FruitESP")

Toggle(Items,"📦 Chest ESP","ChestESP")

Toggle(Items,"🧰 Auto Collect","AutoFarm")

--==================================================
-- RAIDS
--==================================================

Section(Raids,"🏴 RAIDS")

Toggle(Raids,"🏴 Raid Mode","AutoFarm")

Toggle(Raids,"🚪 Auto Enter","AutoQuest")

Toggle(Raids,"⚔️ Auto Combat","AutoAttack")

--==================================================
-- SETTINGS
--==================================================

Section(Settings,"🎛️ MOVEMENT")

Slider(Settings,"💨 WalkSpeed","WalkSpeed",16,100)

Slider(Settings,"🦘 JumpPower","JumpPower",50,150)

Toggle(Settings,"👻 NoClip","NoClip")

Toggle(Settings,"🕊️ Fly","Fly",function(enabled)

	if enabled then
		Notify("Fly ativado")
	else
		Notify("Fly desativado")
	end

end)

--==================================================
-- CHARACTER
--==================================================

local function GetHumanoid()

	local character = Player.Character

	if not character then
		return nil
	end

	return character:FindFirstChildOfClass("Humanoid")
end

local function ApplyMovement()

	local humanoid = GetHumanoid()

	if humanoid then
		humanoid.WalkSpeed = Config.WalkSpeed
		humanoid.JumpPower = Config.JumpPower
	end

end

Player.CharacterAdded:Connect(function()

	task.wait(1)

	ApplyMovement()

end)

--==================================================
-- NOCLIP
--==================================================

RunService.Stepped:Connect(function()

	if not Config.NoClip then
		return
	end

	local character = Player.Character

	if not character then
		return
	end

	for _,part in ipairs(character:GetDescendants()) do

		if part:IsA("BasePart") then
			part.CanCollide = false
		end

	end

end)

--==================================================
-- FLY
--==================================================

local function StopFly()

	if FlyConnection then
		FlyConnection:Disconnect()
		FlyConnection = nil
	end

	if FlyVelocity then
		FlyVelocity:Destroy()
		FlyVelocity = nil
	end

end

local function StartFly()

	StopFly()

	local character = Player.Character
	if not character then return end

	local root = character:FindFirstChild("HumanoidRootPart")
	if not root then return end

	FlyVelocity = Instance.new("BodyVelocity")
	FlyVelocity.MaxForce = Vector3.new(1e6,1e6,1e6)
	FlyVelocity.Velocity = Vector3.zero
	FlyVelocity.Parent = root

	FlyConnection = RunService.RenderStepped:Connect(function()

		if not Config.Fly then
			StopFly()
			return
		end

		local camera = workspace.CurrentCamera

		local direction = Vector3.zero

		if UIS:IsKeyDown(Enum.KeyCode.W) then
			direction += camera.CFrame.LookVector
		end

		if UIS:IsKeyDown(Enum.KeyCode.S) then
			direction -= camera.CFrame.LookVector
		end

		if UIS:IsKeyDown(Enum.KeyCode.A) then
			direction -= camera.CFrame.RightVector
		end

		if UIS:IsKeyDown(Enum.KeyCode.D) then
			direction += camera.CFrame.RightVector
		end

		if UIS:IsKeyDown(Enum.KeyCode.Space) then
			direction += Vector3.new(0,1,0)
		end

		if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
			direction -= Vector3.new(0,1,0)
		end

		if direction.Magnitude > 0 then
			direction = direction.Unit * 70
		end

		FlyVelocity.Velocity = direction

	end)

end

RunService.Heartbeat:Connect(function()

	if Config.Fly and not FlyConnection then
		StartFly()
	elseif not Config.Fly and FlyConnection then
		StopFly()
	end

	ApplyMovement()

end)

--==================================================
-- ESP
--==================================================

local function ClearESP()

	for object,highlight in pairs(ESPObjects) do

		if highlight then
			highlight:Destroy()
		end

		ESPObjects[object] = nil

	end

end

local function AddESP(object,color)

	if not object:IsA("Model") and not object:IsA("BasePart") then
		return
	end

	if ESPObjects[object] then
		return
	end

	local highlight = Instance.new("Highlight")
	highlight.Name = "MysteryESP"
	highlight.FillColor = color
	highlight.OutlineColor = Color3.new(1,1,1)
	highlight.FillTransparency = .65
	highlight.OutlineTransparency = .1
	highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	highlight.Adornee = object
	highlight.Parent = object

	ESPObjects[object] = highlight
end

local function UpdateESP()

	ClearESP()

	local function scan(folderName,enabled,color)

		if not enabled then
			return
		end

		local folder = workspace:FindFirstChild(folderName)

		if not folder then
			return
		end

		for _,object in ipairs(folder:GetChildren()) do
			AddESP(object,color)
		end

	end

	scan("Enemies",Config.NPCESP,Color3.fromRGB(255,80,80))
	scan("NPCs",Config.NPCESP,Color3.fromRGB(255,170,60))
	scan("Fruits",Config.FruitESP,Color3.fromRGB(70,255,120))
	scan("Chests",Config.ChestESP,Color3.fromRGB(255,210,60))
	scan("Bosses",Config.BossESP,Color3.fromRGB(180,60,255))

	if Config.PlayerESP then

		for _,other in ipairs(Players:GetPlayers()) do

			if other ~= Player and other.Character then
				AddESP(
					other.Character,
					Color3.fromRGB(60,150,255)
				)
			end

		end

	end

end

task.spawn(function()

	while task.wait(1) do
		UpdateESP()
	end

end)

--==================================================
-- KEYBIND
--==================================================

UIS.InputBegan:Connect(function(input,processed)

	if processed then
		return
	end

	if input.KeyCode == Enum.KeyCode.RightShift then
		Main.Visible = not Main.Visible
	end

end)

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

	if not dragging then
		return
	end

	if input.UserInputType ~= Enum.UserInputType.MouseMovement then
		return
	end

	local delta = input.Position - dragStart

	Main.Position = UDim2.new(
		startPosition.X.Scale,
		startPosition.X.Offset + delta.X,
		startPosition.Y.Scale,
		startPosition.Y.Offset + delta.Y
	)

end)

--==================================================
-- DEFAULT TAB
--==================================================

Tabs.Home.Page.Visible = true
Tabs.Home.Button.BackgroundColor3 = COLORS.Accent
Tabs.Home.Button.TextColor3 = COLORS.Text

--==================================================
-- START
--==================================================

Notify("Anime Mystery Hub carregado!")

print("================================")
print(" ANIME MYSTERY HUB")
print(" Loaded successfully")
print(" RightShift = Open / Close")
print("================================")
