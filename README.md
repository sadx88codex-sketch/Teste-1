# Teste-1
Isso é do um teste, não use

local PLAYER_IMG_ASSET_ID = "rbxassetid://PUT_YOUR_ASSET_ID_HERE"

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Helper: create UI elements quickly
local function new(class, props)
	local obj = Instance.new(class)
	for k, v in pairs(props or {}) do
		obj[k] = v
	end
	return obj
end

-- Root ScreenGui
local screenGui = new("ScreenGui", {
	Name = "SafeExecutorMockUI",
	Parent = playerGui,
	DisplayOrder = 10,
	ResetOnSpawn = false,
})

-- Main container (centered)
local mainFrame = new("Frame", {
	Name = "MainFrame",
	Parent = screenGui,
	AnchorPoint = Vector2.new(0.5, 0.5),
	Position = UDim2.new(0.5, 0, 0.5, 0),
	Size = UDim2.new(0.5, 0, 0.6, 0),
	BackgroundColor3 = Color3.fromRGB(11, 11, 13),
	BorderSizePixel = 0,
	ClipsDescendants = true,
})

-- Rounded UI (using UIStroke and Corner)
local uiCorner = new("UICorner", {Parent = mainFrame, CornerRadius = UDim.new(0, 18)})
local uiStroke = new("UIStroke", {Parent = mainFrame, Thickness = 1, Color = Color3.fromRGB(40,40,40), Transparency = 0.5})

-- Left and right panels
local leftPanel = new("Frame", {
	Name = "LeftPanel",
	Parent = mainFrame,
	Size = UDim2.new(0.5, 0, 1, 0),
	BackgroundTransparency = 1,
})
local rightPanel = leftPanel:Clone()
rightPanel.Name = "RightPanel"
rightPanel.Parent = mainFrame
rightPanel.Position = UDim2.new(0.5, 0, 0, 0)

-- Top bar (close button + title)
local topBar = new("Frame", {
	Parent = leftPanel,
	Size = UDim2.new(1, 0, 0, 44),
	BackgroundTransparency = 1,
})
local title = new("TextLabel", {
	Parent = topBar,
	Position = UDim2.new(0, 12, 0, 8),
	Size = UDim2.new(0.6, 0, 1, -8),
	Text = "Delta — Mock UI",
	Font = Enum.Font.SourceSansSemibold,
	TextSize = 18,
	TextColor3 = Color3.fromRGB(220,220,220),
	BackgroundTransparency = 1,
	TextXAlignment = Enum.TextXAlignment.Left,
})
local closeBtn = new("TextButton", {
	Parent = topBar,
	AnchorPoint = Vector2.new(1, 0),
	Position = UDim2.new(1, -12, 0, 6),
	Size = UDim2.new(0, 32, 0, 32),
	Text = "X",
	Font = Enum.Font.SourceSansBold,
	TextSize = 18,
	TextColor3 = Color3.fromRGB(220,220,220),
	BackgroundColor3 = Color3.fromRGB(20,20,20),
	BorderSizePixel = 0,
	AutoButtonColor = true,
})
new("UICorner", {Parent = closeBtn, CornerRadius = UDim.new(0, 8)})

-- Left bottom controls (buttons)
local controls = new("Frame", {
	Parent = leftPanel,
	Position = UDim2.new(0, 12, 0, 56),
	Size = UDim2.new(1, -24, 0, 96),
	BackgroundTransparency = 1,
})
local rotateBtn = new("TextButton", {
	Parent = controls,
	Position = UDim2.new(0, 0, 0, 0),
	Size = UDim2.new(0, 0, 0, 36),
	SizeConstraint = Enum.SizeConstraint.RelativeYY,
	AutomaticSize = Enum.AutomaticSize.None,
	Text = "Girar Img",
	Font = Enum.Font.SourceSans,
	TextSize = 14,
	TextColor3 = Color3.fromRGB(220,220,220),
	BackgroundColor3 = Color3.fromRGB(23,23,23),
	BorderSizePixel = 0,
})
new("UICorner", {Parent = rotateBtn, CornerRadius = UDim.new(0,8)})

local aimTrainingBtn = rotateBtn:Clone()
aimTrainingBtn.Parent = controls
aimTrainingBtn.Position = UDim2.new(0, 110, 0, 0)
aimTrainingBtn.Text = "Treino OFF"

-- Left editor area (mock)
local editor = new("Frame", {
	Parent = leftPanel,
	Position = UDim2.new(0, 12, 0, 164),
	Size = UDim2.new(1, -24, 0.6, -164),
	BackgroundColor3 = Color3.fromRGB(6,6,6),
	BorderSizePixel = 0,
})
new("UICorner", {Parent = editor, CornerRadius = UDim.new(0,8)})
local editorText = new("TextBox", {
	Parent = editor,
	Size = UDim2.new(1, -16, 1, -16),
	Position = UDim2.new(0, 8, 0, 8),
	BackgroundTransparency = 1,
	Text = "// Demo editor (não executa)",
	Font = Enum.Font.Code,
	TextSize = 14,
	TextColor3 = Color3.fromRGB(180,180,180),
	ClearTextOnFocus = false,
	MultiLine = true,
})
editorText.TextXAlignment = Enum.TextXAlignment.Left
editorText.TextYAlignment = Enum.TextYAlignment.Top

-- Run demo / Clear
local runBtn = new("TextButton", {
	Parent = leftPanel,
	Position = UDim2.new(0, 12, 1, -54),
	Size = UDim2.new(0, 120, 0, 40),
	Text = "Run (Demo)",
	Font = Enum.Font.SourceSansSemibold,
	TextSize = 14,
	TextColor3 = Color3.fromRGB(10,10,10),
	BackgroundColor3 = Color3.fromRGB(252, 186, 3),
	BorderSizePixel = 0,
})
new("UICorner", {Parent = runBtn, CornerRadius = UDim.new(0,8)})

local clearBtn = runBtn:Clone()
clearBtn.Parent = leftPanel
clearBtn.Position = UDim2.new(0, 148, 1, -54)
clearBtn.Text = "Clear"
clearBtn.BackgroundColor3 = Color3.fromRGB(20,20,20)
clearBtn.TextColor3 = Color3.fromRGB(220,220,220)
new("UICorner", {Parent = clearBtn, CornerRadius = UDim.new(0,8)})

-- Right panel: image with yellow circle
local imageContainer = new("Frame", {
	Parent = rightPanel,
	AnchorPoint = Vector2.new(0.5, 0),
	Position = UDim2.new(0.5, 0, 0.06, 0),
	Size = UDim2.new(0, 220, 0, 220),
	BackgroundTransparency = 1,
})
local yellowCircle = new("ImageLabel", {
	Parent = imageContainer,
	Size = UDim2.new(1, 8, 1, 8),
	Position = UDim2.new(0, -4, 0, -4),
	Image = "",
	BackgroundTransparency = 1,
})
local circleBorder = new("Frame", {
	Parent = imageContainer,
	Size = UDim2.new(1, 8, 1, 8),
	Position = UDim2.new(0, -4, 0, -4),
	BackgroundTransparency = 1,
})
local borderStroke = new("UIStroke", {Parent = circleBorder, Thickness = 6, Color = Color3.fromRGB(249, 202, 36), Transparency = 0})
local playerImg = new("ImageLabel", {
	Parent = imageContainer,
	Size = UDim2.new(0.85, 0, 0.85, 0),
	Position = UDim2.new(0.075, 0, 0.075, 0),
	Image = PLAYER_IMG_ASSET_ID,
	ScaleType = Enum.ScaleType.Crop,
	BackgroundTransparency = 1,
})
new("UICorner", {Parent = playerImg, CornerRadius = UDim.new(1,0)})

-- Small status panel and output mock
local statusPanel = new("Frame", {
	Parent = rightPanel,
	Position = UDim2.new(0.5, -180, 0.55, 0),
	Size = UDim2.new(0.9, 0, 0.28, 0),
	BackgroundColor3 = Color3.fromRGB(8,8,9),
	BorderSizePixel = 0,
})
new("UICorner", {Parent = statusPanel, CornerRadius = UDim.new(0,8)})
local statusText = new("TextLabel", {
	Parent = statusPanel,
	Position = UDim2.new(0, 8, 0, 8),
	Size = UDim2.new(1, -16, 0, 18),
	Text = "State: IDLE",
	Font = Enum.Font.SourceSans,
	TextSize = 14,
	TextColor3 = Color3.fromRGB(220,220,220),
	BackgroundTransparency = 1,
	TextXAlignment = Enum.TextXAlignment.Left,
})
local outputBox = new("TextLabel", {
	Parent = statusPanel,
	Position = UDim2.new(0, 8, 0, 38),
	Size = UDim2.new(1, -16, 1, -46),
	Text = "Output de demonstração aparecerá aqui.",
	Font = Enum.Font.Code,
	TextSize = 12,
	TextColor3 = Color3.fromRGB(170,170,170),
	BackgroundTransparency = 1,
	TextWrapped = true,
	TextYAlignment = Enum.TextYAlignment.Top,
})

-- Aim Training: target and crosshair (UI-only)
local aimContainer = new("Frame", {
	Parent = imageContainer,
	Size = UDim2.new(1, 0, 1, 0),
	BackgroundTransparency = 1,
})
local target = new("Frame", {
	Parent = aimContainer,
	Size = UDim2.new(0, 20, 0, 20),
	Position = UDim2.new(0.5, -10, 0.5, -10),
	BackgroundColor3 = Color3.fromRGB(40,40,40),
	BorderColor3 = Color3.fromRGB(255,255,255),
	BorderSizePixel = 2,
})
new("UICorner", {Parent = target, CornerRadius = UDim.new(1,0)})
target.Visible = false

local crosshair = new("Frame", {
	Parent = aimContainer,
	Size = UDim2.new(0, 18, 0, 18),
	Position = UDim2.new(0.5, -9, 0.5, -9),
	BackgroundTransparency = 1,
	BorderSizePixel = 0,
})
local crossInner = new("Frame", {
	Parent = crosshair,
	Size = UDim2.new(1, 0, 1, 0),
	BackgroundTransparency = 1,
	BorderSizePixel = 2,
	BorderColor3 = Color3.fromRGB(255,255,255),
})
new("UICorner", {Parent = crossInner, CornerRadius = UDim.new(1,0)})
crosshair.Visible = false

-- State variables
local rotating = false
local aimTraining = false
local runningTargetTask = false

-- Close button
closeBtn.MouseButton1Click:Connect(function()
	screenGui:Destroy()
end)

-- Rotate animation (simple tween rotate using ImageLabel Rotation)
local rotTweenInfo = TweenInfo.new(4, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, -1)
local function toggleRotate()
	rotating = not rotating
	if rotating then
		-- Continuous rotation via loop
		spawn(function()
			while rotating and playerImg and playerImg.Parent do
				local current = playerImg.Rotation or 0
				local tween = TweenService:Create(playerImg, TweenInfo.new(2, Enum.EasingStyle.Linear), {Rotation = current + 180})
				tween:Play()
				tween.Completed:Wait()
			end
		end)
		rotateBtn.Text = "Parar Img"
	else
		rotateBtn.Text = "Girar Img"
	end
end
rotateBtn.MouseButton1Click:Connect(toggleRotate)

-- Aim training: move target randomly inside container
local function startAimTraining()
	if runningTargetTask then return end
	runningTargetTask = true
	target.Visible = true
	crosshair.Visible = true
	statusText.Text = "State: TREINO"
	outputBox.Text = "Modo de Treino ativado (visual apenas)."
	while aimTraining and runningTargetTask do
		local parentSize = aimContainer.AbsoluteSize
		-- keep some padding
		local pad = 20
		local x = math.random(pad, math.max(1, parentSize.X - pad))
		local y = math.random(pad, math.max(1, parentSize.Y - pad))
		-- convert to scale
		local xScale = x / parentSize.X
		local yScale = y / parentSize.Y
		local tween = TweenService:Create(target, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {Position = UDim2.new(xScale, -10, yScale, -10)})
		tween:Play()
		-- smoothly move crosshair toward the target (visual follow)
		local followTween = TweenService:Create(crosshair, TweenInfo.new(0.45, Enum.EasingStyle.Linear), {Position = UDim2.new(xScale, -9, yScale, -9)})
		followTween:Play()
		wait(0.9)
	end
	target.Visible = false
	crosshair.Visible = false
	statusText.Text = "State: IDLE"
	outputBox.Text = "Modo de Treino desativado."
	runningTargetTask = false
end

aimTrainingBtn.MouseButton1Click:Connect(function()
	aimTraining = not aimTraining
	if aimTraining then
		aimTrainingBtn.Text = "Treino ON"
		spawn(startAimTraining)
	else
		aimTrainingBtn.Text = "Treino OFF"
		-- stopping loop handled by flag; startAimTraining will return
	end
end)

-- Run (Demo) and Clear (UI only)
runBtn.MouseButton1Click:Connect(function()
	local time = os.date("%X")
	outputBox.Text = "[Demo Run @ " .. time .. "] Execução simulada concluída.\nNenhum código foi executado."
end)

clearBtn.MouseButton1Click:Connect(function()
	editorText.Text = ""
	outputBox.Text = "Output de demonstração apagado."
end)

-- Initial small animation (fade in)
mainFrame.Position = UDim2.new(0.5, 0, 1.2, 0)
TweenService:Create(mainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quad), {Position = UDim2.new(0.5, 0, 0.5, 0)}):Play()

-- End of script
