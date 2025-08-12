-- Carrega Rayfield
local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()

-- Janela Principal
local Janela = Rayfield:CreateWindow({
    Name = "VenomX hub",
    LoadingTitle = "Carregando...",
    LoadingSubtitle = "By doguinho",
    ConfigurationSaving = { Enabled = true, FolderName = "PainelSynk", FileName = "painel_config" },
Theme = "Amethyst" -- Tema roxo aplicado
})

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local bauIndex = 1
local espAtivo = false
local espConnection = nil
local flyAtivo = false
local flyConnection = nil
local noclipAtivo = false
local noclipConnection = nil

local function puxarItem(nome)
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local pos = char.HumanoidRootPart.Position
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == nome then
            if obj:IsA("BasePart") then
                obj.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
            elseif obj:IsA("Model") then
                local primary = obj.PrimaryPart or obj:FindFirstChild("HumanoidRootPart")
                if primary then
                    obj:SetPrimaryPartCFrame(CFrame.new(pos + Vector3.new(0,3,0)))
                end
            end
        end
    end
end

local function puxarGrupo(listaNomes)
    for _, nome in ipairs(listaNomes) do
        puxarItem(nome)
    end
end

local function teleportarAte(nome)
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == nome then
            local destino = (obj:IsA("BasePart") and obj) or (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart"))
            if destino then
                char.HumanoidRootPart.CFrame = destino.CFrame + Vector3.new(0,5,0)
                break
            end
        end
    end
end

-- Função Noclip
local function ativarNoclip()
    local char = LocalPlayer.Character
    if not char then return end
    
    noclipConnection = RunService.Stepped:Connect(function()
        if not noclipAtivo then return end
        
        local char = LocalPlayer.Character
        if not char then return end
        
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") and part.CanCollide then
                part.CanCollide = false
            end
        end
    end)
end

local function desativarNoclip()
    if noclipConnection then
        noclipConnection:Disconnect()
        noclipConnection = nil
    end
    
    local char = LocalPlayer.Character
    if char then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                part.CanCollide = true
            end
        end
    end
end

-- Função Fly
local function ativarFly()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    
    local humanoid = char:FindFirstChild("Humanoid")
    local rootPart = char.HumanoidRootPart
    
    -- Cria BodyVelocity para controlar o movimento
    local bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.Parent = rootPart
    
    local camera = workspace.CurrentCamera
    local speed = 50
    
    flyConnection = RunService.Heartbeat:Connect(function()
        if not flyAtivo then return end
        
        local moveVector = Vector3.new(0, 0, 0)
        local userInput = game:GetService("UserInputService")
        
        -- Controles de movimento
        if userInput:IsKeyDown(Enum.KeyCode.W) then
            moveVector = moveVector + camera.CFrame.LookVector
        end
        if userInput:IsKeyDown(Enum.KeyCode.S) then
            moveVector = moveVector - camera.CFrame.LookVector
        end
        if userInput:IsKeyDown(Enum.KeyCode.A) then
            moveVector = moveVector - camera.CFrame.RightVector
        end
        if userInput:IsKeyDown(Enum.KeyCode.D) then
            moveVector = moveVector + camera.CFrame.RightVector
        end
        if userInput:IsKeyDown(Enum.KeyCode.Space) then
            moveVector = moveVector + Vector3.new(0, 1, 0)
        end
        if userInput:IsKeyDown(Enum.KeyCode.LeftShift) then
            moveVector = moveVector - Vector3.new(0, 1, 0)
        end
        
        -- Aplica movimento
        if moveVector.Magnitude > 0 then
            moveVector = moveVector.Unit * speed
        end
        
        bodyVelocity.Velocity = moveVector
    end)
    
    -- Remove gravidade
    if humanoid then
        humanoid.PlatformStand = true
    end
    
    return bodyVelocity
end

local function desativarFly()
    if flyConnection then
        flyConnection:Disconnect()
        flyConnection = nil
    end
    
    local char = LocalPlayer.Character
    if char then
        local humanoid = char:FindFirstChild("Humanoid")
        local rootPart = char:FindFirstChild("HumanoidRootPart")
        
        if humanoid then
            humanoid.PlatformStand = false
        end
        
        if rootPart then
            local bodyVelocity = rootPart:FindFirstChild("BodyVelocity")
            if bodyVelocity then
                bodyVelocity:Destroy()
            end
        end
    end
end

-- Abas
local AbaMain = Janela:CreateTab("🔧 Main")
local AbaAlien = Janela:CreateTab("👽 Alien")
local AbaFarm = Janela:CreateTab("🌲 Farm")
local AbaItem = Janela:CreateTab("🧱 Item")
local AbaOP = Janela:CreateTab("💎 OP")
local AbaESP = Janela:CreateTab("👁️ ESP")
local AbaTP = Janela:CreateTab("🧭 Teleportes")
local AbaOutros = Janela:CreateTab("🧪 Outros")

-- MAIN
AbaMain:CreateSection("🍖 Comida")
AbaMain:CreateButton({Name = "🍖 Puxar Comida", Callback = function() puxarGrupo({"Morsel", "steak", "Steak"}) end})
AbaMain:CreateButton({Name = "🥕 Puxar Legumes", Callback = function() puxarGrupo({"Carrot", "carrot", "Berry", "berry"}) end})

AbaMain:CreateSection("🌲 Madeira")
AbaMain:CreateButton({Name = "🪵 Puxar Madeira", Callback = function() puxarItem("Log") end})
AbaMain:CreateButton({Name = "🌱 Puxar Mudas", Callback = function()
    for _, obj in pairs(workspace:GetDescendants()) do
        if string.lower(obj.Name):find("sapling") then
            if obj:IsA("BasePart") then
                obj.CFrame = LocalPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0,3,0)
            elseif obj:IsA("Model") then
                local p = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if p then obj:SetPrimaryPartCFrame(LocalPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0,3,0)) end
            end
        end
    end
end})

AbaMain:CreateSection("🐺 NPCs")
AbaMain:CreateButton({Name = "🐺 Puxar Lobo / Bunny / Bear", Callback = function() puxarGrupo({"Wolf", "Alpha Wolf", "Bear", "Bunny"}) end})
AbaMain:CreateButton({Name = "🐍 Puxar Cultist", Callback = function() puxarGrupo({"Cultist", "Cult Leader", "Cultist Warrior", "Crossbow Cultist"}) end})

AbaMain:CreateSection("⛽ Suprimentos")
AbaMain:CreateButton({Name = "⛽ Gasolina / Carvão", Callback = function() puxarGrupo({"Fuel Canister", "Coal"}) end})
AbaMain:CreateButton({Name = "🛢️ Puxar Petróleo (Barril)", Callback = function() puxarItem("Oil Barrel") end})

-- ALIEN
AbaAlien:CreateSection("🔫 Armas & Equipamentos")
AbaAlien:CreateButton({Name = "🔫 Arma Alien (Raygun)", Callback = function()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local pos = char.HumanoidRootPart.Position
    local achou = false
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "Raygun" then
            achou = true
            if obj:IsA("BasePart") then
                obj.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
            elseif obj:IsA("Model") then
                local p = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if p then obj:SetPrimaryPartCFrame(CFrame.new(pos + Vector3.new(0,3,0))) end
            end
        end
    end
    if achou then
        Rayfield:Notify({Title = "✅ Raygun Puxada", Content = "A Arma Alienígena foi teleportada até você.", Duration = 4})
    else
        Rayfield:Notify({Title = "❌ Raygun Não Encontrada", Content = "Nenhuma 'Raygun' foi localizada no servidor.", Duration = 4})
    end
end})

AbaAlien:CreateButton({Name = "🛡️ Armadura Alien", Callback = function()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local pos = char.HumanoidRootPart.Position
    local achou = false
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "Alien Armour" then
            achou = true
            if obj:IsA("BasePart") then
                obj.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
            elseif obj:IsA("Model") then
                local p = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if p then obj:SetPrimaryPartCFrame(CFrame.new(pos + Vector3.new(0,3,0))) end
            end
        end
    end
    if achou then
        Rayfield:Notify({Title = "✅ Armadura Alien Puxada", Content = "A Armadura Alienígena foi teleportada até você.", Duration = 4})
    else
        Rayfield:Notify({Title = "❌ Armadura Não Encontrada", Content = "Nenhuma 'Alien Armour' foi localizada no servidor.", Duration = 4})
    end
end})

AbaAlien:CreateButton({Name = "⚔️ Espada Laser", Callback = function()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local pos = char.HumanoidRootPart.Position
    local achou = false
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "Laser Sword" then
            achou = true
            if obj:IsA("BasePart") then
                obj.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
            elseif obj:IsA("Model") then
                local p = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if p then obj:SetPrimaryPartCFrame(CFrame.new(pos + Vector3.new(0,3,0))) end
            end
        end
    end
    if achou then
        Rayfield:Notify({Title = "✅ Espada Laser Puxada", Content = "A Espada Laser foi teleportada até você.", Duration = 4})
    else
        Rayfield:Notify({Title = "❌ Espada Laser Não Encontrada", Content = "Nenhuma 'Laser Sword' foi localizada no servidor.", Duration = 4})
    end
end})

AbaAlien:CreateButton({Name = "🥪 Meat? Sandwich", Callback = function()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local pos = char.HumanoidRootPart.Position
    local achou = false
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "Meat? Sandwich" then
            achou = true
            if obj:IsA("BasePart") then
                obj.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
            elseif obj:IsA("Model") then
                local p = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if p then obj:SetPrimaryPartCFrame(CFrame.new(pos + Vector3.new(0,3,0))) end
            end
        end
    end
    if achou then
        Rayfield:Notify({Title = "✅ Meat? Sandwich Puxado", Content = "O 'Meat? Sandwich' foi teleportado até você.", Duration = 4})
    else
        Rayfield:Notify({Title = "❌ Meat? Sandwich Não Encontrado", Content = "Nenhum 'Meat? Sandwich' foi localizado no servidor.", Duration = 4})
    end
end})

AbaAlien:CreateSection("🔧 Construção & Blueprints")
AbaAlien:CreateButton({Name = "🚧 Laser Fence", Callback = function()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local pos = char.HumanoidRootPart.Position
    local achou = false
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "Laser Fence Blueprint" then
            achou = true
            if obj:IsA("BasePart") then
                obj.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
            elseif obj:IsA("Model") then
                local p = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if p then obj:SetPrimaryPartCFrame(CFrame.new(pos + Vector3.new(0,3,0))) end
            end
        end
    end
    if achou then
        Rayfield:Notify({Title = "✅ Laser Fence Puxado", Content = "O Blueprint da Cerca Laser foi teleportado até você.", Duration = 4})
    else
        Rayfield:Notify({Title = "❌ Laser Fence Não Encontrado", Content = "Nenhum 'Laser Fence Blueprint' foi localizado no servidor.", Duration = 4})
    end
end})

-- BOTÃO UFO SCRAP MODIFICADO (agora puxa UFO Scrap e UFO Component)
AbaAlien:CreateButton({Name = "🔩 UFO Scrap & Component", Callback = function()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local pos = char.HumanoidRootPart.Position
    local achouScrap = false
    local achouComponent = false
    
    -- Procura UFO Scrap
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "UFO Scrap" then
            achouScrap = true
            if obj:IsA("BasePart") then
                obj.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
            elseif obj:IsA("Model") then
                local p = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if p then obj:SetPrimaryPartCFrame(CFrame.new(pos + Vector3.new(0,3,0))) end
            end
        end
    end
    
    -- Procura UFO Component
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "UFO Component" then
            achouComponent = true
            if obj:IsA("BasePart") then
                obj.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
            elseif obj:IsA("Model") then
                local p = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if p then obj:SetPrimaryPartCFrame(CFrame.new(pos + Vector3.new(0,3,0))) end
            end
        end
    end
    
    local mensagem = ""
    if achouScrap and achouComponent then
        mensagem = "UFO Scrap e UFO Component foram teleportados até você."
    elseif achouScrap then
        mensagem = "Apenas UFO Scrap foi encontrado e teleportado."
    elseif achouComponent then
        mensagem = "Apenas UFO Component foi encontrado e teleportado."
    else
        mensagem = "Nenhum UFO Scrap ou UFO Component foi localizado no servidor."
    end
    
    Rayfield:Notify({
        Title = achouScrap or achouComponent and "✅ Items Puxados" or "❌ Items Não Encontrados", 
        Content = mensagem, 
        Duration = 4
    })
end})

-- NOVO BOTÃO: BAÚ ALIEN
AbaAlien:CreateButton({Name = "📦 Baú Alien", Callback = function()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local pos = char.HumanoidRootPart.Position
    local achou = false
    
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "Alien Chest" then
            achou = true
            if obj:IsA("BasePart") then
                obj.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
            elseif obj:IsA("Model") then
                local p = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if p then obj:SetPrimaryPartCFrame(CFrame.new(pos + Vector3.new(0,3,0))) end
            end
        end
    end
    
    if achou then
        Rayfield:Notify({Title = "✅ Baú Alien Puxado", Content = "O Alien Chest foi teleportado até você.", Duration = 4})
    else
        Rayfield:Notify({Title = "❌ Baú Alien Não Encontrado", Content = "Nenhum 'Alien Chest' foi localizado no servidor.", Duration = 4})
    end
end})

AbaAlien:CreateSection("🌀 Teleporte")
AbaAlien:CreateButton({Name = "🌀 Teleporte Nave", Callback = function()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local achou = false
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "Teleporter" then
            achou = true
            local destino = obj:IsA("BasePart") and obj or (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart"))
            if destino then
                char.HumanoidRootPart.CFrame = destino.CFrame + Vector3.new(0,5,0)
                break
            end
        end
    end
    if achou then
        Rayfield:Notify({Title = "✅ Teleportado para Nave", Content = "Você foi teleportado para o Teleporter.", Duration = 4})
    else
        Rayfield:Notify({Title = "❌ Teleporter Não Encontrado", Content = "Nenhum 'Teleporter' foi localizado no servidor.", Duration = 4})
    end
end})

-- FARM
AbaFarm:CreateSection("⚠️ Aviso")
AbaFarm:CreateButton({Name = "EM BREVE!", Callback = function()
    Rayfield:Notify({Title = "Aviso", Content = "Funcionalidade Farm será adicionada em breve.", Duration = 5})
end})

-- ITEM
AbaItem:CreateSection("🩹 Cura")
AbaItem:CreateButton({Name = "Puxar Itens de Cura", Callback = function() puxarGrupo({"bandage","Bandage","MedKit","medkit"}) end})

AbaItem:CreateSection("🔫 Armas e Munição")
AbaItem:CreateButton({Name = "Munição Revólver", Callback = function() puxarItem("Revolver Ammo") end})
AbaItem:CreateButton({Name = "Rifle e Munição", Callback = function() puxarGrupo({"Rifle", "Rifle Ammo"}) end})

AbaItem:CreateSection("🎒 Equipamentos")
AbaItem:CreateButton({Name = "Bom Saco", Callback = function() puxarItem("Good Sack") end})
AbaItem:CreateButton({Name = "Bom Machado", Callback = function() puxarItem("Good Axe") end})
AbaItem:CreateButton({Name = "Leather Body (TP)", Callback = function() teleportarAte("Leather Body") end})

AbaItem:CreateSection("🔩 Materiais")
AbaItem:CreateButton({Name = "Puxar Itens Metálicos", Callback = function()
    puxarGrupo({"Bolt","Sheet Metal","Old Radio","Broken Fan","Broken Microwave"})
end})

-- OP
AbaOP:CreateButton({
    Name = "📦 Teleporte Cíclico Baús",
    Callback = function()
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        local pos = char.HumanoidRootPart.Position
        local baus = {}
        for _, obj in pairs(workspace:GetDescendants()) do
            if (obj.Name == "ChestLid" or obj.Name == "Item Chest") then
                local base = obj:IsA("Model") and (obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")) or obj
                if base then table.insert(baus, {part = base, dist = (base.Position - pos).Magnitude}) end
            end
        end
        table.sort(baus, function(a,b) return a.dist < b.dist end)
        if #baus == 0 then return end
        if bauIndex > #baus then bauIndex = 1 end
        char.HumanoidRootPart.CFrame = baus[bauIndex].part.CFrame + Vector3.new(0,5,0)
        Rayfield:Notify({Title = "✅ Teleportado", Content = "Teleportado para Baú " .. bauIndex .. " de " .. #baus, Duration = 3})
        bauIndex += 1
    end
})

-- ESP
AbaESP:CreateToggle({
    Name = "🔴 ESP Players (Highlight por Time)",
    CurrentValue = false,
    Callback = function(value)
        espAtivo = value

        local function aplicarHighlight(player)
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                if not player.Character:FindFirstChild("Highlight") then
                    local highlight = Instance.new("Highlight")
                    highlight.Name = "Highlight"
                    highlight.Adornee = player.Character
                    highlight.Parent = player.Character
                    highlight.FillTransparency = 0.3
                    highlight.OutlineTransparency = 0.1

                    if player.Team == LocalPlayer.Team then
                        highlight.FillColor = Color3.fromRGB(0, 170, 255)
                    else
                        highlight.FillColor = Color3.fromRGB(255, 0, 0)
                    end
                end
            end
        end

        if value then
            for _, player in ipairs(Players:GetPlayers()) do aplicarHighlight(player) end
            espConnection = Players.PlayerAdded:Connect(function(player)
                player.CharacterAdded:Connect(function() wait(1) aplicarHighlight(player) end)
            end)
        else
            if espConnection then espConnection:Disconnect() espConnection = nil end
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character and player.Character:FindFirstChild("Highlight") then
                    player.Character.Highlight:Destroy()
                end
            end
        end
    end
})

-- TELEPORTES
AbaTP:CreateButton({Name = "🏠 Área Inicial", Callback = function()
    LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0,5,0)
end})

AbaTP:CreateButton({Name = "🪖 TP Base Militar", Callback = function()
    for _, obj in pairs(workspace:GetDescendants()) do
        if string.lower(obj.Name):find("military base") then
            local destino = obj:IsA("BasePart") and obj or obj.PrimaryPart
            if destino then
                LocalPlayer.Character.HumanoidRootPart.CFrame = destino.CFrame + Vector3.new(0,5,0)
                break
            end
        end
    end
end})

AbaTP:CreateButton({Name = "🚧 TP Construction Site", Callback = function()
    for _, obj in pairs(workspace:GetDescendants()) do
        if string.lower(obj.Name):find("construction site") then
            local destino = obj:IsA("BasePart") and obj or obj.PrimaryPart
            if destino then
                LocalPlayer.Character.HumanoidRootPart.CFrame = destino.CFrame + Vector3.new(0,5,0)
                break
            end
        end
    end
end})

-- OUTROS
AbaOutros:CreateSection("👻 Noclip")

-- Toggle para Noclip
AbaOutros:CreateToggle({
    Name = "👻 NOCLIP - On/Off",
    CurrentValue = false,
    Callback = function(value)
        noclipAtivo = value
        
        if noclipAtivo then
            ativarNoclip()
            Rayfield:Notify({
                Title = "✅ Noclip Ativado", 
                Content = "Agora você pode atravessar paredes!", 
                Duration = 4
            })
        else
            desativarNoclip()
            Rayfield:Notify({
                Title = "❌ Noclip Desativado", 
                Content = "Colisão restaurada!", 
                Duration = 3
            })
        end
    end
})

AbaOutros:CreateButton({
    Name = "🛡️ Painel Admin (Infinite Yield)",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
        Rayfield:Notify({
            Title = "✅ Admin Ativado",
            Content = "Painel Infinite Yield carregado! Use ;help para comandos",
            Duration = 5
        })
    end
})

AbaOutros:CreateButton({
    Name = "👑 Painel Admin (CMD-X)",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/CMD-X/CMD-X/master/Source",true))()
        Rayfield:Notify({
            Title = "✅ Admin Ativado",
            Content = "Painel CMD-X carregado! Pressione END para abrir",
            Duration = 5
        })
    end
})

-- Botão adicional para script externo (opcional)
AbaOutros:CreateButton({
    Name = "🌟 FLY - ARCEUS (Externo)",
    Callback = function()
        loadstring("\108\111\97\100\115\116\114\105\110\103\40\103\97\109\101\58\72\116\116\112\71\101\116\40\40\39\104\116\116\112\115\58\47\47\103\105\115\116\46\103\105\116\104\117\98\117\115\101\114\99\111\110\116\101\110\116\46\99\111\109\47\109\101\111\122\111\110\101\89\84\47\98\102\48\51\55\100\102\102\57\102\48\97\55\48\48\49\55\51\48\52\100\100\100\54\55\102\100\99\100\51\55\48\47\114\97\119\47\101\49\52\101\55\52\102\52\50\53\98\48\54\48\100\102\53\50\51\51\52\51\99\102\51\48\98\55\56\55\48\55\52\101\98\51\99\53\100\50\47\97\114\99\101\117\115\37\50\53\50\48\120\37\50\53\50\48\102\108\121\37\50\53\50\48\50\37\50\53\50\48\111\98\102\108\117\99\97\116\111\114\39\41\44\116\114\117\101\41\41\40\41\10\10")()
        Rayfield:Notify({Title = "🛸 Fly Externo", Content = "Script Fly - ARCEUS executado!", Duration = 3})
    end
})

--
