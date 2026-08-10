--// ANIME/BLOX TEST HUB - INSTALLER
--// EXECUTE NO COMMAND BAR DO ROBLOX STUDIO
--// Para usar na sua própria cópia/test place.

local StarterPlayer = game:GetService("StarterPlayer")
local StarterPlayerScripts = StarterPlayer:WaitForChild("StarterPlayerScripts")

local old = StarterPlayerScripts:FindFirstChild("TestHub")
if old then
	old:Destroy()
end

local script = Instance.new("LocalScript")
script.Name = "TestHub"
script.Source = [==[
--========================================================
-- TEST HUB
--========================================================

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local guiParent = player:WaitForChild("PlayerGui")

local Config = {
	Speed = 16,
	Jump = 50,

	SpeedEnabled = false,
	JumpEnabled = false,
	NoClip = false,
	Fly = false,

	PlayerESP = false,
	NPCESP = false,
	BossESP = false,
	FruitESP = false,
	ChestESP = false,

	AutoFarm = false,
	AutoAttack = false,
}

--========================================================
-- GUI
--========================================================

local Gui = Instance.new("ScreenGui")
Gui.Name = "TestHub"
Gui.ResetOnSpawn = false
Gui.Parent = guiParent

local Main = Instance.new("Frame")
Main.Size = UDim2.fromOffset(850,520)
Main.Position = UDim2.new(.5,-425,.5,-260)
Main.BackgroundColor3 = Color3.fromRGB(12,12,18)
Main.BorderSizePixel = 0
Main.Parent = Gui

Instance.new("UICorner",Main).CornerRadius = UDim.new(0,16)

local Stroke = Instance.new("UIStroke")
Stroke.Color = Color3.fromRGB(145,60,255)
Stroke.Thickness = 2
Stroke.Parent = Main

--========================================================
-- TITLE
--========================================================

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1,-100,0,60)
Title.Position = UDim2.fromOffset(20,0)
Title.BackgroundTransparency = 1
Title.Text = "⚡ TEST HUB"
Title.TextColor3 = Color3.new(1,1,1)
Title.Font = Enum.Font.GothamBlack
Title.TextSize = 24
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Main

local Close = Instance.new("TextButton")
Close.Size = UDim2.fromOffset(40,40)
Close.Position = UDim2.new(1,-55,0,10)
Close.Text = "×"
Close.TextSize = 25
Close.TextColor3 = Color3.new(1,1,1)
Close.BackgroundColor3 = Color3.fromRGB(200,50,70)
Close.BorderSizePixel = 0
Close.Parent = Main

Instance.new("UICorner",Close).CornerRadius = UDim.new(0,10)

Close.MouseButton1Click:Connect(function()
	Main.Visible = false
end)

--========================================================
-- SIDEBAR
--========================================================

local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.fromOffset(180,440)
Sidebar.Position = UDim2.fromOffset(10,65)
Sidebar.BackgroundColor3 = Color3.fromRGB(18,18,27)
Sidebar.BorderSizePixel = 0
Sidebar.Parent = Main

Instance.new("UICorner",Sidebar).CornerRadius = UDim.new(0,12)

local SideLayout = Instance.new("UIListLayout")
SideLayout.Padding = UDim.new(0,6)
SideLayout.Parent = Sidebar

local SidePad = Instance.new("UIPadding")
SidePad.PaddingTop = UDim.new(0,10)
SidePad.PaddingLeft = UDim.new(0,10)
SidePad.PaddingRight = UDim.new(0,10)
SidePad.Parent = Sidebar

--========================================================
-- CONTENT
--========================================================

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1,-205,1,-75)
Content.Position = UDim2.fromOffset(195,65)
Content.BackgroundColor3 = Color3.fromRGB(18,18,27)
Content.BorderSizePixel = 0
Content.Parent = Main

Instance.new("UICorner",Content).CornerRadius = UDim.new(0,12)

local Tabs = {}

local function CreateTab(name,icon)

	local Button = Instance.new("TextButton")
	Button.Size = UDim2.new(1,0,0,42)
	Button.BackgroundColor3 = Color3.fromRGB(25,25,36)
	Button.Text = icon.."  "..name
	Button.TextColor3 = Color3.fromRGB(190,190,205)
	Button.Font = Enum.Font.GothamBold
	Button.TextSize = 13
	Button.BorderSizePixel = 0
	Button.Parent = Sidebar

	Instance.new("UICorner",Button).CornerRadius = UDim.new(0,9)

	local Page = Instance.new("ScrollingFrame")
	Page.Size = UDim2.new(1,-20,1,-20)
	Page.Position = UDim2.fromOffset(10,10)
	Page.BackgroundTransparency = 1
	Page.BorderSizePixel = 0
	Page.ScrollBarThickness = 3
	Page.Visible = false
	Page.Parent = Content

	local Layout = Instance.new("UIListLayout")
	Layout.Padding = UDim.new(0,8)
	Layout.Parent = Page

	Tabs[name] = {
		Button = Button,
		Page = Page
	}

	Button.MouseButton1Click:Connect(function()

		for _,tab in pairs(Tabs) do
			tab.Page.Visible = false
			tab.Button.BackgroundColor3 = Color3.fromRGB(25,25,36)
		end

		Page.Visible = true
		Button.BackgroundColor3 = Color3.fromRGB(105,45,190)

	end)

	return Page
end

--========================================================
-- COMPONENTS
--========================================================

local function Section(parent,text)

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1,-5,0,35)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.fromRGB(185,90,255)
	label.Font = Enum.Font.GothamBlack
	label.TextSize = 16
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = parent

end

local function Toggle(parent,text,key,callback)

	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1,-5,0,48)
	button.BackgroundColor3 = Color3.fromRGB(27,27,40)
	button.Text = ""
	button.BorderSizePixel = 0
	button.Parent = parent

	Instance.new("UICorner",button).CornerRadius = UDim.new(0,9)

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1,-80,1,0)
	label.Position = UDim2.fromOffset(15,0)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.new(1,1,1)
	label.Font = Enum.Font.GothamBold
	label.TextSize = 13
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = button

	local state = Instance.new("TextLabel")
	state.Size = UDim2.fromOffset(50,25)
	state.Position = UDim2.new(1,-65,.5,-12)
	state.BackgroundColor3 = Color3.fromRGB(55,55,70)
	state.Text = "OFF"
	state.TextColor3 = Color3.fromRGB(170,170,180)
	state.Font = Enum.Font.GothamBlack
	state.TextSize = 10
	state.Parent = button

	Instance.new("UICorner",state).CornerRadius = UDim.new(1,0)

	local function Update()

		if Config[key] then
			state.Text = "ON"
			state.BackgroundColor3 = Color3.fromRGB(120,55,220)
		else
			state.Text = "OFF"
			state.BackgroundColor3 = Color3.fromRGB(55,55,70)
		end

	end

	button.MouseButton1Click:Connect(function()

		Config[key] = not Config[key]

		Update()

		if callback then
			callback(Config[key])
		end

	end)

	Update()

end

--========================================================
-- TABS
--========================================================

local Home = CreateTab("Home","🏠")
local Farm = CreateTab("Farm","⚔️")
local Combat = CreateTab("Combat","💥")
local ESP = CreateTab("ESP","👁️")
local Movement = CreateTab("Movement","🪽")
local Teleport = CreateTab("Teleport","🌀")
local Settings = CreateTab("Settings","⚙️")

--========================================================
-- HOME
--========================================================

Section(Home,"⚡ Dashboard")

local Info = Instance.new("TextLabel")
Info.Size = UDim2.new(1,-5,0,120)
Info.BackgroundColor3 = Color3.fromRGB(27,27,40)
Info.TextColor3 = Color3.new(1,1,1)
Info.Font = Enum.Font.GothamBold
Info.TextSize = 16
Info.Text =
	"⚡ TEST HUB\n\n"..
	"Painel para testes da sua própria experiência.\n\n"..
	"RightShift = abrir / fechar"
Info.Parent = Home

Instance.new("UICorner",Info).CornerRadius = UDim.new(0,10)

--========================================================
-- FARM
--========================================================

Section(Farm,"⚔️ FARM")

Toggle(Farm,"🤖 Auto Farm","AutoFarm",function(v)
	print("AutoFarm:",v)
end)

Toggle(Farm,"⚔️ Auto Attack","AutoAttack",function(v)
	print("AutoAttack:",v)
end)

--========================================================
-- COMBAT
--========================================================

Section(Combat,"💥 COMBAT")

Toggle(Combat,"⚔️ Auto Attack","AutoAttack")

Toggle(Combat,"🎯 Target Nearest","AutoAttack")

--========================================================
-- ESP
--========================================================

Section(ESP,"👁️ ESP")

Toggle(ESP,"👤 Player ESP","PlayerESP")

Toggle(ESP,"👹 NPC ESP","NPCESP")

Toggle(ESP,"👑 Boss ESP","BossESP")

Toggle(ESP,"🍎 Fruit ESP","FruitESP")

Toggle(ESP,"📦 Chest ESP","ChestESP")

--========================================================
-- MOVEMENT
--========================================================

Section(Movement,"🪽 MOVEMENT")

Toggle(Movement,"👻 NoClip","NoClip")

Toggle(Movement,"🕊️ Fly","Fly")

Toggle(Movement,"💨 Speed x2","SpeedEnabled")

Toggle(Movement,"🦘 Jump x2","JumpEnabled")

--========================================================
-- TELEPORT
--========================================================

Section(Teleport,"🌀 TELEPORT")

local function TeleportTo(name)

	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1,-5,0,45)
	button.BackgroundColor3 = Color3.fromRGB(27,27,40)
	button.Text = "🌀  "..name
	button.TextColor3 = Color3.new(1,1,1)
	button.Font = Enum.Font.GothamBold
	button.TextSize = 13
	button.BorderSizePixel = 0
	button.Parent = Teleport

	Instance.new("UICorner",button).CornerRadius = UDim.new(0,9)

	button.MouseButton1Click:Connect(function()

		local points = workspace:FindFirstChild("TeleportPoints")

		if not points then
			warn("Crie Workspace.TeleportPoints")
			return
		end

		local point = points:FindFirstChild(name)

		if not point then
			warn("Ponto não encontrado:",name)
			return
		end

		local character = player.Character
		local root = character and character:FindFirstChild("HumanoidRootPart")

		if root then
			root.CFrame = point.CFrame + Vector3.new(0,4,0)
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
	TeleportTo(name)
end

--========================================================
-- SETTINGS
--========================================================

Section(Settings,"⚙️ SETTINGS")

Toggle(Settings,"🔔 Console Debug","Notifications",function(v)
	print("Debug:",v)
end)

--========================================================
-- NOCLIP
--========================================================

RunService.Stepped:Connect(function()

	if not Config.NoClip then
		return
	end

	local character = player.Character

	if not character then
		return
	end

	for _,obj in ipairs(character:GetDescendants()) do

		if obj:IsA("BasePart") then
			obj.CanCollide = false
		end

	end

end)

--========================================================
-- MOVEMENT
--========================================================

RunService.Heartbeat:Connect(function()

	local character = player.Character
	local humanoid = character and character:FindFirstChildOfClass("Humanoid")

	if not humanoid then
		return
	end

	if Config.SpeedEnabled then
		humanoid.WalkSpeed = 40
	else
		humanoid.WalkSpeed = Config.Speed
	end

	if Config.JumpEnabled then
		humanoid.JumpPower = 100
	else
		humanoid.JumpPower = Config.Jump
	end

end)

--========================================================
-- ESP
--========================================================

local Highlights = {}

local function ClearHighlights()

	for _,highlight in ipairs(Highlights) do
		highlight:Destroy()
	end

	table.clear(Highlights)

end

local function HighlightObject(object,color)

	if not object:IsA("Model") and not object:IsA("BasePart") then
		return
	end

	local highlight = Instance.new("Highlight")
	highlight.Adornee = object
	highlight.FillColor = color
	highlight.FillTransparency = .65
	highlight.OutlineColor = Color3.new(1,1,1)
	highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	highlight.Parent = object

	table.insert(Highlights,highlight)

end

local function UpdateESP()

	ClearHighlights()

	if Config.PlayerESP then

		for _,p in ipairs(Players:GetPlayers()) do

			if p ~= player and p.Character then
				HighlightObject(
					p.Character,
					Color3.fromRGB(60,150,255)
				)
			end

		end

	end

	local folders = {
		{"NPCs",Config.NPCESP,Color3.fromRGB(255,100,100)},
		{"Enemies",Config.NPCESP,Color3.fromRGB(255,100,100)},
		{"Bosses",Config.BossESP,Color3.fromRGB(190,70,255)},
		{"Fruits",Config.FruitESP,Color3.fromRGB(70,255,120)},
		{"Chests",Config.ChestESP,Color3.fromRGB(255,210,60)},
	}

	for _,data in ipairs(folders) do

		local folder = workspace:FindFirstChild(data[1])

		if folder and data[2] then

			for _,obj in ipairs(folder:GetChildren()) do
				HighlightObject(obj,data[3])
			end

		end

	end

end

task.spawn(function()

	while task.wait(1) do
		UpdateESP()
	end

end)

--========================================================
-- KEYBIND
--========================================================

UIS.InputBegan:Connect(function(input,processed)

	if processed then
		return
	end

	if input.KeyCode == Enum.KeyCode.RightShift then
		Main.Visible = not Main.Visible
	end

end)

--========================================================
-- DRAG
--========================================================

local dragging = false
local dragStart
local startPosition

Title.InputBegan:Connect(function(input)

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

--========================================================
-- DEFAULT TAB
--========================================================

Tabs.Home.Page.Visible = true
Tabs.Home.Button.BackgroundColor3 = Color3.fromRGB(105,45,190)

print("TEST HUB carregado!")
print("RightShift abre/fecha.")

]==]

script.Parent = StarterPlayerScripts

print("✅ TestHub instalado!")
print("▶️ Dê Play para abrir o Hub.")
