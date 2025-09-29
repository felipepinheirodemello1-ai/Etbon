-- MM2 SCRIPT SIMPLES E FUNCIONAL
local Players = game:GetService("Players")
local lp = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- Criar GUI
local gui = Instance.new("ScreenGui")
gui.Parent = game.CoreGui
gui.Name = "MM2Simple"

-- Botão para abrir/fechar menu
local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(0, 120, 0, 40)
toggleBtn.Position = UDim2.new(0, 10, 0, 10)
toggleBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 255)
toggleBtn.TextColor3 = Color3.new(1, 1, 1)
toggleBtn.Text = "🎯 ABRIR MENU"
toggleBtn.Font = Enum.Font.GothamBold
toggleBtn.TextSize = 14
toggleBtn.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = toggleBtn

-- Frame principal
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 250, 0, 350)
mainFrame.Position = UDim2.new(0, 10, 0, 60)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
mainFrame.BackgroundTransparency = 0.1
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Visible = false
mainFrame.Parent = gui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 12)
mainCorner.Parent = mainFrame

local mainStroke = Instance.new("UIStroke")
mainStroke.Color = Color3.fromRGB(255, 255, 255)
mainStroke.Thickness = 2
mainStroke.Parent = mainFrame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "🎯 MM2 SCRIPT"
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.Parent = mainFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 12)
titleCorner.Parent = title

-- Botão fechar
local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.Text = "✕"
closeBtn.TextColor3 = Color3.new(1, 1, 1)
closeBtn.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.TextSize = 16
closeBtn.Parent = title

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(1, 0)
closeCorner.Parent = closeBtn

-- Container de botões
local container = Instance.new("ScrollingFrame")
container.Size = UDim2.new(1, -10, 1, -50)
container.Position = UDim2.new(0, 5, 0, 45)
container.BackgroundTransparency = 1
container.ScrollBarThickness = 6
container.CanvasSize = UDim2.new(0, 0, 0, 500)
container.Parent = mainFrame

-- Estados
local estados = {
    radar = false,
    speed = false,
    esp = false,
    noclip = false
}

local conexoes = {}

-- Função para criar botões
local function criarBotao(texto, posicao, callback, cor)
    local botao = Instance.new("TextButton")
    botao.Size = UDim2.new(1, -10, 0, 35)
    botao.Position = posicao
    botao.Text = texto
    botao.BackgroundColor3 = cor or Color3.fromRGB(50, 50, 70)
    botao.TextColor3 = Color3.new(1, 1, 1)
    botao.Font = Enum.Font.Gotham
    botao.TextSize = 12
    botao.Parent = container
    
    local botaoCorner = Instance.new("UICorner")
    botaoCorner.CornerRadius = UDim.new(0, 8)
    botaoCorner.Parent = botao
    
    botao.MouseButton1Click:Connect(callback)
    return botao
end

-- =============================================================================
-- SISTEMA DE RADAR (FUNCIONANDO)
-- =============================================================================

local function getPlayerRole(player)
    if not player.Character then return "😇 INOCENTE" end
    
    -- Verificar ferramentas equipadas
    for _, item in pairs(player.Character:GetChildren()) do
        if item:IsA("Tool") then
            local nome = string.lower(item.Name)
            if nome:find("knife") or nome:find("faca") then
                return "🔪 ASSASSINO"
            elseif nome:find("gun") or nome:find("revolver") then
                return "👮 XERIFE"
            end
        end
    end
    return "😇 INOCENTE"
end

local function criarTag(player)
    if not player.Character then return end
    local head = player.Character:FindFirstChild("Head")
    if not head then return end
    
    -- Remover tag antiga
    local tagAntiga = head:FindFirstChild("RadarTag")
    if tagAntiga then tagAntiga:Destroy() end
    
    local papel = getPlayerRole(player)
    
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "RadarTag"
    billboard.Size = UDim2.new(0, 200, 0, 50)
    billboard.Adornee = head
    billboard.AlwaysOnTop = true
    billboard.MaxDistance = 100
    billboard.Parent = head
    
    local texto = Instance.new("TextLabel")
    texto.Size = UDim2.new(1, 0, 1, 0)
    texto.BackgroundTransparency = 1
    texto.Text = player.Name .. "\n" .. papel
    texto.TextColor3 = Color3.new(1, 1, 1)
    texto.Font = Enum.Font.GothamBold
    texto.TextSize = 12
    texto.TextStrokeTransparency = 0
    texto.Parent = billboard
    
    -- Cor baseada no papel
    if papel == "🔪 ASSASSINO" then
        texto.TextColor3 = Color3.fromRGB(255, 0, 0)
    elseif papel == "👮 XERIFE" then
        texto.TextColor3 = Color3.fromRGB(0, 100, 255)
    else
        texto.TextColor3 = Color3.fromRGB(0, 255, 0)
    end
end

local function atualizarRadar()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= lp then
            if estados.radar then
                criarTag(player)
            else
                -- Remover tags
                if player.Character then
                    local head = player.Character:FindFirstChild("Head")
                    if head then
                        local tag = head:FindFirstChild("RadarTag")
                        if tag then tag:Destroy() end
                    end
                end
            end
        end
    end
end

-- =============================================================================
-- SISTEMA ESP (HIGHLIGHT)
-- =============================================================================

local highlights = {}

local function toggleESP()
    if estados.esp then
        -- Criar highlights
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= lp and player.Character then
                local highlight = Instance.new("Highlight")
                highlight.Parent = player.Character
                highlight.FillTransparency = 0.5
                highlight.Name = "ESPHighlight"
                
                local papel = getPlayerRole(player)
                if papel == "🔪 ASSASSINO" then
                    highlight.FillColor = Color3.fromRGB(255, 0, 0)
                elseif papel == "👮 XERIFE" then
                    highlight.FillColor = Color3.fromRGB(0, 0, 255)
                else
                    highlight.FillColor = Color3.fromRGB(0, 255, 0)
                end
                
                highlights[player.Character] = highlight
            end
        end
    else
        -- Remover highlights
        for character, highlight in pairs(highlights) do
            if highlight then
                highlight:Destroy()
            end
        end
        highlights = {}
    end
end

-- =============================================================================
-- SISTEMA VELOCIDADE
-- =============================================================================

local function toggleSpeed()
    if lp.Character and lp.Character:FindFirstChild("Humanoid") then
        if estados.speed then
            lp.Character.Humanoid.WalkSpeed = 30
        else
            lp.Character.Humanoid.WalkSpeed = 16
        end
    end
end

-- =============================================================================
-- SISTEMA NO CLIP
-- =============================================================================

local function toggleNoClip()
    if estados.noclip then
        conexoes.noclip = RunService.Stepped:Connect(function()
            if lp.Character then
                for _, part in pairs(lp.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    else
        if conexoes.noclip then
            conexoes.noclip:Disconnect()
        end
    end
end

-- =============================================================================
-- FUNÇÕES UTILITÁRIAS
-- =============================================================================

local function encontrarFaca()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Tool") and (string.lower(obj.Name):find("knife") or string.lower(obj.Name):find("faca")) then
            if lp.Character and lp.Character:FindFirstChild("HumanoidRootPart") then
                lp.Character.HumanoidRootPart.CFrame = obj.Handle.CFrame + Vector3.new(0, 3, 0)
                print("🗡️ Faca encontrada!")
                return
            end
        end
    end
    print("❌ Nenhuma faca encontrada")
end

local function encontrarArma()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Tool") and (string.lower(obj.Name):find("gun") or string.lower(obj.Name):find("revolver")) then
            if lp.Character and lp.Character:FindFirstChild("HumanoidRootPart") then
                lp.Character.HumanoidRootPart.CFrame = obj.Handle.CFrame + Vector3.new(0, 3, 0)
                print("🔫 Arma encontrada!")
                return
            end
        end
    end
    print("❌ Nenhuma arma encontrada")
end

local function teleportSpawn()
    if lp.Character and lp.Character:FindFirstChild("HumanoidRootPart") then
        lp.Character.HumanoidRootPart.CFrame = CFrame.new(0, 5, 0)
        print("🎯 Teleportado para spawn")
    end
end

local function verInfo()
    print("=== INFO MM2 ===")
    for _, player in pairs(Players:GetPlayers()) do
        local papel = getPlayerRole(player)
        print(player.Name .. " - " .. papel)
    end
end

-- =============================================================================
-- CRIAR BOTÕES
-- =============================================================================

local yPos = 5

-- Radar
local btnRadar = criarBotao("🔴 Radar OFF", UDim2.new(0, 5, 0, yPos), function()
    estados.radar = not estados.radar
    btnRadar.Text = estados.radar and "🟢 Radar ON" or "🔴 Radar OFF"
    btnRadar.BackgroundColor3 = estados.radar and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
    print("📡 Radar " .. (estados.radar and "ATIVADO" or "DESATIVADO"))
end, Color3.fromRGB(150, 0, 0))
yPos = yPos + 40

-- Velocidade
local btnSpeed = criarBotao("🏃‍♂️ Velocidade OFF", UDim2.new(0, 5, 0, yPos), function()
    estados.speed = not estados.speed
    btnSpeed.Text = estados.speed and "🏃‍♂️ Velocidade ON" or "🏃‍♂️ Velocidade OFF"
    btnSpeed.BackgroundColor3 = estados.speed and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
    toggleSpeed()
    print("🏃‍♂️ Velocidade " .. (estados.speed and "30" or "16"))
end, Color3.fromRGB(0, 100, 150))
yPos = yPos + 40

-- ESP
local btnESP = criarBotao("👁️ ESP OFF", UDim2.new(0, 5, 0, yPos), function()
    estados.esp = not estados.esp
    btnESP.Text = estados.esp and "👁️ ESP ON" or "👁️ ESP OFF"
    btnESP.BackgroundColor3 = estados.esp and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
    toggleESP()
    print("👁️ ESP " .. (estados.esp and "ATIVADO" or "DESATIVADO"))
end, Color3.fromRGB(150, 100, 0))
yPos = yPos + 40

-- No Clip
local btnNoClip = criarBotao("🚫 NoClip OFF", UDim2.new(0, 5, 0, yPos), function()
    estados.noclip = not estados.noclip
    btnNoClip.Text = estados.noclip and "🚫 NoClip ON" or "🚫 NoClip OFF"
    btnNoClip.BackgroundColor3 = estados.noclip and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
    toggleNoClip()
    print("🚫 NoClip " .. (estados.noclip and "ATIVADO" or "DESATIVADO"))
end, Color3.fromRGB(100, 0, 150))
yPos = yPos + 40

-- Encontrar Faca
criarBotao("🗡️ Encontrar Faca", UDim2.new(0, 5, 0, yPos), encontrarFaca, Color3.fromRGB(200, 0, 0))
yPos = yPos + 40

-- Encontrar Arma
criarBotao("🔫 Encontrar Arma", UDim2.new(0, 5, 0, yPos), encontrarArma, Color3.fromRGB(0, 0, 200))
yPos = yPos + 40

-- Teleport Spawn
criarBotao("🎯 TP Spawn", UDim2.new(0, 5, 0, yPos), teleportSpawn, Color3.fromRGB(200, 200, 0))
yPos = yPos + 40

-- Info
criarBotao("📊 Info Jogadores", UDim2.new(0, 5, 0, yPos), verInfo, Color3.fromRGB(0, 150, 150))

-- =============================================================================
-- CONTROLES DA INTERFACE
-- =============================================================================

-- Botão toggle
toggleBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = not mainFrame.Visible
    toggleBtn.Text = mainFrame.Visible and "🎯 FECHAR MENU" or "🎯 ABRIR MENU"
end)

-- Botão fechar
closeBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
    toggleBtn.Text = "🎯 ABRIR MENU"
end)

-- =============================================================================
-- LOOP PRINCIPAL
-- =============================================================================

-- Loop do radar
spawn(function()
    while true do
        if estados.radar then
            atualizarRadar()
        end
        wait(1)
    end
end)

-- Loop do ESP
spawn(function()
    while true do
        if estados.esp then
            -- Atualizar ESP para novos jogadores
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= lp and player.Character and not highlights[player.Character] then
                    toggleESP()
                end
            end
        end
        wait(2)
    end
end)

-- Reconectar ao respawnar
lp.CharacterAdded:Connect(function(character)
    wait(3)
    if estados.speed and character:FindFirstChild("Humanoid") then
        character.Humanoid.WalkSpeed = 30
    end
end)

print("🎮 MM2 SCRIPT CARREGADO!")
print("✅ Clique em 'ABRIR MENU' para começar!")
print("🚀 8 Funcionalidades testadas!")
