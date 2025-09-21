--[[ 
Script UI - Strongest Battlegrounds
Interface: Preto e Branco
Botões: Pretos + Transparentes
Função: Aim Lock
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
MainButton.Image = "rbxassetid://121642098900456"coloque a imagem da foto aqui
MainButton.BackgroundTransparency = 1

-- CÍRCULO AMARELO AO REDOR
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

-- INTERFACE PRINCIPAL
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0,250,0,300)
Frame.Position = UDim2.new(0.5,-125,0.5,-150)
Frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
Frame.Visible = false
Frame.BackgroundTransparency = 0.2

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
AimButton.Position = UDim2.new(0.1,0,0.3,0)
AimButton.Text = "Ativar AimLock"
AimButton.TextColor3 = Color3.new(1,1,1)
AimButton.BackgroundColor3 = Color3.fromRGB(0,0,0)
AimButton.BackgroundTransparency = 0.3

-- VARIÁVEIS AIMLOCK
local AimLockEnabled = false
local Target

-- Função de encontrar alvo mais próximo
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

-- Loop do AimLock
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

-- Botão de fechar
CloseButton.MouseButton1Click:Connect(function()
    Frame.Visible = false
end)

-- Abrir interface pelo botão principal
MainButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)
