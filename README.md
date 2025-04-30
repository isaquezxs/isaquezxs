
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Criar interface de usuário
local ScreenGui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 300, 0, 400)
Frame.Position = UDim2.new(0.5, -150, 0.5, -200)
Frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)

local function teleportToPlayer(targetPlayer)
    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = targetPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 3, 0)
    end
end

local yOffset = 10
for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        local btn = Instance.new("TextButton", Frame)
        btn.Size = UDim2.new(1, -20, 0, 30)
        btn.Position = UDim2.new(
0
