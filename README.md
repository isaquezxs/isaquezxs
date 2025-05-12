-- Chaos Hub com ícone do Homem-Aranha (sem sistema de chave)

-- Criar GUI
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local ToggleButton = Instance.new("ImageButton")
local UICorner = Instance.new("UICorner")
local ScriptBox = Instance.new("TextBox")
local ExecuteButton = Instance.new("TextButton")

-- Parente da GUI
ScreenGui.Parent = game.CoreGui
ScreenGui.Name = "SpiderHub"

-- ToggleButton (ícone do Homem-Aranha)
ToggleButton.Name = "ToggleButton"
ToggleButton.Parent = ScreenGui
ToggleButton.Size = UDim2.new(0, 50, 0, 50)
ToggleButton.Position = UDim2.new(0, 10, 0.5, -25)
ToggleButton.BackgroundTransparency = 1
ToggleButton.Image = "rbxassetid://12623495343" -- Ícone do Homem-Aranha

-- MainFrame (menu)
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.Size = UDim2.new(0, 300, 0, 200)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -100)
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainFrame.Visible = false

UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

-- ScriptBox
ScriptBox.Parent = MainFrame
ScriptBox.Size = UDim2.new(0.9, 0, 0.6, 0)
ScriptBox.Position = UDim2.new(0.05, 0, 0.1, 0)
ScriptBox.Text = "-- Cole seu script aqui"
ScriptBox.TextWrapped = true
ScriptBox.TextXAlignment = Enum.TextXAlignment.Left
ScriptBox.TextYAlignment = Enum.TextYAlignment.Top
ScriptBox.ClearTextOnFocus = false
ScriptBox.MultiLine = true
ScriptBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
ScriptBox.TextColor3 = Color3.fromRGB(255, 255, 255)

-- ExecuteButton
ExecuteButton.Parent = MainFrame
ExecuteButton.Size = UDim2.new(0.9, 0, 0.2, 0)
ExecuteButton.Position = UDim2.new(0.05, 0, 0.75, 0)
ExecuteButton.Text = "Executar"
ExecuteButton.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
ExecuteButton.TextColor3 = Color3.fromRGB(255, 255, 255)

-- Funções
ToggleButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

ExecuteButton.MouseButton1Click:Connect(function()
    local success, err = pcall(function()
        loadstring(ScriptBox.Text)()
    end)
    if not success then
        warn("Erro ao executar script:", err)
    end
end)
