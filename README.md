--[[ 
Script UI - Strongest Battlegrounds
Interface: Preto e Branco + Transparência
Funções: Aim Lock, Troca de Roupa, Resolução custom
Autor: CODEX
]]--

-- Serviços
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- GUI
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "AimLockUI"

-- BOTÃO PRINCIPAL (com imagem girando + círculo)
local MainButton = Instance.new("ImageButton", ScreenGui)
MainButton.Size = UDim2.new(0,100,0,100)
MainButton.Position = UDim2.new(0.05,0,0.75,0)
MainButton.Image = "rbxassetid://121642098900456" -- Coloque o ID da imagem
MainButton.BackgroundTransparency = 1

-- CÍRCULO AMARELO
local UICorner = Instance.new("UICorner", MainButton)
UICorner.CornerRadius = UDim.new(1,0)

local UIStroke = Instance.new("UIStroke", MainButton)
UIStroke.Thickness = 2
UIStroke.Color = Color3.fromRGB(255, 221, 0)

-- ANIMAÇÃO GIRANDO
task.spawn(function()
    while task.wait() do
        MainButton.Rotation = (MainButton.Rotation + 1) % 360
    end
end)

-- FRAME PRINCIPAL (rolável)
local Frame = Instance.new("ScrollingFrame", ScreenGui)
Frame.Size = UDim2.new(0,270,0,320)
Frame.Position = UDim2.new(0.5,-135,0.5,-160)
Frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
Frame.Visible = false
Frame.BackgroundTransparency = 0.2
Frame.CanvasSize = UDim2.new(0,0,2,0) -- permite rolagem
Frame.ScrollBarThickness = 6

-- Arredondar
local FrameCorner = Instance.new("UICorner", Frame)
FrameCorner.CornerRadius = UDim.new(0,12)

-- BOTÃO DE FECHAR (X)
local CloseButton = Instance.new("TextButton", Frame)
CloseButton.Size = UDim2.new(0,30,0,30)
CloseButton.Position = UDim2.new(1,-35,0,5)
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.new(1,1,1)
CloseButton.BackgroundTransparency = 0.5
CloseButton.BackgroundColor3 = Color3.new(0,0,0)

-- BOTÃO AIMLOCK
local AimButton = Instance.new("TextButton", Frame)
AimButton.Size = UDim2.new(0.8,0,0.15,0)
AimButton.Position = UDim2.new(0.1,0,0.15,0)
AimButton.Text = "Ativar AimLock"
AimButton.TextColor3 = Color3.new(1,1,1)
AimButton.BackgroundColor3 = Color3.fromRGB(0,0,0)
AimButton.BackgroundTransparency = 0.3

-- BOTÕES DE ROUPAS
local Clothes = {
    {Name="Roupa 1", ID=129246265714731},
    {Name="Roupa 2", ID=154587541784272},
    {Name="Roupa 3", ID=323476364},
}

for i,cl in ipairs(Clothes) do
    local b = Instance.new("TextButton", Frame)
    b.Size = UDim2.new(0.8,0,0.12,0)
    b.Position = UDim2.new(0.1,0,0.35+(i*0.15),0)
    b.Text = cl.Name
    b.TextColor3 = Color3.new(1,1,1)
    b.BackgroundColor3 = Color3.new(0,0,0)
    b.BackgroundTransparency = 0.3
    b.MouseButton1Click:Connect(function()
        pcall(function()
            LocalPlayer.Character.Humanoid:RemoveAccessories()
            local shirt = Instance.new("Shirt", LocalPlayer.Character)
            shirt.ShirtTemplate = "rbxassetid://"..cl.ID
        end)
    end)
end

-- BOTÃO RESOLUÇÃO
local ResButton = Instance.new("TextButton", Frame)
ResButton.Size = UDim2.new(0.8,0,0.15,0)
ResButton.Position = UDim2.new(0.1,0,0.85,0)
ResButton.Text = "Resolução: OFF"
ResButton.TextColor3 = Color3.new(1,1,1)
ResButton.BackgroundColor3 = Color3.new(0,0,0)
ResButton.BackgroundTransparency = 0.3

local ResLock = false
ResButton.MouseButton1Click:Connect(function()
    ResLock = not ResLock
    if ResLock then
        ResButton.Text = "Resolução: 1280x978"
        workspace.CurrentCamera.ViewportSize = Vector2.new(1280,978)
    else
        ResButton.Text = "Resolução: OFF"
        -- volta para resolução padrão
        workspace.CurrentCamera.ViewportSize = workspace.CurrentCamera.ViewportSize
    end
end)

-- VARIÁVEIS AIMLOCK
local AimLockEnabled = false
local Target

-- Função de encontrar alvo
local function GetClosestPlayer()
    local closest, dist = nil, math.huge
    for _,player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
            local pos = workspace.CurrentCamera:WorldToViewportPoint(player.Character.Head.Position)
            local mag = (Vector2.new(pos.X,pos.Y) - Vector2.new(Mouse.X,Mouse.Y)).magnitude
            if mag < dist then
                closest = player
                dist = mag
            end
        end
    end
    return closest
end

-- Loop AimLock
RunService.RenderStepped:Connect(function()
    if AimLockEnabled and Target and Target.Character and Target.Character:FindFirstChild("Head") then
        workspace.CurrentCamera.CFrame = CFrame.new(
            workspace.CurrentCamera.CFrame.Position,
            Target.Character.Head.Position
        )
    end
end)

-- Botão AimLock
AimButton.MouseButton1Click:Connect(function()
    AimLockEnabled = not AimLockEnabled
    if AimLockEnabled then
        AimButton.Text = "AimLock: ON"
        Target = GetClosestPlayer()
    else
        AimButton.Text = "AimLock: OFF"
        Target = nil
    end
end)

-- Botão fechar
CloseButton.MouseButton1Click:Connect(function()
    Frame.Visible = false
end)

-- Abrir interface
MainButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)

-- ====================
-- SISTEMA DE MOVER (Drag)
-- ====================
local function MakeDraggable(gui)
    local dragging, dragInput, dragStart, startPos

    gui.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = gui.Position

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    gui.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    UIS.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            gui.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- Aplicar draggable no Frame e no Botão
MakeDraggable(Frame)
MakeDraggable(MainButton)-- Serviço de áudio
local TweenService = game:GetService("TweenService")

-- Função para criar som de clique em qualquer botão
local function AddClickSound(btn)
    local sound = Instance.new("Sound", btn)
    sound.SoundId = "rbxassetid://12222005" -- som clássico de click/pop
    sound.Volume = 1

    btn.MouseButton1Click:Connect(function()
        sound:Play()
    end)
end

-- BOTÃO AIMLOCK
local AimButton = Instance.new("TextButton", Frame)
AimButton.Size = UDim2.new(0.8,0,0.15,0)
AimButton.Position = UDim2.new(0.1,0,0.15,0)
AimButton.Text = "Ativar AimLock"
AimButton.TextColor3 = Color3.new(1,1,1)
AimButton.BackgroundColor3 = Color3.fromRGB(0,0,0)
AimButton.BackgroundTransparency = 0.3
AddClickSound(AimButton) -- som adicionado

-- BOTÕES DE ROUPAS
local Clothes = {
    {Name="Roupa 1", ID=129246265714731},
    {Name="Roupa 2", ID=154587541784272},
    {Name="Roupa 3", ID=323476364},
}

for i,cl in ipairs(Clothes) do
    local b = Instance.new("ImageButton", ClothesFrame)
    b.Size = UDim2.new(0,120,0,150)
    b.Position = UDim2.new(0,(i-1)*130,0,0)
    b.Image = "rbxassetid://"..cl.ID
    b.BackgroundColor3 = Color3.fromRGB(0,0,0)
    b.BackgroundTransparency = 0.3
    AddClickSound(b) -- som adicionado

    local lbl = Instance.new("TextLabel", b)
    lbl.Size = UDim2.new(1,0,0.25,0)
    lbl.Position = UDim2.new(0,0,1,0)
    lbl.Text = cl.Name
    lbl.TextColor3 = Color3.new(1,1,1)
    lbl.TextScaled = true
    lbl.BackgroundTransparency = 1

    b.MouseButton1Click:Connect(function()
        pcall(function()
            for _,v in pairs(LocalPlayer.Character:GetChildren()) do
                if v:IsA("Shirt") or v:IsA("Pants") or v:IsA("Accessory") then
                    v:Destroy()
                end
            end
            local shirt = Instance.new("Shirt", LocalPlayer.Character)
            shirt.ShirtTemplate = "rbxassetid://"..cl.ID
        end)
    end)
end

-- BOTÃO RESOLUÇÃO
local ResButton = Instance.new("TextButton", Frame)
ResButton.Size = UDim2.new(0.8,0,0.15,0)
ResButton.Position = UDim2.new(0.1,0,0.85,0)
ResButton.Text = "Resolução: OFF"
ResButton.TextColor3 = Color3.new(1,1,1)
ResButton.BackgroundColor3 = Color3.new(0,0,0)
ResButton.BackgroundTransparency = 0.3
AddClickSound(ResButton) -- som adicionado

-- BOTÃO FECHAR (X)
local CloseButton = Instance.new("TextButton", Frame)
CloseButton.Size = UDim2.new(0,30,0,30)
CloseButton.Position = UDim2.new(1,-35,0,5)
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.new(1,1,1)
CloseButton.BackgroundTransparency = 0.5
CloseButton.BackgroundColor3 = Color3.new(0,0,0)
AddClickSound(CloseButton) -- som adicionado

-- BOTÃO RESETAR (já tinha som, mas agora com função universal)
local ResetBtn = Instance.new("ImageButton", ClothesFrame)
ResetBtn.Size = UDim2.new(0,120,0,120)
ResetBtn.Position = UDim2.new(0,#Clothes*130,0,15)
ResetBtn.BackgroundColor3 = Color3.fromRGB(180,0,0)
ResetBtn.BackgroundTransparency = 0.2
ResetBtn.Image = "rbxassetid://6031094678"
ResetBtn.ImageColor3 = Color3.new(1,1,1)
AddClickSound(ResetBtn) -- som adicionado

local UICorner = Instance.new("UICorner", ResetBtn)
UICorner.CornerRadius = UDim.new(1,0)

local lbl = Instance.new("TextLabel", ClothesFrame)
lbl.Size = UDim2.new(0,120,0,25)
lbl.Position = UDim2.new(0,#Clothes*130,0,140)
lbl.Text = "Resetar"
lbl.TextColor3 = Color3.new(1,1,1)
lbl.TextScaled = true
lbl.BackgroundTransparency = 1

-- Animação de pulsar do botão Reset
task.spawn(function()
    while task.wait(1) do
        local grow = TweenService:Create(ResetBtn, TweenInfo.new(0.4, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {Size = UDim2.new(0,130,0,130)})
        local shrink = TweenService:Create(ResetBtn, TweenInfo.new(0.4, Enum.EasingStyle.Sine, Enum.EasingDirection.In), {Size = UDim2.new(0,120,0,120)})
        grow:Play()
        grow.Completed:Wait()
        shrink:Play()
        shrink.Completed:Wait()
    end
end)

-- Função reset
ResetBtn.MouseButton1Click:Connect(function()
    pcall(function()
        LocalPlayer.Character:BreakJoints()
    end)
end)
