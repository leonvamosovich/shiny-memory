local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")

local lp = Players.LocalPlayer
local pg = lp:WaitForChild("PlayerGui")
if pg:FindFirstChild("MM2GreenHub") then
	pg.MM2GreenHub:Destroy()
end

-- Состояние скрипта
local S = {
	Lang = "RU", -- "RU", "EN", "VI"
	Minimized = false,

	-- Player
	SpeedEnabled = false,
	SpeedVal = 16,
	JumpEnabled = false,
	JumpVal = 50,
	InfJump = false,
	Noclip = false,
	Invisible = false,
	GodMode = false,

	-- Fling
	FlingMurder = false,
	FlingSheriff = false,
	FlingNearest = false,

	-- MM2 / Extras
	RoleESP = true,
	GunESP = true,
	AntiAFK = true,
}

-- Словари локализации (RU / EN / VI)
local Langs = {
	RU = {
		Title = "MM2 GREEN HUB",
		MiniLabel = "MM2  [+]",
		TabPlayer = "Плеер",
		TabFling = "Флинг",
		TabMM2 = "MM2",
		TabSet = "Настр.",
		Speed = "Скорость",
		Jump = "Прыжок",
		InfJump = "Беск. прыжок",
		Noclip = "Noclip (сквозь стены)",
		Invisible = "Невидимость",
		GodMode = "God Mode (Локал.)",
		FlingM = "Флинг Убийцы",
		FlingS = "Флинг Шерифа",
		FlingN = "Флинг Ближайшего",
		RoleESP = "ESP Ролей",
		GunESP = "ESP Пистолета",
		TPGun = "ТП к пистолету",
		AntiAFK = "Анти-AFK",
		LangTitle = "Язык / Language / Ngôn ngữ:",
		ON = "ВКЛ",
		OFF = "ВЫКЛ",
	},
	EN = {
		Title = "MM2 GREEN HUB",
		MiniLabel = "MM2  [+]",
		TabPlayer = "Player",
		TabFling = "Fling",
		TabMM2 = "MM2",
		TabSet = "Settings",
		Speed = "Speed",
		Jump = "Jump Power",
		InfJump = "Inf Jump",
		Noclip = "Noclip",
		Invisible = "Invisible",
		GodMode = "God Mode (Local)",
		FlingM = "Fling Murderer",
		FlingS = "Fling Sheriff",
		FlingN = "Fling Nearest",
		RoleESP = "Role ESP",
		GunESP = "Gun ESP",
		TPGun = "TP to Gun",
		AntiAFK = "Anti-AFK",
		LangTitle = "Язык / Language / Ngôn ngữ:",
		ON = "ON",
		OFF = "OFF",
	},
	VI = {
		Title = "MM2 GREEN HUB",
		MiniLabel = "MM2  [+]",
		TabPlayer = "Người chơi",
		TabFling = "Đẩy (Fling)",
		TabMM2 = "MM2",
		TabSet = "Cài đặt",
		Speed = "Tốc độ",
		Jump = "Nhảy cao",
		InfJump = "Nhảy vô hạn",
		Noclip = "Đi xuyên tường",
		Invisible = "Tàng hình",
		GodMode = "Bất tử (Local)",
		FlingM = "Đẩy Sát thủ",
		FlingS = "Đẩy Cảnh sát",
		FlingN = "Đẩy Gần nhất",
		RoleESP = "ESP Vai trò",
		GunESP = "ESP Súng rơi",
		TPGun = "Dịch chuyển tới súng",
		AntiAFK = "Chống AFK",
		LangTitle = "Язык / Language / Ngôn ngữ:",
		ON = "BẬT",
		OFF = "TẮT",
	},
}

local function T(key)
	local dict = Langs[S.Lang] or Langs.RU
	return dict[key] or Langs.RU[key] or key
end

-- Цветовая палитра (зелёная)
local C = {
	Bg = Color3.fromRGB(12, 22, 16),
	Card = Color3.fromRGB(18, 33, 24),
	Header = Color3.fromRGB(16, 38, 25),
	Stroke = Color3.fromRGB(34, 197, 94),
	Accent = Color3.fromRGB(22, 163, 74),
	AccentBright = Color3.fromRGB(34, 197, 94),
	OffBtn = Color3.fromRGB(24, 40, 30),
	Text = Color3.fromRGB(236, 253, 243),
	Muted = Color3.fromRGB(148, 204, 168),
}

local gui = Instance.new("ScreenGui")
gui.Name = "MM2GreenHub"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = pg

-- Компактная свёрнутая плашка (остаётся на экране при уменьшении)
local miniBar = Instance.new("TextButton")
miniBar.Name = "MiniBar"
miniBar.Size = UDim2.new(0, 108, 0, 30)
miniBar.Position = UDim2.new(0.5, -54, 0.12, 0)
miniBar.BackgroundColor3 = C.Header
miniBar.TextColor3 = C.Text
miniBar.Font = Enum.Font.GothamBold
miniBar.TextSize = 12
miniBar.Text = T("MiniLabel")
miniBar.Visible = false
miniBar.BorderSizePixel = 0
miniBar.Parent = gui
Instance.new("UICorner", miniBar).CornerRadius = UDim.new(0, 8)
local miniStroke = Instance.new("UIStroke", miniBar)
miniStroke.Color = C.Stroke
miniStroke.Thickness = 1.2

-- Главное окно
local win = Instance.new("Frame")
win.Name = "MainWindow"
win.Size = UDim2.new(0, 276, 0, 286)
win.Position = UDim2.new(0.5, -138, 0.5, -143)
win.BackgroundColor3 = C.Bg
win.BorderSizePixel = 0
win.Parent = gui
Instance.new("UICorner", win).CornerRadius = UDim.new(0, 11)
local winStroke = Instance.new("UIStroke", win)
winStroke.Color = C.Stroke
winStroke.Thickness = 1.2

local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 34)
header.BackgroundColor3 = C.Header
header.BorderSizePixel = 0
header.Parent = win
Instance.new("UICorner", header).CornerRadius = UDim.new(0, 11)

local titleLbl = Instance.new("TextLabel")
titleLbl.Size = UDim2.new(1, -46, 1, 0)
titleLbl.Position = UDim2.new(0, 10, 0, 0)
titleLbl.BackgroundTransparency = 1
titleLbl.Text = T("Title")
titleLbl.TextColor3 = C.Text
titleLbl.Font = Enum.Font.GothamBold
titleLbl.TextSize = 12
titleLbl.TextXAlignment = Enum.TextXAlignment.Left
titleLbl.Parent = header

local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 26, 0, 24)
minBtn.Position = UDim2.new(1, -32, 0.5, -12)
minBtn.BackgroundColor3 = C.Accent
minBtn.TextColor3 = Color3.new(1, 1, 1)
minBtn.Font = Enum.Font.GothamBold
minBtn.TextSize = 14
minBtn.Text = "—"
minBtn.BorderSizePixel = 0
minBtn.Parent = header
Instance.new("UICorner", minBtn).CornerRadius = UDim.new(0, 6)

-- Перетаскивание (работает для главного окна и свёрнутой плашки)
local function makeDraggable(dragHandle, targetFrame)
	local dragging, dInput, dStart, sPos
	dragHandle.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dStart = input.Position
			sPos = targetFrame.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end)
	dragHandle.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
			dInput = input
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if dragging and input == dInput then
			local delta = input.Position - dStart
			targetFrame.Position = UDim2.new(
				sPos.X.Scale,
				sPos.X.Offset + delta.X,
				sPos.Y.Scale,
				sPos.Y.Offset + delta.Y
			)
		end
	end)
end

makeDraggable(header, win)
makeDraggable(miniBar, miniBar)

local function setMinimized(state)
	S.Minimized = state
	if state then
		miniBar.Position = win.Position
		win.Visible = false
		miniBar.Visible = true
	else
		win.Position = miniBar.Position
		miniBar.Visible = false
		win.Visible = true
	end
end

minBtn.MouseButton1Click:Connect(function()
	setMinimized(true)
end)

miniBar.MouseButton1Click:Connect(function()
	setMinimized(false)
end)

-- Вкладки
local tabBar = Instance.new("Frame")
tabBar.Size = UDim2.new(1, -12, 0, 26)
tabBar.Position = UDim2.new(0, 6, 0, 40)
tabBar.BackgroundTransparency = 1
tabBar.Parent = win

local tabGrid = Instance.new("UIGridLayout", tabBar)
tabGrid.CellSize = UDim2.new(0.235, 0, 1, 0)
tabGrid.CellPadding = UDim2.new(0.02, 0, 0, 0)

local contentArea = Instance.new("Frame")
contentArea.Size = UDim2.new(1, -12, 1, -74)
contentArea.Position = UDim2.new(0, 6, 0, 70)
contentArea.BackgroundTransparency = 1
contentArea.Parent = win

local pages = {}
local tabBtns = {}
local uiUpdaters = {}

local function createPage(id, langKey)
	local sf = Instance.new("ScrollingFrame")
	sf.Name = id
	sf.Size = UDim2.new(1, 0, 1, 0)
	sf.BackgroundTransparency = 1
	sf.ScrollBarThickness = 2
	sf.ScrollBarImageColor3 = C.AccentBright
	sf.Visible = false
	sf.Parent = contentArea

	local ll = Instance.new("UIListLayout", sf)
	ll.Padding = UDim.new(0, 6)
	ll:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
		sf.CanvasSize = UDim2.new(0, 0, 0, ll.AbsoluteContentSize.Y + 6)
	end)

	local tb = Instance.new("TextButton")
	tb.Text = T(langKey)
	tb.BackgroundColor3 = C.OffBtn
	tb.TextColor3 = C.Muted
	tb.Font = Enum.Font.GothamSemibold
	tb.TextSize = 11
	tb.BorderSizePixel = 0
	tb.Parent = tabBar
	Instance.new("UICorner", tb).CornerRadius = UDim.new(0, 6)

	tb.MouseButton1Click:Connect(function()
		for k, p in pairs(pages) do
			local active = (k == id)
			p.Visible = active
			tabBtns[k].BackgroundColor3 = active and C.Accent or C.OffBtn
			tabBtns[k].TextColor3 = active and Color3.new(1, 1, 1) or C.Muted
		end
	end)

	pages[id] = sf
	tabBtns[id] = tb
	table.insert(uiUpdaters, function()
		tb.Text = T(langKey)
	end)
	return sf
end

local pPlayer = createPage("Player", "TabPlayer")
local pFling = createPage("Fling", "TabFling")
local pMM2 = createPage("MM2", "TabMM2")
local pSet = createPage("Settings", "TabSet")

pages.Player.Visible = true
tabBtns.Player.BackgroundColor3 = C.Accent
tabBtns.Player.TextColor3 = Color3.new(1, 1, 1)

local function addToggle(parent, langKey, stateKey, onChange)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1, -4, 0, 28)
	btn.BorderSizePixel = 0
	btn.Font = Enum.Font.GothamSemibold
	btn.TextSize = 11
	btn.TextColor3 = C.Text
	btn.Parent = parent
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
	local st = Instance.new("UIStroke", btn)
	st.Thickness = 1

	local function refresh()
		local active = S[stateKey]
		btn.BackgroundColor3 = active and C.Accent or C.OffBtn
		st.Color = active and C.AccentBright or Color3.fromRGB(36, 74, 50)
		btn.Text = string.format("[%s]  %s", active and T("ON") or T("OFF"), T(langKey))
	end

	refresh()
	table.insert(uiUpdaters, refresh)

	btn.MouseButton1Click:Connect(function()
		S[stateKey] = not S[stateKey]
		refresh()
		if onChange then
			pcall(onChange, S[stateKey])
		end
	end)
end

-- Контрол скорости: 16 по умолчанию, шаг +5 / -5 до 100
local speedCard = Instance.new("Frame")
speedCard.Size = UDim2.new(1, -4, 0, 56)
speedCard.BackgroundColor3 = C.Card
speedCard.BorderSizePixel = 0
speedCard.Parent = pPlayer
Instance.new("UICorner", speedCard).CornerRadius = UDim.new(0, 7)
local scStroke = Instance.new("UIStroke", speedCard)
scStroke.Color = Color3.fromRGB(36, 74, 50)
scStroke.Thickness = 1

local speedToggle = Instance.new("TextButton")
speedToggle.Size = UDim2.new(1, -10, 0, 24)
speedToggle.Position = UDim2.new(0, 5, 0, 4)
speedToggle.Font = Enum.Font.GothamBold
speedToggle.TextSize = 11
speedToggle.TextColor3 = Color3.new(1, 1, 1)
speedToggle.BorderSizePixel = 0
speedToggle.Parent = speedCard
Instance.new("UICorner", speedToggle).CornerRadius = UDim.new(0, 5)

local function refreshSpeedUI()
	speedToggle.BackgroundColor3 = S.SpeedEnabled and C.Accent or C.OffBtn
	speedToggle.Text = string.format("%s: %d / 100  (%s)", T("Speed"), S.SpeedVal, S.SpeedEnabled and T("ON") or T("OFF"))
end
refreshSpeedUI()
table.insert(uiUpdaters, refreshSpeedUI)

speedToggle.MouseButton1Click:Connect(function()
	S.SpeedEnabled = not S.SpeedEnabled
	refreshSpeedUI()
end)

local function makeStepBtn(txt, xScale, wScale, deltaOrReset)
	local b = Instance.new("TextButton")
	b.Size = UDim2.new(wScale, -4, 0, 20)
	b.Position = UDim2.new(xScale, 2, 0, 32)
	b.BackgroundColor3 = C.OffBtn
	b.TextColor3 = C.Text
	b.Font = Enum.Font.GothamBold
	b.TextSize = 11
	b.Text = txt
	b.BorderSizePixel = 0
	b.Parent = speedCard
	Instance.new("UICorner", b).CornerRadius = UDim.new(0, 5)

	b.MouseButton1Click:Connect(function()
		if deltaOrReset == "reset" then
			S.SpeedVal = 16
		else
			S.SpeedVal = math.clamp(S.SpeedVal + deltaOrReset, 16, 100)
		end
		refreshSpeedUI()
	end)
end

makeStepBtn("-5", 0, 0.3, -5)
makeStepBtn("+5", 0.3, 0.4, 5)
makeStepBtn("16", 0.7, 0.3, "reset")

-- Остальные функции Player
addToggle(pPlayer, "Jump", "JumpEnabled")
addToggle(pPlayer, "InfJump", "InfJump")
addToggle(pPlayer, "Noclip", "Noclip")
addToggle(pPlayer, "Invisible", "Invisible", function(v)
	local ch = lp.Character
	if not ch then return end
	for _, obj in ipairs(ch:GetDescendants()) do
		if obj:IsA("BasePart") and obj.Name ~= "HumanoidRootPart" then
			obj.LocalTransparencyModifier = v and 1 or 0
			obj.Transparency = v and 1 or 0
		elseif obj:IsA("Decal") then
			obj.Transparency = v and 1 or 0
		end
	end
end)
addToggle(pPlayer, "GodMode", "GodMode")

-- Функции Fling
addToggle(pFling, "FlingM", "FlingMurder")
addToggle(pFling, "FlingS", "FlingSheriff")
addToggle(pFling, "FlingN", "FlingNearest")

-- Функции MM2
addToggle(pMM2, "RoleESP", "RoleESP")
addToggle(pMM2, "GunESP", "GunESP")

local tpBtn = Instance.new("TextButton")
tpBtn.Size = UDim2.new(1, -4, 0, 28)
tpBtn.BackgroundColor3 = C.Accent
tpBtn.TextColor3 = Color3.new(1, 1, 1)
tpBtn.Font = Enum.Font.GothamBold
tpBtn.TextSize = 11
tpBtn.Text = T("TPGun")
tpBtn.BorderSizePixel = 0
tpBtn.Parent = pMM2
Instance.new("UICorner", tpBtn).CornerRadius = UDim.new(0, 6)
table.insert(uiUpdaters, function()
	tpBtn.Text = T("TPGun")
end)

tpBtn.MouseButton1Click:Connect(function()
	local gunDrop = workspace:FindFirstChild("GunDrop")
	local root = lp.Character and lp.Character:FindFirstChild("HumanoidRootPart")
	if gunDrop and root then
		local part = gunDrop:IsA("BasePart") and gunDrop or gunDrop:FindFirstChildWhichIsA("BasePart", true)
		if part then
			root.CFrame = part.CFrame + Vector3.new(0, 3, 0)
		end
	end
end)

addToggle(pMM2, "AntiAFK", "AntiAFK")

-- Настройки языка (Вьетнамский / Русский / Английский)
local langLbl = Instance.new("TextLabel")
langLbl.Size = UDim2.new(1, -4, 0, 22)
langLbl.BackgroundTransparency = 1
langLbl.Text = T("LangTitle")
langLbl.TextColor3 = C.Muted
langLbl.Font = Enum.Font.GothamSemibold
langLbl.TextSize = 11
langLbl.TextXAlignment = Enum.TextXAlignment.Left
langLbl.Parent = pSet

local function applyLanguage(code)
	S.Lang = code
	titleLbl.Text = T("Title")
	miniBar.Text = T("MiniLabel")
	langLbl.Text = T("LangTitle")
	for _, fn in ipairs(uiUpdaters) do
		pcall(fn)
	end
end

local function addLangButton(code, caption)
	local b = Instance.new("TextButton")
	b.Size = UDim2.new(1, -4, 0, 28)
	b.BackgroundColor3 = C.Card
	b.TextColor3 = C.Text
	b.Font = Enum.Font.GothamBold
	b.TextSize = 11
	b.Text = caption
	b.BorderSizePixel = 0
	b.Parent = pSet
	Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
	local s = Instance.new("UIStroke", b)
	s.Color = C.Stroke
	s.Thickness = 1

	b.MouseButton1Click:Connect(function()
		applyLanguage(code)
	end)
end

addLangButton("VI", "Tiếng Việt (Вьетнамский)")
addLangButton("RU", "Русский")
addLangButton("EN", "English (Английский)")

-- Вспомогательная логика MM2 и Fling
local function getRole(plr)
	local function hasKeyword(container, list)
		if not container then return false end
		for _, item in ipairs(container:GetChildren()) do
			if item:IsA("Tool") then
				local low = string.lower(item.Name)
				for _, w in ipairs(list) do
					if string.find(low, w, 1, true) then
						return true
					end
				end
			end
		end
		return false
	end
	local c = plr.Character
	local b = plr:FindFirstChild("Backpack")
	if hasKeyword(c, {"knife"}) or hasKeyword(b, {"knife"}) then
		return "Murderer", Color3.fromRGB(239, 68, 68)
	elseif hasKeyword(c, {"gun", "revolver"}) or hasKeyword(b, {"gun", "revolver"}) then
		return "Sheriff", Color3.fromRGB(59, 130, 246)
	end
	return "Innocent", Color3.fromRGB(34, 197, 94)
end

local function pickFlingTarget()
	local myRoot = lp.Character and lp.Character:FindFirstChild("HumanoidRootPart")
	if not myRoot then return nil end

	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= lp and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
			local role = getRole(p)
			if S.FlingMurder and role == "Murderer" then
				return p.Character.HumanoidRootPart
			end
			if S.FlingSheriff and role == "Sheriff" then
				return p.Character.HumanoidRootPart
			end
		end
	end

	if S.FlingNearest then
		local bestPart, bestDist = nil, math.huge
		for _, p in ipairs(Players:GetPlayers()) do
			if p ~= lp and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
				local hum = p.Character:FindFirstChildOfClass("Humanoid")
				if not hum or hum.Health > 0 then
					local d = (p.Character.HumanoidRootPart.Position - myRoot.Position).Magnitude
					if d < bestDist then
						bestDist = d
						bestPart = p.Character.HumanoidRootPart
					end
				end
			end
		end
		return bestPart
	end
	return nil
end

local function updateESP()
	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= lp and p.Character then
			local hl = p.Character:FindFirstChild("MM2GreenHL")
			if S.RoleESP then
				hl = hl or Instance.new("Highlight")
				hl.Name = "MM2GreenHL"
				hl.FillTransparency = 0.55
				hl.OutlineTransparency = 0
				local _, col = getRole(p)
				hl.FillColor, hl.OutlineColor = col, col
				hl.Parent = p.Character
			elseif hl then
				hl:Destroy()
			end
		end
	end

	local gd = workspace:FindFirstChild("GunDrop")
	if gd then
		local gh = gd:FindFirstChild("MM2GreenGunHL")
		if S.GunESP then
			gh = gh or Instance.new("Highlight")
			gh.Name = "MM2GreenGunHL"
			gh.FillColor = Color3.fromRGB(74, 222, 128)
			gh.OutlineColor = Color3.new(1, 1, 1)
			gh.FillTransparency = 0.35
			gh.Parent = gd
		elseif gh then
			gh:Destroy()
		end
	end
end

UserInputService.JumpRequest:Connect(function()
	if S.InfJump then
		local hum = lp.Character and lp.Character:FindFirstChildOfClass("Humanoid")
		if hum then
			hum:ChangeState(Enum.HumanoidStateType.Jumping)
		end
	end
end)

lp.Idled:Connect(function()
	if S.AntiAFK then
		VirtualUser:CaptureController()
		VirtualUser:ClickButton2(Vector2.new())
	end
end)

local espTimer = 0
RunService.Stepped:Connect(function(_, dt)
	pcall(function()
		local ch = lp.Character
		local hum = ch and ch:FindFirstChildOfClass("Humanoid")
		local root = ch and ch:FindFirstChild("HumanoidRootPart")

		if hum and hum.Health > 0 then
			if S.SpeedEnabled then
				hum.WalkSpeed = S.SpeedVal
			end
			if S.JumpEnabled then
				hum.UseJumpPower = true
				hum.JumpPower = S.JumpVal
			end
			if S.GodMode then
				hum.MaxHealth = 100
				hum.Health = 100
			end
		end

		if S.Noclip and ch then
			for _, part in ipairs(ch:GetDescendants()) do
				if part:IsA("BasePart") then
					part.CanCollide = false
				end
			end
		end

		local targetRoot = pickFlingTarget()
		if targetRoot and root then
			root.CFrame = targetRoot.CFrame * CFrame.Angles(0, tick() * 35, 0)
			root.AssemblyAngularVelocity = Vector3.new(0, 9999, 0)
			root.AssemblyLinearVelocity = Vector3.new(0, 120, 0)
		end

		espTimer += dt
		if espTimer >= 0.4 then
			espTimer = 0
			updateESP()
		end
	end)
end)
