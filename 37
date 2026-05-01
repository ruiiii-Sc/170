return function(UI)
	local TweenService = UI.TweenService
	local RunService   = UI.RunService
	local Config       = UI.Config
	local ExtrasScroll = UI.ExtrasScroll

	local function makeToggle(parent, label, default, cb)
		local ROW = Instance.new("Frame", parent)
		ROW.Size             = UDim2.new(1, -8, 0, 28)
		ROW.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
		ROW.BorderSizePixel  = 0
		Instance.new("UICorner", ROW).CornerRadius = UDim.new(0, 6)
		local Lbl = Instance.new("TextLabel", ROW)
		Lbl.Size             = UDim2.new(1, -70, 1, 0)
		Lbl.Position         = UDim2.fromOffset(8, 0)
		Lbl.BackgroundTransparency = 1
		Lbl.Text             = label
		Lbl.TextColor3       = Color3.fromRGB(200, 200, 215)
		Lbl.Font             = Enum.Font.FredokaOne
		Lbl.TextSize         = 12
		Lbl.TextXAlignment   = Enum.TextXAlignment.Left
		local state = default or false
		local Track = Instance.new("TextButton", ROW)
		Track.Size             = UDim2.fromOffset(44, 22)
		Track.Position         = UDim2.new(1, -52, 0.5, -11)
		Track.BackgroundColor3 = state and Color3.fromRGB(21, 103, 251) or Color3.fromRGB(55, 55, 62)
		Track.Text             = ""
		Track.AutoButtonColor  = false
		Instance.new("UICorner", Track).CornerRadius = UDim.new(5, 0)
		local Knob = Instance.new("TextButton", Track)
		Knob.Size             = UDim2.fromOffset(18, 18)
		Knob.Position         = state and UDim2.fromOffset(24, 2) or UDim2.fromOffset(2, 2)
		Knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
		Knob.Text             = ""
		Knob.AutoButtonColor  = false
		Instance.new("UICorner", Knob).CornerRadius = UDim.new(5, 0)
		local function toggle()
			state = not state
			Track.BackgroundColor3 = state and Color3.fromRGB(21, 103, 251) or Color3.fromRGB(55, 55, 62)
			Knob:TweenPosition(state and UDim2.fromOffset(24, 2) or UDim2.fromOffset(2, 2), "In", "Sine", 0.1, true)
			if cb then cb(state) end
		end
		Track.MouseButton1Click:Connect(toggle)
		Knob.MouseButton1Click:Connect(toggle)
		if state then task.defer(function() if cb then cb(true) end end) end
		return ROW
	end

	makeToggle(ExtrasScroll, "Instant Break",  Config.InstantBreak,  function(v) UI.stateInstantBreak = v; if v then UI.startInstantBreak() else UI.stopInstantBreak() end end)
	makeToggle(ExtrasScroll, "Mage Animation", Config.MageAnim,      function(v) UI.stateMageAnim = v; if v then UI.startMageAnim() else UI.stopMageAnim() end end)
	makeToggle(ExtrasScroll, "Infinite Zoom",  Config.InfZoom,       function(v) UI.stateInfZoom = v; UI.applyInfZoom() end)
	makeToggle(ExtrasScroll, "Infinite Range", Config.InfRange,      function(v) UI.stateInfRange = v; UI.applyInfRange() end)
	makeToggle(ExtrasScroll, "Inviscam",       Config.Inviscam,      function(v) UI.stateInviscam = v; UI.applyInviscam() end)

	local wsRow = Instance.new("Frame", ExtrasScroll)
	wsRow.Size             = UDim2.new(1, -8, 0, 28)
	wsRow.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
	wsRow.BorderSizePixel  = 0
	Instance.new("UICorner", wsRow).CornerRadius = UDim.new(0, 6)
	local wsLbl = Instance.new("TextLabel", wsRow)
	wsLbl.Size = UDim2.fromOffset(90, 28); wsLbl.Position = UDim2.fromOffset(8, 0)
	wsLbl.BackgroundTransparency = 1; wsLbl.Text = "Walk Speed"
	wsLbl.TextColor3 = Color3.fromRGB(200, 200, 215); wsLbl.Font = Enum.Font.FredokaOne
	wsLbl.TextSize = 12; wsLbl.TextXAlignment = Enum.TextXAlignment.Left
	local wsBox = Instance.new("TextBox", wsRow)
	wsBox.Size = UDim2.fromOffset(46, 20); wsBox.Position = UDim2.new(1, -104, 0.5, -10)
	wsBox.BackgroundColor3 = Color3.fromRGB(35, 35, 45); wsBox.Text = tostring(UI.customWalkSpeed)
	wsBox.TextColor3 = Color3.fromRGB(230, 230, 230); wsBox.Font = Enum.Font.FredokaOne
	wsBox.TextSize = 11; wsBox.ClearTextOnFocus = false
	Instance.new("UICorner", wsBox).CornerRadius = UDim.new(0, 5)
	local wsbs = Instance.new("UIStroke", wsBox); wsbs.Color = Color3.fromRGB(50, 50, 65); wsbs.Thickness = 1
	wsBox.FocusLost:Connect(function()
		UI.customWalkSpeed = tonumber(wsBox.Text) or UI.customWalkSpeed
		wsBox.Text = tostring(UI.customWalkSpeed)
	end)
	local wsState = Config.WalkSpeed
	local wsTrack = Instance.new("TextButton", wsRow)
	wsTrack.Size = UDim2.fromOffset(44, 22); wsTrack.Position = UDim2.new(1, -52, 0.5, -11)
	wsTrack.BackgroundColor3 = wsState and Color3.fromRGB(21, 103, 251) or Color3.fromRGB(55, 55, 62)
	wsTrack.Text = ""; wsTrack.AutoButtonColor = false
	Instance.new("UICorner", wsTrack).CornerRadius = UDim.new(5, 0)
	local wsKnob = Instance.new("TextButton", wsTrack)
	wsKnob.Size = UDim2.fromOffset(18, 18)
	wsKnob.Position = wsState and UDim2.fromOffset(24, 2) or UDim2.fromOffset(2, 2)
	wsKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255); wsKnob.Text = ""; wsKnob.AutoButtonColor = false
	Instance.new("UICorner", wsKnob).CornerRadius = UDim.new(5, 0)
	local function toggleWS()
		wsState = not wsState; UI.stateWalkSpeed = wsState
		wsTrack.BackgroundColor3 = wsState and Color3.fromRGB(21, 103, 251) or Color3.fromRGB(55, 55, 62)
		wsKnob:TweenPosition(wsState and UDim2.fromOffset(24, 2) or UDim2.fromOffset(2, 2), "In", "Sine", 0.1, true)
		if wsState then UI.startWalkSpeed() else UI.stopWalkSpeed() end
	end
	wsTrack.MouseButton1Click:Connect(toggleWS)
	wsKnob.MouseButton1Click:Connect(toggleWS)
	if wsState then task.defer(UI.startWalkSpeed) end

	makeToggle(ExtrasScroll, "Infinite Jump",  Config.InfJump,      function(v) UI.stateInfJump = v; if v then UI.startInfJump() else UI.stopInfJump() end end)
	makeToggle(ExtrasScroll, "Noclip",         Config.Noclip,       function(v) UI.stateNoclip = v; if v then UI.startNoclip() else UI.stopNoclip() end end)
	makeToggle(ExtrasScroll, "Inf Item Limit", Config.InfItemLimit, function(v) UI.stateInfItemLimit = v; if v then UI.applyInfItemLimit() end end)
	makeToggle(ExtrasScroll, "Anti AFK",       Config.AntiAfk,      function(v) UI.stateAntiAfk = v; if v then UI.startAntiAfk() else UI.stopAntiAfk() end end)

	makeToggle(ExtrasScroll, "No FlowingWater", Config.DisableFlowingWater, function(v)
		UI.stateFlowingWater = v
		if v then UI.setupFlowingWaterRemover() else UI.stopFlowingWaterRemover() end
	end)

	makeToggle(ExtrasScroll, "Disable 3D Rendering (AFK)", Config.Disable3DRendering, function(v)
		UI.stateDisable3DRender = v
		UI.apply3DRendering()
	end)

	local flyRow = Instance.new("Frame", ExtrasScroll)
	flyRow.Size             = UDim2.new(1, -8, 0, 28)
	flyRow.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
	flyRow.BorderSizePixel  = 0
	Instance.new("UICorner", flyRow).CornerRadius = UDim.new(0, 6)
	local flyLbl = Instance.new("TextLabel", flyRow)
	flyLbl.Size                 = UDim2.fromOffset(90, 28)
	flyLbl.Position             = UDim2.fromOffset(8, 0)
	flyLbl.BackgroundTransparency = 1
	flyLbl.Text                 = "Fly Speed"
	flyLbl.TextColor3           = Color3.fromRGB(200, 200, 215)
	flyLbl.Font                 = Enum.Font.FredokaOne
	flyLbl.TextSize             = 12
	flyLbl.TextXAlignment       = Enum.TextXAlignment.Left
	local flyStatusLbl = Instance.new("TextLabel", flyRow)
	flyStatusLbl.Size                 = UDim2.fromOffset(34, 20)
	flyStatusLbl.Position             = UDim2.new(1, -104, 0.5, -10)
	flyStatusLbl.BackgroundColor3     = Color3.fromRGB(35, 35, 45)
	flyStatusLbl.Text                 = "OFF"
	flyStatusLbl.TextColor3           = Color3.fromRGB(180, 100, 100)
	flyStatusLbl.Font                 = Enum.Font.FredokaOne
	flyStatusLbl.TextSize             = 10
	flyStatusLbl.BackgroundTransparency = 0
	Instance.new("UICorner", flyStatusLbl).CornerRadius = UDim.new(0, 5)
	local flySpeedBox = Instance.new("TextBox", flyRow)
	flySpeedBox.Size             = UDim2.fromOffset(46, 20)
	flySpeedBox.Position         = UDim2.new(1, -56, 0.5, -10)
	flySpeedBox.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
	flySpeedBox.Text             = tostring(UI.flySpeed)
	flySpeedBox.TextColor3       = Color3.fromRGB(230, 230, 230)
	flySpeedBox.Font             = Enum.Font.FredokaOne
	flySpeedBox.TextSize         = 11
	flySpeedBox.ClearTextOnFocus = false
	Instance.new("UICorner", flySpeedBox).CornerRadius = UDim.new(0, 5)
	local flysbs = Instance.new("UIStroke", flySpeedBox)
	flysbs.Color = Color3.fromRGB(50, 50, 65); flysbs.Thickness = 1
	flySpeedBox.FocusLost:Connect(function()
		local v = tonumber(flySpeedBox.Text)
		if v and v > 0 then UI.flySpeed = v end
		flySpeedBox.Text = tostring(UI.flySpeed)
	end)
	RunService.Heartbeat:Connect(function()
		if UI.flying then
			flyStatusLbl.Text       = "ON"
			flyStatusLbl.TextColor3 = Color3.fromRGB(100, 210, 120)
		else
			flyStatusLbl.Text       = "OFF"
			flyStatusLbl.TextColor3 = Color3.fromRGB(180, 100, 100)
		end
	end)

	local fillSep = Instance.new("Frame", ExtrasScroll)
	fillSep.Size             = UDim2.new(1, -8, 0, 1)
	fillSep.BackgroundColor3 = Color3.fromRGB(50, 50, 65)
	fillSep.BorderSizePixel  = 0

	local fillHeader = Instance.new("TextLabel", ExtrasScroll)
	fillHeader.Size                 = UDim2.new(1, -8, 0, 20)
	fillHeader.BackgroundColor3     = Color3.fromRGB(30, 30, 40)
	fillHeader.Text                 = "FILL FUNCTION"
	fillHeader.TextColor3           = Color3.fromRGB(0, 200, 255)
	fillHeader.Font                 = Enum.Font.FredokaOne
	fillHeader.TextSize             = 12
	fillHeader.BorderSizePixel      = 0
	Instance.new("UICorner", fillHeader).CornerRadius = UDim.new(0, 6)

	local function makeFillCoordRow(labelTxt)
		local row = Instance.new("Frame", ExtrasScroll)
		row.Size             = UDim2.new(1, -8, 0, 28)
		row.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
		row.BorderSizePixel  = 0
		Instance.new("UICorner", row).CornerRadius = UDim.new(0, 6)
		local lbl = Instance.new("TextLabel", row)
		lbl.Size                 = UDim2.fromOffset(38, 28)
		lbl.Position             = UDim2.fromOffset(4, 0)
		lbl.BackgroundTransparency = 1
		lbl.Text                 = labelTxt
		lbl.TextColor3           = Color3.fromRGB(130, 130, 145)
		lbl.Font                 = Enum.Font.FredokaOne
		lbl.TextSize             = 11
		local function mkFB(col)
			local t = Instance.new("TextBox", row)
			t.Size             = UDim2.fromOffset(52, 22)
			t.Position         = UDim2.fromOffset(42 + col * 58, 3)
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
		return row, mkFB(0), mkFB(1), mkFB(2)
	end

	local fillRow1, f1X, f1Y, f1Z = makeFillCoordRow("P1")
	local fillRow2, f2X, f2Y, f2Z = makeFillCoordRow("P2")

	local fillCtrlRow = Instance.new("Frame", ExtrasScroll)
	fillCtrlRow.Size             = UDim2.new(1, -8, 0, 28)
	fillCtrlRow.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
	fillCtrlRow.BorderSizePixel  = 0
	Instance.new("UICorner", fillCtrlRow).CornerRadius = UDim.new(0, 6)

	local previewBtn = Instance.new("TextButton", fillCtrlRow)
	previewBtn.Size             = UDim2.new(0.48, 0, 1, -6)
	previewBtn.Position         = UDim2.fromOffset(4, 3)
	previewBtn.BackgroundColor3 = Color3.fromRGB(38, 80, 120)
	previewBtn.Text             = "Preview"
	previewBtn.TextColor3       = Color3.fromRGB(230, 230, 230)
	previewBtn.Font             = Enum.Font.FredokaOne
	previewBtn.TextSize         = 12
	previewBtn.AutoButtonColor  = false
	Instance.new("UICorner", previewBtn).CornerRadius = UDim.new(0, 6)

	local fillBtn = Instance.new("TextButton", fillCtrlRow)
	fillBtn.Size             = UDim2.new(0.48, 0, 1, -6)
	fillBtn.Position         = UDim2.new(0.52, 0, 0, 3)
	fillBtn.BackgroundColor3 = Color3.fromRGB(45, 115, 55)
	fillBtn.Text             = "FILL"
	fillBtn.TextColor3       = Color3.fromRGB(230, 230, 230)
	fillBtn.Font             = Enum.Font.FredokaOne
	fillBtn.TextSize         = 14
	fillBtn.AutoButtonColor  = false
	Instance.new("UICorner", fillBtn).CornerRadius = UDim.new(0, 6)

	local previewActive = false
	local snap3fill = function(v) return math.round(v / 3) * 3 end

	previewBtn.MouseButton1Click:Connect(function()
		local x1 = tonumber(f1X.Text); local y1 = tonumber(f1Y.Text); local z1 = tonumber(f1Z.Text)
		local x2 = tonumber(f2X.Text); local y2 = tonumber(f2Y.Text); local z2 = tonumber(f2Z.Text)
		if not (x1 and y1 and z1 and x2 and y2 and z2) then return end
		if previewActive then
			UI.clearFillPreview()
			previewActive = false
			previewBtn.BackgroundColor3 = Color3.fromRGB(38, 80, 120)
			previewBtn.Text = "Preview"
		else
			UI.showFillPreview(snap3fill(x1), snap3fill(y1), snap3fill(z1), snap3fill(x2), snap3fill(y2), snap3fill(z2))
			previewActive = true
			previewBtn.BackgroundColor3 = Color3.fromRGB(80, 38, 38)
			previewBtn.Text = "Clear"
		end
	end)

	fillBtn.MouseButton1Click:Connect(function()
		if UI.isFilling then
			UI.stopFill = true
			fillBtn.Text             = "FILL"
			fillBtn.BackgroundColor3 = Color3.fromRGB(45, 115, 55)
			return
		end
		if not UI.selectedItem or not UI.selectedItemRef then return end
		local x1 = tonumber(f1X.Text); local y1 = tonumber(f1Y.Text); local z1 = tonumber(f1Z.Text)
		local x2 = tonumber(f2X.Text); local y2 = tonumber(f2Y.Text); local z2 = tonumber(f2Z.Text)
		if not (x1 and y1 and z1 and x2 and y2 and z2) then return end
		UI.clearFillPreview()
		previewActive = false
		previewBtn.BackgroundColor3 = Color3.fromRGB(38, 80, 120)
		previewBtn.Text = "Preview"
		fillBtn.Text             = "CANCEL"
		fillBtn.BackgroundColor3 = Color3.fromRGB(160, 50, 50)
		task.spawn(function()
			UI.runFill(snap3fill(x1), snap3fill(y1), snap3fill(z1), snap3fill(x2), snap3fill(y2), snap3fill(z2), UI.selectedItem, UI.selectedItemRef, UI.currentFace)
			fillBtn.Text             = "FILL"
			fillBtn.BackgroundColor3 = Color3.fromRGB(45, 115, 55)
		end)
	end)

	local breakerSep = Instance.new("Frame", ExtrasScroll)
	breakerSep.Size             = UDim2.new(1, -8, 0, 1)
	breakerSep.BackgroundColor3 = Color3.fromRGB(50, 50, 65)
	breakerSep.BorderSizePixel  = 0

	local breakerHeader = Instance.new("TextLabel", ExtrasScroll)
	breakerHeader.Size             = UDim2.new(1, -8, 0, 20)
	breakerHeader.BackgroundColor3 = Color3.fromRGB(40, 20, 20)
	breakerHeader.Text             = "BREAKER MODE"
	breakerHeader.TextColor3       = Color3.fromRGB(255, 100, 80)
	breakerHeader.Font             = Enum.Font.FredokaOne
	breakerHeader.TextSize         = 12
	breakerHeader.BorderSizePixel  = 0
	Instance.new("UICorner", breakerHeader).CornerRadius = UDim.new(0, 6)

	local breakerInfoLbl = Instance.new("TextLabel", ExtrasScroll)
	breakerInfoLbl.Size             = UDim2.new(1, -8, 0, 32)
	breakerInfoLbl.BackgroundColor3 = Color3.fromRGB(25, 15, 15)
	breakerInfoLbl.Text             = "Rate: 35 fire/blok • Queue: sequential\nAktifkan mode Breaker di SourcePill"
	breakerInfoLbl.TextColor3       = Color3.fromRGB(180, 150, 150)
	breakerInfoLbl.Font             = Enum.Font.FredokaOne
	breakerInfoLbl.TextSize         = 10
	breakerInfoLbl.TextWrapped      = true
	breakerInfoLbl.BorderSizePixel  = 0
	Instance.new("UICorner", breakerInfoLbl).CornerRadius = UDim.new(0, 6)

	local clearQueueBtn = Instance.new("TextButton", ExtrasScroll)
	clearQueueBtn.Size             = UDim2.new(1, -8, 0, 26)
	clearQueueBtn.BackgroundColor3 = Color3.fromRGB(80, 30, 30)
	clearQueueBtn.Text             = "Clear Breaker Queue"
	clearQueueBtn.TextColor3       = Color3.fromRGB(255, 140, 120)
	clearQueueBtn.Font             = Enum.Font.FredokaOne
	clearQueueBtn.TextSize         = 12
	clearQueueBtn.AutoButtonColor  = false
	Instance.new("UICorner", clearQueueBtn).CornerRadius = UDim.new(0, 6)
	clearQueueBtn.MouseButton1Click:Connect(function()
		UI.clearBreakerQueue()
	end)
end
