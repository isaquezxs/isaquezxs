-- Night Hub

-- Painel
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.Players.LocalPlayer.PlayerGui

local Frame = Instance.new("Frame")
Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
Frame.Position = UDim2.new(0.5, -200, 0.5, -150)
Frame.Size = UDim2.new(0, 400, 0, 300)

local TextLabel = Instance.new("TextLabel")
TextLabel.Parent = Frame
TextLabel.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
TextLabel.Position = UDim2.new(0, 0, 0, 0)
TextLabel.Size = UDim2.new(1, 0, 0, 30)
TextLabel.Text = "SpiderHub"
TextLabel.TextColor3 = Color3.new(1, 1, 1)
TextLabel.TextSize = 20

-- Botões
local function criarBotao(texto, callback)
    local botao = Instance.new("TextButton")
    botao.Parent = Frame
    botao.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
    botao.Position = UDim2.new(0, 10, 0, 40 + (#Frame:GetChildren() - 2) * 40)
    botao.Size = UDim2.new(0, 380, 0, 30)
    botao.Text = texto
    botao.TextColor3 = Color3.new(1, 1, 1)
    botao.TextSize = 16
    botao.MouseButton1Click:Connect(callback)
    return botao
end

-- Exemplos de botões
local botaoVoo = criarBotao("Voo", function()
    -- Código para voo
    game.Players.LocalPlayer.Character.HumanoidRootPart.Anchored = true
    while true do
        game.Players.LocalPlayer.Character.HumanoidRootPart.Velocity = Vector3.new(0, 50, 0)
        wait()
    end
end)

local botaoVelocidade = criarBotao("Velocidade", function()
    -- Código para velocidade
    game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = 100
end)

-- Fechar painel
local function fecharPainel()
    ScreenGui.Enabled = false
end

local botaoFechar = criarBotao("Fechar", fecharPainel)
botaoFechar.Position = UDim2.new(0, 10, 0, 240)

-- Abrir painel
ScreenGui.Enabled = true
-- Fling Boat
local function flingBoat()
    local boat = game.Workspace:FindFirstChild("Boat")
    if boat then
        boat.HumanoidRootPart.Anchored = false
        boat.HumanoidRootPart.Velocity = Vector3.new(0, 1000, 0)
    end
end

-- Fling Ball
local function flingBall()
    local ball = Instance.new("Part")
    ball.Parent = game.Workspace
    ball.Shape = Enum.PartType.Ball
    ball.Size = Vector3.new(5, 5, 5)
    ball.Position = game.Players.LocalPlayer.Character.HumanoidRootPart.Position
    ball.Velocity = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.LookVector * 1000
end

-- Botões
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.Players.LocalPlayer.PlayerGui

local Frame = Instance.new("Frame")
Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
Frame.Position = UDim2.new(0.5, -100, 0.5, -50)
Frame.Size = UDim2.new(0, 200, 0, 100)

local botaoFlingBoat = Instance.new("TextButton")
botaoFlingBoat.Parent = Frame
botaoFlingBoat.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
botaoFlingBoat.Position = UDim2.new(0, 10, 0, 10)
botaoFlingBoat.Size = UDim2.new(0, 180, 0, 30)
botaoFlingBoat.Text = "Fling Boat"
botaoFlingBoat.MouseButton1Click:Connect(flingBoat)

local botaoFlingBall = Instance.new("TextButton")
botaoFlingBall.Parent = Frame
botaoFlingBall.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
botaoFlingBall.Position = UDim2.new(0, 10, 0, 50)
botaoFlingBall.Size = UDim2.new(0, 180, 0, 30)
botaoFlingBall.Text = "Fling Ball"
botaoFlingBall.MouseButton1Click:Connect(flingBall)
