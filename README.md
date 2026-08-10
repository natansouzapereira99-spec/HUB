--========================================================
-- ANIME / BLOX TEST HUB
-- SINGLE LOCAL SCRIPT
-- PARA SUA PRÓPRIA EXPERIÊNCIA / TEST PLACE
--========================================================

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local VirtualUser = game:GetService("VirtualUser")
local TeleportService = game:GetService("TeleportService")

local LP = Players.LocalPlayer
local PG = LP:WaitForChild("PlayerGui")

--========================================================
-- CONFIG
--========================================================

local C = {
	AutoFarm = false,
	AutoAttack = false,
	AutoCollect = false,
	AutoQuest = false,

	PlayerESP = false,
	NPCESP = false,
	BossESP = false,
	FruitESP = false,
	ChestESP = false,

	Fly = false,
	NoClip = false,

	WalkSpeed = 16,
	JumpPower = 50,
	FlySpeed = 70,

	TargetMode = "Nearest",
	AttackDistance = 25,

	AntiAFK = true,
}

local Connections = {}
local Highlights = {}
local CurrentTarget = nil
local FlyBV = nil
local FlyConnection = nil

--========================================================
-- HELPERS
--========================================================

local function Character()
	return LP.Character
end

local function Humanoid()
	local c = Character()
	return c and c:FindFirstChildOfClass("Humanoid")
end

local function Root()
	local c = Character()
	return c and c:FindFirstChild("HumanoidRootPart")
end

local function Alive(model)
	if not model then
		return false
	end

	local hum = model:FindFirstChildOfClass("Humanoid")
	local root = model:FindFirstChild("HumanoidRootPart")

	return hum and root and hum.Health > 0
end

local function Distance(a,b)
	if not a or not b then
		return math.huge
	end

	return (a.Position - b.Position).Magnitude
end

local function FindRoot(obj)
	if obj:IsA("Model") then
		return obj:FindFirstChild("HumanoidRootPart")
			or obj.PrimaryPart
			or obj:FindFirstChildWhichIsA("BasePart")
	end

	if obj:IsA("BasePart") then
		return obj
	end

	return nil
end

local function IsPlayerCharacter(model)
	for _,p in ipairs(Players:GetPlayers()) do
		if p.Character == model then
			return true
		end
	end

	return false
end

--========================================================
-- REMOVE OLD GUI
--========================================================

local old = PG:FindFirstChild("MysteryTestHub")

if old then
	old:Destroy()
end

--========================================================
-- GUI
--========================================================

local GUI = Instance.new("ScreenGui")
GUI.Name = "MysteryTestHub"
GUI.ResetOnSpawn = false
GUI.IgnoreGuiInset = true
GUI.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
GUI.Parent = PG

local Main = Instance.new("Frame")
Main.Size = UDim2.fromOffset(920,580)
Main.Position = UDim2.new(.5,-460,.5,-290)
Main.BackgroundColor3 = Color3.fromRGB(9,9,14)
Main.BorderSizePixel = 0
Main.Parent = GUI

Instance.new("UICorner",Main).CornerRadius = UDim.new(0,18)

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(155,60,255)
MainStroke.Thickness = 2
MainStroke.Transparency = .25
MainStroke.Parent = Main

--========================================================
-- TOP
--========================================================

local Top = Instance.new("Frame")
Top.Size = UDim2.new(1,0,0,75)
Top.BackgroundColor3 = Color3.fromRGB(18,18,27)
Top.BorderSizePixel = 0
Top.Parent = Main

Instance.new("UICorner",Top).CornerRadius = UDim.new(0,18)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.fromOffset(450,35)
Title.Position = UDim2.fromOffset(25,8)
Title.BackgroundTransparency = 1
Title.Text = "⚡ ANIME MYSTERY"
Title.TextColor3 = Color3.fromRGB(250,250,255)
Title.Font = Enum.Font.GothamBlack
Title.TextSize = 24
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Top

local Sub = Instance.new("TextLabel")
Sub.Size = UDim2.fromOffset(500,22)
Sub.Position = UDim2.fromOffset(27,43)
Sub.BackgroundTransparency = 1
Sub.Text = "TEST HUB  •  PRIVATE EXPERIENCE"
Sub.TextColor3 = Color3.fromRGB(180,80,255)
Sub.Font = Enum.Font.GothamBold
Sub.TextSize = 10
Sub.TextXAlignment = Enum.TextXAlignment.Left
Sub.Parent = Top

local Online = Instance.new("TextLabel")
Online.Size = UDim2.fromOffset(100,30)
Online.Position = UDim2.new(1,-145,0,22)
Online.BackgroundTransparency = 1
Online.Text = "● ONLINE"
Online.TextColor3 = Color3.fromRGB(70,230,130)
Online.Font = Enum.Font.GothamBold
Online.TextSize = 12
Online.Parent = Top

local Close = Instance.new("TextButton")
Close.Size = UDim2.fromOffset(38,38)
Close.Position = UDim2.new(1,-55,0,18)
Close.BackgroundColor3 = Color3.fromRGB(210,55,75)
Close.Text = "×"
Close.TextColor3 = Color3.new(1,1,1)
Close.Font = Enum.Font.GothamBlack
Close.TextSize = 25
Close.BorderSizePixel = 0
Close.Parent = Top

Instance.new("UICorner",Close).CornerRadius = UDim.new(0,10)

Close.MouseButton1Click:Connect(function()
	Main.Visible = false
end)

--========================================================
-- SIDEBAR
--========================================================

local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.fromOffset(190,485)
Sidebar.Position = UDim2.fromOffset(12,83)
Sidebar.BackgroundColor3 = Color3.fromRGB(13,13,21)
Sidebar.BorderSizePixel = 0
Sidebar.Parent = Main

Instance.new("UICorner",Sidebar).CornerRadius = UDim.new(0,14)

local SidePadding = Instance.new("UIPadding")
SidePadding.PaddingTop = UDim.new(0,12)
SidePadding.PaddingLeft = UDim.new(0,10)
SidePadding.PaddingRight = UDim.new(0,10)
SidePadding.Parent = Sidebar

local SideLayout = Instance.new("UIListLayout")
SideLayout.Padding = UDim.new(0,7)
SideLayout.Parent = Sidebar

--========================================================
-- CONTENT
--========================================================

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1,-215,1,-95)
Content.Position = UDim2.fromOffset(202,83)
Content.BackgroundColor3 = Color3.fromRGB(17,17,26)
Content.BorderSizePixel = 0
Content.Parent = Main

Instance.new("UICorner",Content).CornerRadius = UDim.new(0,14)

local Pages = {}
local Buttons = {}

--========================================================
-- NOTIFICATION
--========================================================

local NotifyFrame = Instance.new("Frame")
NotifyFrame.Size = UDim2.fromOffset(340,65)
NotifyFrame.Position = UDim2.new(1,-360,1,-85)
NotifyFrame.BackgroundColor3 = Color3.fromRGB(23,23,34)
NotifyFrame.BorderSizePixel = 0
NotifyFrame.Visible = false
NotifyFrame.Parent = GUI

Instance.new("UICorner",NotifyFrame).CornerRadius = UDim.new(0,12)

local NotifyText = Instance.new("TextLabel")
NotifyText.Size = UDim2.new(1,-20,1,0)
NotifyText.Position = UDim2.fromOffset(10,0)
NotifyText.BackgroundTransparency = 1
NotifyText.TextColor3 = Color3.new(1,1,1)
NotifyText.Font = Enum.Font.GothamBold
NotifyText.TextSize = 13
NotifyText.TextXAlignment = Enum.TextXAlignment.Left
NotifyText.Parent = NotifyFrame

local notifyId = 0

local function Notify(text)
	notifyId += 1
	local id = notifyId

	NotifyText.Text = "🔔  "..text
	NotifyFrame.Visible = true

	task.delay(2.5,function()
		if id == notifyId then
			NotifyFrame.Visible = false
		end
	end)
end

--========================================================
-- TAB
--========================================================

local function Tab(name,icon)

	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1,0,0,43)
	button.BackgroundColor3 = Color3.fromRGB(22,22,32)
	button.Text = icon.."  "..name
	button.TextColor3 = Color3.fromRGB(175,175,190)
	button.Font = Enum.Font.GothamBold
	button.TextSize = 13
	button.BorderSizePixel = 0
	button.AutoButtonColor = false
	button.Parent = Sidebar

	Instance.new("UICorner",button).CornerRadius = UDim.new(0,9)

	local page = Instance.new("ScrollingFrame")
	page.Size = UDim2.new(1,-24,1,-24)
	page.Position = UDim2.fromOffset(12,12)
	page.BackgroundTransparency = 1
	page.BorderSizePixel = 0
	page.ScrollBarThickness = 3
	page.ScrollBarImageColor3 = Color3.fromRGB(150,60,255)
	page.Visible = false
	page.CanvasSize = UDim2.fromOffset(0,0)
	page.Parent = Content

	local layout = Instance.new("UIListLayout")
	layout.Padding = UDim.new(0,9)
	layout.Parent = page

	local padding = Instance.new("UIPadding")
	padding.PaddingBottom = UDim.new(0,20)
	padding.Parent = page

	layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
		page.CanvasSize = UDim2.fromOffset(
			0,
			layout.AbsoluteContentSize.Y + 30
		)
	end)

	Pages[name] = page
	Buttons[name] = button

	button.MouseButton1Click:Connect(function()

		for n,p in pairs(Pages) do
			p.Visible = false
			Buttons[n].BackgroundColor3 = Color3.fromRGB(22,22,32)
			Buttons[n].TextColor3 = Color3.fromRGB(175,175,190)
		end

		page.Visible = true
		button.BackgroundColor3 = Color3.fromRGB(105,45,190)
		button.TextColor3 = Color3.new(1,1,1)
	end)

	return page
end

local function Section(parent,text)

	local l = Instance.new("TextLabel")
	l.Size = UDim2.new(1,-5,0,35)
	l.BackgroundTransparency = 1
	l.Text = text
	l.TextColor3 = Color3.fromRGB(190,85,255)
	l.Font = Enum.Font.GothamBlack
	l.TextSize = 16
	l.TextXAlignment = Enum.TextXAlignment.Left
	l.Parent = parent

	return l
end

--========================================================
-- TOGGLE
--========================================================

local function Toggle(parent,text,key,callback)

	local b = Instance.new("TextButton")
	b.Size = UDim2.new(1,-5,0,50)
	b.BackgroundColor3 = Color3.fromRGB(25,25,37)
	b.Text = ""
	b.BorderSizePixel = 0
	b.AutoButtonColor = false
	b.Parent = parent

	Instance.new("UICorner",b).CornerRadius = UDim.new(0,10)

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1,-90,1,0)
	label.Position = UDim2.fromOffset(15,0)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.new(1,1,1)
	label.Font = Enum.Font.GothamBold
	label.TextSize = 13
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = b

	local state = Instance.new("TextLabel")
	state.Size = UDim2.fromOffset(52,27)
	state.Position = UDim2.new(1,-68,.5,-13)
	state.Font = Enum.Font.GothamBlack
	state.TextSize = 10
	state.Parent = b

	Instance.new("UICorner",state).CornerRadius = UDim.new(1,0)

	local function update()

		if C[key] then
			state.Text = "ON"
			state.TextColor3 = Color3.new(1,1,1)
			state.BackgroundColor3 = Color3.fromRGB(125,55,220)
		else
			state.Text = "OFF"
			state.TextColor3 = Color3.fromRGB(170,170,185)
			state.BackgroundColor3 = Color3.fromRGB(52,52,66)
		end
	end

	b.MouseButton1Click:Connect(function()

		C[key] = not C[key]
		update()

		if callback then
			callback(C[key])
		end
	end)

	b.MouseEnter:Connect(function()
		TweenService:Create(
			b,
			TweenInfo.new(.12),
			{BackgroundColor3=Color3.fromRGB(35,30,48)}
		):Play()
	end)

	b.MouseLeave:Connect(update)

	update()

	return b
end

--========================================================
-- SLIDER
--========================================================

local function Slider(parent,text,key,min,max)

	local frame = Instance.new("Frame")
	frame.Size = UDim2.new(1,-5,0,70)
	frame.BackgroundColor3 = Color3.fromRGB(25,25,37)
	frame.BorderSizePixel = 0
	frame.Parent = parent

	Instance.new("UICorner",frame).CornerRadius = UDim.new(0,10)

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1,-90,0,30)
	label.Position = UDim2.fromOffset(15,5)
	label.BackgroundTransparency = 1
	label.Text = text
	label.TextColor3 = Color3.new(1,1,1)
	label.Font = Enum.Font.GothamBold
	label.TextSize = 13
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = frame

	local value = Instance.new("TextLabel")
	value.Size = UDim2.fromOffset(65,30)
	value.Position = UDim2.new(1,-78,0,5)
	value.BackgroundTransparency = 1
	value.Text = tostring(C[key])
	value.TextColor3 = Color3.fromRGB(190,85,255)
	value.Font = Enum.Font.GothamBlack
	value.TextSize = 13
	value.Parent = frame

	local bar = Instance.new("Frame")
	bar.Size = UDim2.new(1,-30,0,7)
	bar.Position = UDim2.fromOffset(15,48)
	bar.BackgroundColor3 = Color3.fromRGB(55,55,68)
	bar.BorderSizePixel = 0
	bar.Parent = frame

	Instance.new("UICorner",bar).CornerRadius = UDim.new(1,0)

	local fill = Instance.new("Frame")
	fill.BackgroundColor3 = Color3.fromRGB(145,60,245)
	fill.BorderSizePixel = 0
	fill.Parent = bar

	Instance.new("UICorner",fill).CornerRadius = UDim.new(1,0)

	local function set(v)

		v = math.clamp(v,min,max)
		C[key] = math.floor(v)

		local percent = (v-min)/(max-min)

		fill.Size = UDim2.new(percent,0,1,0)
		value.Text = tostring(C[key])
	end

	set(C[key])

	local dragging = false

	bar.InputBegan:Connect(function(input)

		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true

			local percent = math.clamp(
				(input.Position.X-bar.AbsolutePosition.X)/
				bar.AbsoluteSize.X,
				0,1
			)

			set(min+(max-min)*percent)
		end
	end)

	UIS.InputChanged:Connect(function(input)

		if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then

			local percent = math.clamp(
				(input.Position.X-bar.AbsolutePosition.X)/
				bar.AbsoluteSize.X,
				0,1
			)

			set(min+(max-min)*percent)
		end
	end)

	UIS.InputEnded:Connect(function(input)

		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = false
		end
	end)
end

--========================================================
-- PAGES
--========================================================

local Home = Tab("Home","🏠")
local Farm = Tab("Farm","⚔️")
local Combat = Tab("Combat","💥")
local ESP = Tab("ESP","👁️")
local Items = Tab("Items","📦")
local Tele = Tab("Teleport","🌀")
local Movement = Tab("Movement","🪽")
local Settings = Tab("Settings","⚙️")

--========================================================
-- HOME
--========================================================

Section(Home,"⚡ DASHBOARD")

local card = Instance.new("Frame")
card.Size = UDim2.new(1,-5,0,130)
card.BackgroundColor3 = Color3.fromRGB(25,25,37)
card.BorderSizePixel = 0
card.Parent = Home

Instance.new("UICorner",card).CornerRadius = UDim.new(0,13)

local txt = Instance.new("TextLabel")
txt.Size = UDim2.new(1,-30,1,-20)
txt.Position = UDim2.fromOffset(15,10)
txt.BackgroundTransparency = 1
txt.Text =
	"⚡ TEST HUB\n\n"..
	"Painel para testar sua própria experiência.\n"..
	"RightShift abre/fecha o painel."
txt.TextColor3 = Color3.new(1,1,1)
txt.Font = Enum.Font.GothamBold
txt.TextSize = 15
txt.TextXAlignment = Enum.TextXAlignment.Left
txt.Parent = card

Section(Home,"📊 PLAYER")

local PlayerInfo = Instance.new("TextLabel")
PlayerInfo.Size = UDim2.new(1,-5,0,70)
PlayerInfo.BackgroundColor3 = Color3.fromRGB(25,25,37)
PlayerInfo.TextColor3 = Color3.fromRGB(220,220,230)
PlayerInfo.Font = Enum.Font.GothamBold
PlayerInfo.TextSize = 13
PlayerInfo.TextXAlignment = Enum.TextXAlignment.Left
PlayerInfo.Parent = Home

Instance.new("UICorner",PlayerInfo).CornerRadius = UDim.new(0,10)

--========================================================
-- FARM
--========================================================

Section(Farm,"⚔️ FARM")

Toggle(Farm,"🤖 Auto Farm","AutoFarm",function(v)
	Notify(v and "Auto Farm ativado" or "Auto Farm desativado")
end)

Toggle(Farm,"⚔️ Auto Attack","AutoAttack",function(v)
	Notify(v and "Auto Attack ativado" or "Auto Attack desativado")
end)

Toggle(Farm,"🎁 Auto Collect","AutoCollect",function(v)
	Notify(v and "Auto Collect ativado" or "Auto Collect desativado")
end)

Toggle(Farm,"📜 Auto Quest","AutoQuest",function(v)
	Notify(v and "Auto Quest ativado" or "Auto Quest desativado")
end)

--========================================================
-- COMBAT
--========================================================

Section(Combat,"💥 COMBAT")

Slider(Combat,"🎯 Attack Distance","AttackDistance",10,100)

Toggle(Combat,"⚔️ Auto Attack","AutoAttack")

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
-- ITEMS
--========================================================

Section(Items,"📦 ITEMS")

Toggle(Items,"🍎 Fruit Finder","FruitESP")

Toggle(Items,"📦 Chest Finder","ChestESP")

Toggle(Items,"🧲 Auto Collect","AutoCollect")

--========================================================
-- MOVEMENT
--========================================================

Section(Movement,"🪽 MOVEMENT")

Slider(Movement,"💨 WalkSpeed","WalkSpeed",16,150)

Slider(Movement,"🦘 JumpPower","JumpPower",50,200)

Slider(Movement,"🕊️ Fly Speed","FlySpeed",30,150)

Toggle(Movement,"👻 NoClip","NoClip")

Toggle(Movement,"🕊️ Fly","Fly",function(v)

	if v then
		Notify("Fly ativado")
	else
		Notify("Fly desativado")
	end
end)

--========================================================
-- TELEPORT
--========================================================

Section(Tele,"🌀 TELEPORT")

local function MakeTeleport(name)

	local b = Instance.new("TextButton")
	b.Size = UDim2.new(1,-5,0,47)
	b.BackgroundColor3 = Color3.fromRGB(25,25,37)
	b.Text = "🌀  "..name
	b.TextColor3 = Color3.new(1,1,1)
	b.Font = Enum.Font.GothamBold
	b.TextSize = 13
	b.TextXAlignment = Enum.TextXAlignment.Left
	b.BorderSizePixel = 0
	b.Parent = Tele

	Instance.new("UICorner",b).CornerRadius = UDim.new(0,10)

	local p = Instance.new("UIPadding")
	p.PaddingLeft = UDim.new(0,15)
	p.Parent = b

	b.MouseButton1Click:Connect(function()

		local possibleFolders = {
			workspace:FindFirstChild("TeleportPoints"),
			workspace:FindFirstChild("SpawnLocations"),
			workspace:FindFirstChild("Locations"),
		}

		local point

		for _,folder in ipairs(possibleFolders) do

			if folder then

				point = folder:FindFirstChild(name)

				if point then
					break
				end
			end
		end

		if not point then

			for _,obj in ipairs(workspace:GetDescendants()) do

				if obj.Name:lower() == name:lower() then

					local r = FindRoot(obj)

					if r then
						point = r
						break
					end
				end
			end
		end

		local root = Root()

		if root and point then

			root.CFrame = point.CFrame + Vector3.new(0,5,0)
			Notify("Teleportado para "..name)

		else
			Notify("Ponto '"..name.."' não encontrado")
		end
	end)
end

for _,name in ipairs({
	"Lobby",
	"Marine",
	"Jungle",
	"Desert",
	"Frozen",
	"Volcano",
	"Castle",
	"Arena"
}) do
	MakeTeleport(name)
end

--========================================================
-- SETTINGS
--========================================================

Section(Settings,"⚙️ UTILITIES")

Toggle(Settings,"💤 Anti-AFK","AntiAFK")

local Rejoin = Instance.new("TextButton")
Rejoin.Size = UDim2.new(1,-5,0,47)
Rejoin.BackgroundColor3 = Color3.fromRGB(25,25,37)
Rejoin.Text = "🔄  Rejoin"
Rejoin.TextColor3 = Color3.new(1,1,1)
Rejoin.Font = Enum.Font.GothamBold
Rejoin.TextSize = 13
Rejoin.BorderSizePixel = 0
Rejoin.Parent = Settings

Instance.new("UICorner",Rejoin).CornerRadius = UDim.new(0,10)

Rejoin.MouseButton1Click:Connect(function()

	Notify("Rejoin...")

	task.wait(.5)

	TeleportService:Teleport(
		game.PlaceId,
		LP
	)
end)

local Reset = Instance.new("TextButton")
Reset.Size = UDim2.new(1,-5,0,47)
Reset.BackgroundColor3 = Color3.fromRGB(25,25,37)
Reset.Text = "♻️  Reset Character"
Reset.TextColor3 = Color3.new(1,1,1)
Reset.Font = Enum.Font.GothamBold
Reset.TextSize = 13
Reset.BorderSizePixel = 0
Reset.Parent = Settings

Instance.new("UICorner",Reset).CornerRadius = UDim.new(0,10)

Reset.MouseButton1Click:Connect(function()

	local h = Humanoid()

	if h then
		h.Health = 0
	end
end)

--========================================================
-- ESP
--========================================================

local function ClearESP()

	for _,h in ipairs(Highlights) do

		if h and h.Parent then
			h:Destroy()
		end
	end

	table.clear(Highlights)
end

local function AddHighlight(obj,color)

	if not obj then
		return
	end

	local h = Instance.new("Highlight")
	h.Name = "MysteryESP"
	h.Adornee = obj
	h.FillColor = color
	h.OutlineColor = Color3.new(1,1,1)
	h.FillTransparency = .65
	h.OutlineTransparency = .1
	h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	h.Parent = obj

	table.insert(Highlights,h)
end

local function NameLooks(obj,list)

	local n = obj.Name:lower()

	for _,word in ipairs(list) do

		if n:find(word) then
			return true
		end
	end

	return false
end

local function UpdateESP()

	ClearESP()

	for _,obj in ipairs(workspace:GetDescendants()) do

		if obj:IsA("Model") then

			if Config.BossESP and NameLooks(obj,{
				"boss",
				"raid boss",
				"sea beast"
			}) then

				AddHighlight(
					obj,
					Color3.fromRGB(200,60,255)
				)

			elseif Config.NPCESP
				and Alive(obj)
				and not IsPlayerCharacter(obj)
				and not NameLooks(obj,{"player"}) then

				AddHighlight(
					obj,
					Color3.fromRGB(255,80,80)
				)
			end
		end

		local n = obj.Name:lower()

		if Config.FruitESP and (
			n:find("fruit")
			or n:find("devil")
		) then

			AddHighlight(
				obj,
				Color3.fromRGB(70,255,120)
			)
		end

		if Config.ChestESP and n:find("chest") then

			AddHighlight(
				obj,
				Color3.fromRGB(255,210,60)
			)
		end
	end

	if Config.PlayerESP then

		for _,p in ipairs(Players:GetPlayers()) do

			if p ~= LP and p.Character then

				AddHighlight(
					p.Character,
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

--========================================================
-- TARGET FINDER
--========================================================

local function GetNearestEnemy()

	local root = Root()

	if not root then
		return nil
	end

	local nearest
	local best = math.huge

	for _,obj in ipairs(workspace:GetDescendants()) do

		if obj:IsA("Model")
			and Alive(obj)
			and not IsPlayerCharacter(obj) then

			local enemyRoot = FindRoot(obj)

			if enemyRoot then

				local d = Distance(root,enemyRoot)

				if d < best then
					best = d
					nearest = obj
				end
			end
		end
	end

	return nearest
end

--========================================================
-- TOOL ATTACK
--========================================================

local function GetTool()

	local character = Character()

	if not character then
		return nil
	end

	for _,obj in ipairs(character:GetChildren()) do

		if obj:IsA("Tool") then
			return obj
		end
	end

	local backpack = LP:FindFirstChildOfClass("Backpack")

	if backpack then

		for _,obj in ipairs(backpack:GetChildren()) do

			if obj:IsA("Tool") then

				local h = Humanoid()

				if h then
					h:EquipTool(obj)
					return obj
				end
			end
		end
	end

	return nil
end

local attackCooldown = 0

local function AttackTarget(target)

	if not target then
		return
	end

	local root = Root()
	local targetRoot = FindRoot(target)

	if not root or not targetRoot then
		return
	end

	if Distance(root,targetRoot) > C.AttackDistance then

		root.CFrame =
			targetRoot.CFrame *
			CFrame.new(0,0,C.AttackDistance * .7)

	end

	if os.clock() - attackCooldown < .25 then
		return
	end

	attackCooldown = os.clock()

	local tool = GetTool()

	if tool then
		pcall(function()
			tool:Activate()
		end)
	end
end

--========================================================
-- AUTO FARM
--========================================================

task.spawn(function()

	while task.wait(.15) do

		if C.AutoFarm then

			local target = GetNearestEnemy()

			CurrentTarget = target

			if target then
				AttackTarget(target)
			end
		end
	end
end)

--========================================================
-- AUTO ATTACK
--========================================================

task.spawn(function()

	while task.wait(.15) do

		if C.AutoAttack and not C.AutoFarm then

			local target = GetNearestEnemy()

			if target then
				AttackTarget(target)
			end
		end
	end
end)

--========================================================
-- AUTO COLLECT
--========================================================

local function ActivatePrompt(prompt)

	pcall(function()

		if prompt:IsA("ProximityPrompt") then
			fireproximityprompt(prompt)
		elseif prompt:IsA("ClickDetector") then
			fireclickdetector(prompt)
		end

	end)
end

task.spawn(function()

	while task.wait(.25) do

		if C.AutoCollect then

			for _,obj in ipairs(workspace:GetDescendants()) do

				if obj:IsA("ProximityPrompt")
					or obj:IsA("ClickDetector") then

					ActivatePrompt(obj)
				end
			end
		end
	end
end)

--========================================================
-- AUTO QUEST
--========================================================

task.spawn(function()

	while task.wait(.5) do

		if C.AutoQuest then

			for _,obj in ipairs(workspace:GetDescendants()) do

				if obj:IsA("ProximityPrompt") then

					local n = obj.Name:lower()

					if n:find("quest")
						or n:find("talk")
						or n:find("mission")
						or n:find("accept") then

						ActivatePrompt(obj)
					end
				end
			end
		end
	end
end)

--========================================================
-- MOVEMENT
--========================================================

RunService.Heartbeat:Connect(function()

	local h = Humanoid()

	if h then
		h.WalkSpeed = C.WalkSpeed
		h.JumpPower = C.JumpPower
	end
end)

--========================================================
-- NOCLIP
--========================================================

RunService.Stepped:Connect(function()

	if not C.NoClip then
		return
	end

	local character = Character()

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
-- FLY
--========================================================

local function StopFly()

	if FlyConnection then
		FlyConnection:Disconnect()
		FlyConnection = nil
	end

	if FlyBV then
		FlyBV:Destroy()
		FlyBV = nil
	end
end

local function StartFly()

	StopFly()

	local root = Root()

	if not root then
		return
	end

	FlyBV = Instance.new("BodyVelocity")
	FlyBV.MaxForce = Vector3.new(
		math.huge,
		math.huge,
		math.huge
	)
	FlyBV.Velocity = Vector3.zero
	FlyBV.Parent = root

	FlyConnection = RunService.RenderStepped:Connect(function()

		if not C.Fly or not Root() then
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
			direction += Vector3.yAxis
		end

		if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
			direction -= Vector3.yAxis
		end

		if direction.Magnitude > 0 then
			direction = direction.Unit * C.FlySpeed
		end

		FlyBV.Velocity = direction
	end)
end

task.spawn(function()

	while task.wait(.2) do

		if C.Fly and not FlyConnection then
			StartFly()
		elseif not C.Fly and FlyConnection then
			StopFly()
		end
	end
end)

--========================================================
-- ANTI AFK
--========================================================

LP.Idled:Connect(function()

	if C.AntiAFK then

		VirtualUser:CaptureController()
		VirtualUser:ClickButton2(
			Vector2.new(
				workspace.CurrentCamera.ViewportSize.X/2,
				workspace.CurrentCamera.ViewportSize.Y/2
			)
		)
	end
end)

--========================================================
-- PLAYER INFO
--========================================================

task.spawn(function()

	while task.wait(.5) do

		local h = Humanoid()

		if h then

			local targetName =
				CurrentTarget and CurrentTarget.Name
				or "Nenhum"

			-- Mantém o título atualizado
			Sub.Text =
				"TARGET: "..targetName..
				"  •  HP: "..math.floor(h.Health)
		end
	end
end)

--========================================================
-- DRAG
--========================================================

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

UIS.InputChanged:Connect(function(input)

	if dragging
		and input.UserInputType == Enum.UserInputType.MouseMovement then

		local delta = input.Position - dragStart

		Main.Position = UDim2.new(
			startPosition.X.Scale,
			startPosition.X.Offset + delta.X,
			startPosition.Y.Scale,
			startPosition.Y.Offset + delta.Y
		)
	end
end)

UIS.InputEnded:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = false
	end
end)

--========================================================
-- RIGHT SHIFT
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
-- DEFAULT
--========================================================

Pages.Home.Visible = true
Buttons.Home.BackgroundColor3 = Color3.fromRGB(105,45,190)
Buttons.Home.TextColor3 = Color3.new(1,1,1)

Notify("Hub carregado com sucesso!")

print("==========================================")
print("⚡ ANIME MYSTERY TEST HUB")
print("✅ CARREGADO")
print("⌨️ RIGHT SHIFT = ABRIR / FECHAR")
print("==========================================")
