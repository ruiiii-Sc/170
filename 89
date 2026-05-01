local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local VirtualUser = game:GetService("VirtualUser")
local LocalPlayer = Players.LocalPlayer
local PlaceBlockE = ReplicatedStorage:WaitForChild("PlaceBlockE")

pcall(function()
	if LocalPlayer.PlayerGui:FindFirstChild("BreakBlockHub") then
		LocalPlayer.PlayerGui.BreakBlockHub:Destroy()
	end
end)

_G.LoopRegistry = _G.LoopRegistry or { Count = 0, Active = {} }
_G.LoopRegistry.Count = _G.LoopRegistry.Count + 1
if _G.LoopRegistry.Count > 2 then
	for _, v in pairs(_G.LoopRegistry.Active) do
		pcall(function()
			if typeof(v) == "RBXScriptConnection" then v:Disconnect()
			elseif typeof(v) == "thread" then task.cancel(v) end
		end)
	end
	_G.LoopRegistry.Active = {}
	_G.LoopRegistry.Count = 1
	error("[BreakBlockHub] Duplicate instance detected and terminated.", 0)
	return
end

local pgOk, globals = pcall(function()
	return require(LocalPlayer.PlayerScripts.Modules.PlayerGlobals)
end)

local Config = {
	Gap                 = 0,
	Amount              = 10,
	DefaultSource       = "Player",
	InstantBreak        = false,
	MageAnim            = false,
	InfZoom             = false,
	InfRange            = false,
	Inviscam            = false,
	WalkSpeed           = false,
	WalkSpeedValue      = 40,
	InfJump             = false,
	Noclip              = false,
	InfItemLimit        = false,
	AntiAfk             = true,
	DisableFlowingWater = true,
	Disable3DRendering  = false,
	FlySpeed            = 30,
}

local UI = {}
UI.Config    = Config
UI.pgOk      = pgOk
UI.globals   = globals
UI.PlaceBlockE = PlaceBlockE
UI.Players   = Players
UI.ReplicatedStorage = ReplicatedStorage
UI.RunService = RunService
UI.UIS       = UIS
UI.TweenService = TweenService
UI.VirtualUser = VirtualUser
UI.LocalPlayer = LocalPlayer

UI.stateInstantBreak   = Config.InstantBreak
UI.stateMageAnim       = Config.MageAnim
UI.stateInfZoom        = Config.InfZoom
UI.stateInfRange       = Config.InfRange
UI.stateInviscam       = Config.Inviscam
UI.stateWalkSpeed      = Config.WalkSpeed
UI.customWalkSpeed     = Config.WalkSpeedValue
UI.stateInfJump        = Config.InfJump
UI.stateNoclip         = Config.Noclip
UI.stateInfItemLimit   = Config.InfItemLimit
UI.stateAntiAfk        = Config.AntiAfk
UI.stateFlowingWater   = Config.DisableFlowingWater
UI.stateDisable3DRender = Config.Disable3DRendering
UI.flySpeed            = Config.FlySpeed

UI.infJumpConn      = nil
UI.antiAfkConn      = nil
UI.lastBreakArgs    = nil
UI.isBreaking       = false
UI.breakHook        = nil
UI.breakLoop        = nil
UI.mageAnimLoop     = nil
UI.flowingWaterConn = nil
UI.masterSteppedConn = nil
UI.flyConn          = nil
UI.flyRSConn        = nil
UI.flying           = false
UI.linearVel        = nil
UI.aForce           = nil
UI.flyAttach        = nil

UI.selectedItem    = nil
UI.selectedItemRef = nil
UI.isBuilding      = false
UI.stopBuilding    = false
UI.sourceModes     = { "Player", "Block", "Breaker" }
UI.sourceModeIdx   = 1
UI.sourceMode      = "Player"
UI.isStartFrozen   = false
UI.isEndFrozen     = false
UI.minimized       = false
UI.extrasOpen      = false
UI.currentFace     = Enum.NormalId.Front
UI.currentDirName  = "Front"
UI.directionFaces  = {
	Front  = Enum.NormalId.Front,  Back   = Enum.NormalId.Back,
	Left   = Enum.NormalId.Left,   Right  = Enum.NormalId.Right,
	Top    = Enum.NormalId.Top,    Bottom = Enum.NormalId.Bottom,
}
UI.dirOrder = { "Front", "Back", "Left", "Right", "Top", "Bottom" }

UI.breakerQueue   = {}
UI.breakerRunning = false
UI.breakerLoop    = nil
UI.BREAKER_RATE   = 35
UI.BREAKER_INTERVAL = 0

UI.isFilling       = false
UI.stopFill        = false
UI.fillPreviewParts = {}

local function snap3(v) return math.floor((v + 1.5) / 3) * 3 end
local function sign(n)  return n > 0 and 1 or (n < 0 and -1 or 0) end
UI.snap3 = snap3
UI.sign  = sign

local function getGroundY()
	local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
	if not hrp then return 45 end
	local rp = RaycastParams.new()
	rp.FilterType = Enum.RaycastFilterType.Exclude
	rp.FilterDescendantsInstances = { LocalPlayer.Character }
	local rr = workspace:Raycast(hrp.Position, Vector3.new(0, -500, 0), rp)
	return snap3(rr and rr.Position.Y or 42) + 3
end

local function getBlockPos()
	local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
	if not hrp then return Vector3.new(0, 45, 0) end
	local p = hrp.Position
	return Vector3.new(snap3(p.X), snap3(p.Y), snap3(p.Z))
end

UI.getGroundY  = getGroundY
UI.getBlockPos = getBlockPos

local FRAME_W  = 299
local TOPBAR_H = 40
local FRAME_H  = 310
UI.FRAME_W  = FRAME_W
UI.TOPBAR_H = TOPBAR_H
UI.FRAME_H  = FRAME_H

local ScreenGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
ScreenGui.Name         = "BreakBlockHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 10
UI.ScreenGui = ScreenGui

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size             = UDim2.fromOffset(FRAME_W, FRAME_H)
Frame.Position         = UDim2.fromScale(0.5, 0.5)
Frame.AnchorPoint      = Vector2.new(0.5, 0.5)
Frame.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
Frame.BorderSizePixel  = 0
Frame.Active           = true
Instance.new("UICorner", Frame).CornerRadius = UDim.new(0, 10)
local FrameStroke = Instance.new("UIStroke", Frame)
FrameStroke.Color     = Color3.fromRGB(55, 55, 62)
FrameStroke.Thickness = 4.2
UI.Frame = Frame

local Header = Instance.new("Frame", Frame)
Header.Size             = UDim2.new(1, 0, 0, TOPBAR_H)
Header.BackgroundColor3 = Color3.fromRGB(28, 28, 34)
Header.BorderSizePixel  = 0
Header.ZIndex           = 5
Instance.new("UICorner", Header).CornerRadius = UDim.new(0, 10)
UI.Header = Header

local HeaderFix = Instance.new("Frame", Header)
HeaderFix.Size             = UDim2.new(1, 0, 0.5, 0)
HeaderFix.Position         = UDim2.new(0, 0, 0.5, 0)
HeaderFix.BackgroundColor3 = Color3.fromRGB(28, 28, 34)
HeaderFix.BorderSizePixel  = 0
HeaderFix.ZIndex           = 4

local CC = Instance.new("Frame", Header)
CC.Size             = UDim2.new(0, 0, 1, 0)
CC.Position         = UDim2.new(0.5, 0, 0, 0)
CC.AnchorPoint      = Vector2.new(0.5, 0)
CC.BackgroundTransparency = 1

local SourcePill = Instance.new("TextButton", CC)
SourcePill.Size             = UDim2.fromOffset(60, 22)
SourcePill.Position         = UDim2.fromOffset(-135, 9)
SourcePill.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
SourcePill.Text             = UI.sourceMode
SourcePill.TextColor3       = Color3.fromRGB(180, 180, 180)
SourcePill.Font             = Enum.Font.FredokaOne
SourcePill.TextSize         = 12
SourcePill.ZIndex           = 6
Instance.new("UICorner", SourcePill).CornerRadius = UDim.new(0, 6)
UI.SourcePill = SourcePill

local ItemPill = Instance.new("TextLabel", CC)
ItemPill.AutomaticSize    = Enum.AutomaticSize.X
ItemPill.Size             = UDim2.fromOffset(0, 22)
ItemPill.Position         = UDim2.fromOffset(-72, 9)
ItemPill.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
ItemPill.Text             = "..."
ItemPill.TextColor3       = Color3.fromRGB(140, 140, 160)
ItemPill.Font             = Enum.Font.FredokaOne
ItemPill.TextSize         = 13
ItemPill.ZIndex           = 6
local ipPad = Instance.new("UIPadding", ItemPill)
ipPad.PaddingLeft  = UDim.new(0, 6)
ipPad.PaddingRight = UDim.new(0, 6)
Instance.new("UICorner", ItemPill).CornerRadius = UDim.new(0, 6)
UI.ItemPill = ItemPill

local function fadeText(label, newText)
	TweenService:Create(label, TweenInfo.new(0.08, Enum.EasingStyle.Quad, Enum.EasingDirection.In), { TextTransparency = 1 }):Play()
	task.delay(0.09, function()
		label.Text = newText
		TweenService:Create(label, TweenInfo.new(0.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), { TextTransparency = 0 }):Play()
	end)
end
UI.fadeText = fadeText

local function makeTraffic(xOff, rgb)
	local b = Instance.new("TextButton", Header)
	b.Size             = UDim2.fromOffset(13, 13)
	b.Position         = UDim2.new(1, xOff, 0.5, -7)
	b.BackgroundColor3 = Color3.fromRGB(rgb[1], rgb[2], rgb[3])
	b.Text             = ""
	b.AutoButtonColor  = false
	b.ZIndex           = 6
	Instance.new("UICorner", b).CornerRadius = UDim.new(1, 0)
	return b
end

UI.GreenBtn  = makeTraffic(-54, { 39, 201, 63 })
UI.YellowBtn = makeTraffic(-36, { 255, 189, 46 })
UI.RedBtn    = makeTraffic(-19, { 254, 94, 86 })

local PROG_H = 5
local ProgBg = Instance.new("Frame", Frame)
ProgBg.Size             = UDim2.new(1, -6, 0, PROG_H)
ProgBg.Position         = UDim2.fromOffset(4, TOPBAR_H - 8)
ProgBg.BackgroundColor3 = Color3.fromRGB(28, 28, 34)
ProgBg.BorderSizePixel  = 0
ProgBg.ZIndex           = 6
Instance.new("UICorner", ProgBg).CornerRadius = UDim.new(1, 0)
UI.ProgBg = ProgBg

local ProgFill = Instance.new("Frame", ProgBg)
ProgFill.Size             = UDim2.fromScale(0, 1)
ProgFill.BackgroundColor3 = Color3.fromRGB(39, 201, 63)
ProgFill.BorderSizePixel  = 0
ProgFill.ZIndex           = 7
Instance.new("UICorner", ProgFill).CornerRadius = UDim.new(1, 0)
UI.ProgFill = ProgFill

local function setProgress(pct, colRgb)
	local col = colRgb and Color3.fromRGB(colRgb[1], colRgb[2], colRgb[3]) or ProgFill.BackgroundColor3
	TweenService:Create(ProgFill, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
		Size             = UDim2.fromScale(math.clamp(pct, 0, 1), 1),
		BackgroundColor3 = col,
	}):Play()
end
UI.setProgress = setProgress

local CONTENT_Y = TOPBAR_H + PROG_H + 10
local ROW_H     = 24
UI.CONTENT_Y = CONTENT_Y
UI.ROW_H     = ROW_H

local function createRow(labelTxt, yPos)
	local lbl = Instance.new("TextLabel", Frame)
	lbl.Size              = UDim2.fromOffset(38, ROW_H)
	lbl.Position          = UDim2.fromOffset(10, yPos)
	lbl.BackgroundTransparency = 1
	lbl.Text              = labelTxt
	lbl.TextColor3        = Color3.fromRGB(130, 130, 145)
	lbl.Font              = Enum.Font.FredokaOne
	lbl.TextSize          = 11
	local function mkBox(col)
		local t = Instance.new("TextBox", Frame)
		t.Size             = UDim2.fromOffset(44, ROW_H)
		t.Position         = UDim2.fromOffset(50 + col * 48, yPos)
		t.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
		t.Text             = "0"
		t.TextColor3       = Color3.fromRGB(230, 230, 230)
		t.Font             = Enum.Font.FredokaOne
		t.TextSize         = 11
		t.ClearTextOnFocus = false
		Instance.new("UICorner", t).CornerRadius = UDim.new(0, 5)
		local s = Instance.new("UIStroke", t); s.Color = Color3.fromRGB(50, 50, 60); s.Thickness = 1
		return t
	end
	local tx, ty, tz = mkBox(0), mkBox(1), mkBox(2)
	local freeze = Instance.new("TextButton", Frame)
	freeze.Size             = UDim2.fromOffset(24, ROW_H)
	freeze.Position         = UDim2.fromOffset(196, yPos)
	freeze.BackgroundColor3 = Color3.fromRGB(38, 38, 50)
	freeze.Text             = "❄️"
	freeze.Font             = Enum.Font.FredokaOne
	freeze.TextSize         = 12
	freeze.TextColor3       = Color3.fromRGB(255, 255, 255)
	Instance.new("UICorner", freeze).CornerRadius = UDim.new(0, 5)
	local reset = Instance.new("TextButton", Frame)
	reset.Size             = UDim2.fromOffset(24, ROW_H)
	reset.Position         = UDim2.fromOffset(224, yPos)
	reset.BackgroundColor3 = Color3.fromRGB(50, 32, 32)
	reset.Text             = "X"
	reset.Font             = Enum.Font.FredokaOne
	reset.TextSize         = 12
	reset.TextColor3       = Color3.fromRGB(255, 80, 80)
	Instance.new("UICorner", reset).CornerRadius = UDim.new(0, 5)
	reset.MouseButton1Click:Connect(function()
		tx.Text = "0"; ty.Text = "0"; tz.Text = "0"
	end)
	return tx, ty, tz, freeze, reset, lbl
end

UI.sX, UI.sY, UI.sZ, UI.sFreeze, UI.sReset, UI.sLbl = createRow("START", CONTENT_Y - 6)
UI.eX, UI.eY, UI.eZ, UI.eFreeze, UI.eReset, UI.eLbl = createRow("END",   CONTENT_Y + 24)

local Div = Instance.new("Frame", Frame)
Div.Size             = UDim2.new(1, -20, 0, 1)
Div.Position         = UDim2.fromOffset(10, CONTENT_Y + 57)
Div.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
Div.BorderSizePixel  = 0
UI.Div = Div

local LIST_Y = CONTENT_Y + 67
local LIST_H = FRAME_H - LIST_Y - 6
UI.LIST_Y = LIST_Y
UI.LIST_H = LIST_H

local ItemScroll = Instance.new("ScrollingFrame", Frame)
ItemScroll.Size                 = UDim2.fromOffset(140, LIST_H)
ItemScroll.Position             = UDim2.fromOffset(10, LIST_Y)
ItemScroll.BackgroundColor3     = Color3.fromRGB(22, 22, 28)
ItemScroll.BorderSizePixel      = 0
ItemScroll.ScrollBarThickness   = 0
ItemScroll.ClipsDescendants     = true
Instance.new("UICorner", ItemScroll).CornerRadius = UDim.new(0, 6)
local UIList = Instance.new("UIListLayout", ItemScroll)
UIList.Padding = UDim.new(0, 2)
local UIPad = Instance.new("UIPadding", ItemScroll)
UIPad.PaddingTop   = UDim.new(0, 3)
UIPad.PaddingLeft  = UDim.new(0, 3)
UIPad.PaddingRight = UDim.new(0, 3)
UI.ItemScroll = ItemScroll
UI.UIList     = UIList

local CtrlX  = 157
local CTRL_W = FRAME_W - CtrlX - 10
UI.CtrlX  = CtrlX
UI.CTRL_W = CTRL_W

local QtyBox = Instance.new("TextBox", Frame)
QtyBox.Size             = UDim2.fromOffset(CTRL_W, 26)
QtyBox.Position         = UDim2.fromOffset(CtrlX, LIST_Y)
QtyBox.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
QtyBox.Text             = tostring(Config.Amount)
QtyBox.TextColor3       = Color3.fromRGB(230, 230, 230)
QtyBox.Font             = Enum.Font.FredokaOne
QtyBox.TextSize         = 14
QtyBox.ClearTextOnFocus = false
Instance.new("UICorner", QtyBox).CornerRadius = UDim.new(0, 6)
local QtyStroke = Instance.new("UIStroke", QtyBox)
QtyStroke.Color     = Color3.fromRGB(50, 50, 65)
QtyStroke.Thickness = 1
UI.QtyBox = QtyBox

local GapBox = Instance.new("TextBox", Frame)
GapBox.Size             = UDim2.fromOffset(CTRL_W, 18)
GapBox.Position         = UDim2.fromOffset(CtrlX, LIST_Y + 41)
GapBox.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
GapBox.Text             = tostring(Config.Gap)
GapBox.TextColor3       = Color3.fromRGB(230, 230, 230)
GapBox.Font             = Enum.Font.FredokaOne
GapBox.TextSize         = 12
GapBox.ClearTextOnFocus = false
Instance.new("UICorner", GapBox).CornerRadius = UDim.new(0, 6)
local GapStroke = Instance.new("UIStroke", GapBox)
GapStroke.Color     = Color3.fromRGB(50, 50, 65)
GapStroke.Thickness = 1
UI.GapBox = GapBox

local function mkCtrlBtn(yOff, h, bgRgb, txt, fs)
	local b = Instance.new("TextButton", Frame)
	b.Size             = UDim2.fromOffset(CTRL_W, h)
	b.Position         = UDim2.fromOffset(CtrlX, LIST_Y + yOff)
	b.BackgroundColor3 = Color3.fromRGB(bgRgb[1], bgRgb[2], bgRgb[3])
	b.Text             = txt
	b.TextColor3       = Color3.fromRGB(240, 240, 240)
	b.Font             = Enum.Font.FredokaOne
	b.TextSize         = fs or 13
	b.AutoButtonColor  = false
	Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
	return b
end

UI.DirBtn    = mkCtrlBtn(62, 26,          { 38, 38, 48 },  "Front ▾", 12)
UI.ActionBtn = mkCtrlBtn(94, LIST_H - 93, { 45, 115, 55 }, "BUILD",   18)

local DirMenu = Instance.new("Frame", ScreenGui)
DirMenu.Size             = UDim2.fromOffset(130, 0)
DirMenu.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
DirMenu.Visible          = false
DirMenu.ZIndex           = 30
DirMenu.ClipsDescendants = true
Instance.new("UICorner", DirMenu).CornerRadius = UDim.new(0, 8)
local DirMenuStr = Instance.new("UIStroke", DirMenu)
DirMenuStr.Color = Color3.fromRGB(55, 55, 70); DirMenuStr.Thickness = 1
Instance.new("UIListLayout", DirMenu).Padding = UDim.new(0, 1)
local dmp = Instance.new("UIPadding", DirMenu)
dmp.PaddingTop = UDim.new(0, 4); dmp.PaddingBottom = UDim.new(0, 4)
dmp.PaddingLeft = UDim.new(0, 4); dmp.PaddingRight = UDim.new(0, 4)
UI.DirMenu = DirMenu

local DIR_ITEM_H = 24
local DIR_FULL_H = #UI.dirOrder * (DIR_ITEM_H + 1) + 8
local dirMenuOpen = false
UI.dirMenuOpen = false

local function setDirMenuVisible(open)
	dirMenuOpen = open
	UI.dirMenuOpen = open
	if open then
		DirMenu.Visible = true
		DirMenu.Size    = UDim2.fromOffset(130, 0)
		TweenService:Create(DirMenu, TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
			Size = UDim2.fromOffset(130, DIR_FULL_H)
		}):Play()
	else
		TweenService:Create(DirMenu, TweenInfo.new(0.16, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {
			Size = UDim2.fromOffset(130, 0)
		}):Play()
		task.delay(0.16, function() if not dirMenuOpen then DirMenu.Visible = false end end)
	end
end
UI.setDirMenuVisible = setDirMenuVisible

for _, dirName in ipairs(UI.dirOrder) do
	local item = Instance.new("TextButton", DirMenu)
	item.Size             = UDim2.new(1, 0, 0, DIR_ITEM_H)
	item.BackgroundColor3 = Color3.fromRGB(38, 38, 50)
	item.Text             = dirName
	item.TextColor3       = Color3.fromRGB(210, 210, 210)
	item.Font             = Enum.Font.FredokaOne
	item.TextSize         = 12
	item.ZIndex           = 31
	item.AutoButtonColor  = false
	Instance.new("UICorner", item).CornerRadius = UDim.new(0, 5)
	item.MouseEnter:Connect(function() item.BackgroundColor3 = Color3.fromRGB(55, 55, 75) end)
	item.MouseLeave:Connect(function() item.BackgroundColor3 = Color3.fromRGB(38, 38, 50) end)
	item.MouseButton1Click:Connect(function()
		UI.currentFace    = UI.directionFaces[dirName]
		UI.currentDirName = dirName
		UI.DirBtn.Text    = dirName .. " ▾"
		setDirMenuVisible(false)
	end)
end

UI.DirBtn.MouseButton1Click:Connect(function()
	local abs = UI.DirBtn.AbsolutePosition
	DirMenu.Position = UDim2.fromOffset(abs.X, abs.Y + UI.DirBtn.AbsoluteSize.Y + 4)
	setDirMenuVisible(not dirMenuOpen)
end)

UIS.InputBegan:Connect(function(input)
	if dirMenuOpen and input.UserInputType == Enum.UserInputType.MouseButton1 then
		task.wait(); setDirMenuVisible(false)
	end
end)

local ExtrasScroll = Instance.new("ScrollingFrame", Frame)
ExtrasScroll.Size                 = UDim2.fromOffset(FRAME_W - 8, LIST_H)
ExtrasScroll.Position             = UDim2.fromOffset(4, LIST_Y)
ExtrasScroll.BackgroundTransparency = 1
ExtrasScroll.BorderSizePixel      = 0
ExtrasScroll.ScrollBarThickness   = 0
ExtrasScroll.CanvasSize           = UDim2.new(0, 0, 0, 0)
ExtrasScroll.AutomaticCanvasSize  = Enum.AutomaticSize.Y
ExtrasScroll.ClipsDescendants     = true
ExtrasScroll.Visible              = false
local ExLayout = Instance.new("UIListLayout", ExtrasScroll)
ExLayout.Padding             = UDim.new(0, 4)
ExLayout.SortOrder           = Enum.SortOrder.LayoutOrder
ExLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
local ExPad = Instance.new("UIPadding", ExtrasScroll)
ExPad.PaddingTop    = UDim.new(0, 4); ExPad.PaddingBottom = UDim.new(0, 4)
ExPad.PaddingLeft   = UDim.new(0, 4); ExPad.PaddingRight  = UDim.new(0, 4)
UI.ExtrasScroll = ExtrasScroll

local SelectionBox = Instance.new("SelectionBox", workspace)
SelectionBox.Color3        = Color3.fromRGB(0, 255, 100)
SelectionBox.LineThickness = 0.05
UI.SelectionBox = SelectionBox

local BreakerSelBox = Instance.new("SelectionBox", workspace)
BreakerSelBox.Color3        = Color3.fromRGB(255, 60, 30)
BreakerSelBox.LineThickness = 0.07
BreakerSelBox.SurfaceColor3 = Color3.fromRGB(255, 60, 30)
BreakerSelBox.SurfaceTransparency = 0.7
UI.BreakerSelBox = BreakerSelBox

UI.Mouse = LocalPlayer:GetMouse()

return UI
