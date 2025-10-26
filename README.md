local Players = game:GetService("Players")
local player = Players.LocalPlayer
local gui = Instance.new("ScreenGui")
gui.Name = "ExportadorEtbonito"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- Botão fixo para abrir a interface
local abrirButton = Instance.new("ImageButton")
abrirButton.Name = "AbrirEtbonito"
abrirButton.Size = UDim2.new(0, 50, 0, 50)
abrirButton.Position = UDim2.new(0, 10, 0, 10)
abrirButton.Image = "rbxassetid://81739893945719"
abrirButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
abrirButton.Parent = gui

local abrirCorner = Instance.new("UICorner")
abrirCorner.CornerRadius = UDim.new(0, 8)
abrirCorner.Parent = abrirButton

-- Função para verificar pasta etbonito
local function verificarPastaEtbonito()
    local pasta = workspace:FindFirstChild("etbonito")
    if not pasta then
        return false, "❌ Crie uma pasta 'etbonito' no workspace!"
    end
    return true, pasta
end

-- Função para exportar objetos
local function exportarObjeto(obj, nivel)
    local indentacao = string.rep("    ", nivel)
    local codigo = ""
    
    if obj:IsA("Folder") then
        codigo = codigo .. indentacao .. string.format('local %s = Instance.new("Folder")', obj.Name)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Name = "%s"', obj.Name, obj.Name)
        
        for _, filho in ipairs(obj:GetChildren()) do
            codigo = codigo .. "\n\n" .. exportarObjeto(filho, nivel)
        end
        
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Parent = %s', obj.Name, nivel == 0 and "etbonito" or "p")
        
    elseif obj:IsA("Part") then
        codigo = codigo .. indentacao .. string.format('local %s = Instance.new("Part")', obj.Name)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Name = "%s"', obj.Name, obj.Name)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Size = Vector3.new(%s, %s, %s)', obj.Name, 
            tostring(obj.Size.X), tostring(obj.Size.Y), tostring(obj.Size.Z))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Position = Vector3.new(%s, %s, %s)', obj.Name,
            tostring(obj.Position.X), tostring(obj.Position.Y), tostring(obj.Position.Z))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Color = Color3.fromRGB(%d, %d, %d)', obj.Name,
            math.floor(obj.Color.R * 255), math.floor(obj.Color.G * 255), math.floor(obj.Color.B * 255))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Material = Enum.Material.%s', obj.Name, obj.Material.Name)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Transparency = %s', obj.Name, tostring(obj.Transparency))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.CanCollide = %s', obj.Name, tostring(obj.CanCollide))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Anchored = %s', obj.Name, tostring(obj.Anchored))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Parent = %s', obj.Name, nivel == 0 and "etbonito" or "p")
        
    elseif obj:IsA("Script") or obj:IsA("LocalScript") then
        codigo = codigo .. indentacao .. string.format('local %s = Instance.new("%s")', obj.Name, obj.ClassName)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Name = "%s"', obj.Name, obj.Name)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Source = [[%s]]', obj.Name, obj.Source:gsub("]]", "] ]"))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Parent = p', obj.Name)
        
    elseif obj:IsA("SpecialMesh") then
        codigo = codigo .. indentacao .. string.format('local %s = Instance.new("SpecialMesh")', obj.Name)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Name = "%s"', obj.Name, obj.Name)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.MeshId = "%s"', obj.Name, obj.MeshId)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.TextureId = "%s"', obj.Name, obj.TextureId)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Scale = Vector3.new(%s, %s, %s)', obj.Name,
            tostring(obj.Scale.X), tostring(obj.Scale.Y), tostring(obj.Scale.Z))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Parent = p', obj.Name)
        
    else
        codigo = codigo .. indentacao .. string.format('local %s = Instance.new("%s")', obj.Name, obj.ClassName)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Name = "%s"', obj.Name, obj.Name)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Parent = p', obj.Name)
    end
    
    return codigo
end

-- Função para exportar tudo
local function exportarTudoEtbonito()
    local sucesso, pasta = verificarPastaEtbonito()
    if not sucesso then
        return pasta
    end
    
    local codigo = "-- Exportação completa da pasta etbonito\n"
    codigo = codigo .. "-- Gerado por Etbonito Exportador\n\n"
    
    codigo = codigo .. "local etbonito = Instance.new(\"Folder\")\n"
    codigo = codigo .. "etbonito.Name = \"etbonito\"\n"
    codigo = codigo .. "etbonito.Parent = workspace\n\n"
    
    for _, obj in ipairs(pasta:GetChildren()) do
        codigo = codigo .. exportarObjeto(obj, 0) .. "\n\n"
    end
    
    return codigo
end

-- Função para exportar scripts
local function exportarScriptsEtbonito()
    local sucesso, pasta = verificarPastaEtbonito()
    if not sucesso then
        return pasta
    end
    
    local codigo = "-- SCRIPTS da pasta etbonito\n"
    codigo = codigo .. "-- Gerado por Etbonito Exportador\n\n"
    
    codigo = codigo .. "local etbonito = Instance.new(\"Folder\")\n"
    codigo = codigo .. "etbonito.Name = \"etbonito\"\n"
    codigo = codigo .. "etbonito.Parent = workspace\n\n"
    
    for _, obj in ipairs(pasta:GetDescendants()) do
        if obj:IsA("Script") or obj:IsA("LocalScript") then
            codigo = codigo .. string.format('-- Script: %s\n', obj.Name)
            codigo = codigo .. string.format('local script = Instance.new("%s")\n', obj.ClassName)
            codigo = codigo .. string.format('script.Name = "%s"\n', obj.Name)
            codigo = codigo .. string.format('script.Source = [[%s]]\n', obj.Source:gsub("]]", "] ]"))
            codigo = codigo .. string.format('script.Parent = etbonito\n\n')
        end
    end
    
    return codigo
end

-- Função para exportar partes
local function exportarPartesEtbonito()
    local sucesso, pasta = verificarPastaEtbonito()
    if not sucesso then
        return pasta
    end
    
    local codigo = "-- PARTES da pasta etbonito\n"
    codigo = codigo .. "-- Gerado por Etbonito Exportador\n\n"
    
    codigo = codigo .. "local etbonito = Instance.new(\"Folder\")\n"
    codigo = codigo .. "etbonito.Name = \"etbonito\"\n"
    codigo = codigo .. "etbonito.Parent = workspace\n\n"
    
    for _, obj in ipairs(pasta:GetDescendants()) do
        if obj:IsA("Part") then
            codigo = codigo .. string.format('local %s = Instance.new("Part")\n', obj.Name)
            codigo = codigo .. string.format('%s.Name = "%s"\n', obj.Name, obj.Name)
            codigo = codigo .. string.format('%s.Size = Vector3.new(%s, %s, %s)\n', obj.Name, 
                tostring(obj.Size.X), tostring(obj.Size.Y), tostring(obj.Size.Z))
            codigo = codigo .. string.format('%s.Position = Vector3.new(%s, %s, %s)\n', obj.Name,
                tostring(obj.Position.X), tostring(obj.Position.Y), tostring(obj.Position.Z))
            codigo = codigo .. string.format('%s.Color = Color3.fromRGB(%d, %d, %d)\n', obj.Name,
                math.floor(obj.Color.R * 255), math.floor(obj.Color.G * 255), math.floor(obj.Color.B * 255))
            codigo = codigo .. string.format('%s.Material = Enum.Material.%s\n', obj.Name, obj.Material.Name)
            codigo = codigo .. string.format('%s.Anchored = true\n', obj.Name)
            codigo = codigo .. string.format('%s.Parent = etbonito\n\n', obj.Name)
        end
    end
    
    return codigo
end

-- Função para criar interface
local function criarInterface()
    if gui:FindFirstChild("PainelEtbonito") then
        guui.PainelEtbonito:Destroy()
    end
    
    -- Frame principal
    local frame = Instance.new("Frame")
    frame.Name = "PainelEtbonito"
    frame.Size = UDim2.new(0, 380, 0, 420)
    frame.Position = UDim2.new(0.5, -190, 0.5, -210)
    frame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    frame.BorderSizePixel = 0
    frame.Parent = gui

    local frameCorner = Instance.new("UICorner")
    frameCorner.CornerRadius = UDim.new(0, 10)
    frameCorner.Parent = frame

    -- Header
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 60)
    header.Position = UDim2.new(0, 0, 0, 0)
    header.BackgroundColor3 = Color3.fromRGB(0, 120, 220)
    header.BorderSizePixel = 0
    header.Parent = frame

    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 10)
    headerCorner.Parent = header

    -- Logo
    local logo = Instance.new("ImageLabel")
    logo.Size = UDim2.new(0, 40, 0, 40)
    logo.Position = UDim2.new(0, 10, 0, 10)
    logo.Image = "rbxassetid://81739893945719"
    logo.BackgroundTransparency = 1
    logo.Parent = header

    -- Título
    local titulo = Instance.new("TextLabel")
    titulo.Size = UDim2.new(1, -60, 0, 30)
    titulo.Position = UDim2.new(0, 60, 0, 8)
    titulo.Text = "ETBONITO EXPORT"
    titulo.Font = Enum.Font.SourceSansBold
    titulo.TextSize = 16
    titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
    titulo.BackgroundTransparency = 1
    titulo.TextXAlignment = Enum.TextXAlignment.Left
    titulo.Parent = header

    local subtitulo = Instance.new("TextLabel")
    subtitulo.Size = UDim2.new(1, -60, 0, 20)
    subtitulo.Position = UDim2.new(0, 60, 0, 35)
    subtitulo.Text = "Use pasta 'etbonito' no workspace"
    subtitulo.Font = Enum.Font.SourceSans
    subtitulo.TextSize = 12
    subtitulo.TextColor3 = Color3.fromRGB(220, 240, 255)
    subtitulo.BackgroundTransparency = 1
    subtitulo.TextXAlignment = Enum.TextXAlignment.Left
    subtitulo.Parent = header

    -- Área de conteúdo
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -20, 1, -80)
    contentFrame.Position = UDim2.new(0, 10, 0, 70)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = frame

    -- Caixa de texto
    local textboxContainer = Instance.new("Frame")
    textboxContainer.Size = UDim2.new(1, 0, 0, 140)
    textboxContainer.Position = UDim2.new(0, 0, 0, 0)
    textboxContainer.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    textboxContainer.BorderSizePixel = 0
    textboxContainer.Parent = contentFrame

    local textboxCorner = Instance.new("UICorner")
    textboxCorner.CornerRadius = UDim.new(0, 6)
    textboxCorner.Parent = textboxContainer

    local textbox = Instance.new("TextBox")
    textbox.Name = "ExportBox"
    textbox.Size = UDim2.new(1, -10, 1, -10)
    textbox.Position = UDim2.new(0, 5, 0, 5)
    textbox.TextWrapped = true
    textbox.TextXAlignment = Enum.TextXAlignment.Left
    textbox.TextYAlignment = Enum.TextYAlignment.Top
    textbox.ClearTextOnFocus = false
    textbox.MultiLine = true
    textbox.Font = Enum.Font.SourceSans
    textbox.TextSize = 11
    textbox.Text = "Coloque seus objetos na pasta 'etbonito' do workspace"
    textbox.BackgroundTransparency = 1
    textbox.TextColor3 = Color3.fromRGB(255, 255, 255)
    textbox.Parent = textboxContainer

    -- Botões
    local buttonGrid = Instance.new("Frame")
    buttonGrid.Size = UDim2.new(1, 0, 0, 120)
    buttonGrid.Position = UDim2.new(0, 0, 0, 150)
    buttonGrid.BackgroundTransparency = 1
    buttonGrid.Parent = contentFrame

    -- Função para criar botões
    local function criarBotao(nome, texto, cor, posicao, tamanho)
        local botao = Instance.new("TextButton")
        botao.Name = nome
        botao.Size = tamanho
        botao.Position = posicao
        botao.Text = texto
        botao.Font = Enum.Font.SourceSansBold
        botao.TextSize = 12
        botao.BackgroundColor3 = cor
        botao.TextColor3 = Color3.fromRGB(255, 255, 255)
        botao.BorderSizePixel = 0
        botao.AutoButtonColor = true
        botao.Parent = buttonGrid

        local botaoCorner = Instance.new("UICorner")
        botaoCorner.CornerRadius = UDim.new(0, 5)
        botaoCorner.Parent = botao

        return botao
    end

    -- Botões principais
    local exportTudoBtn = criarBotao("ExportTudo", "🌎 EXPORTAR TUDO", Color3.fromRGB(0, 170, 255), 
        UDim2.new(0, 0, 0, 0), UDim2.new(0.48, -5, 0, 30))

    local exportPartesBtn = criarBotao("ExportPartes", "🧱 PARTES", Color3.fromRGB(0, 120, 200), 
        UDim2.new(0.5, 0, 0, 0), UDim2.new(0.48, 0, 0, 30))

    local exportScriptsBtn = criarBotao("ExportScripts", "📜 SCRIPTS", Color3.fromRGB(140, 100, 255), 
        UDim2.new(0, 0, 0, 40), UDim2.new(0.48, -5, 0, 30))

    local salvarArquivoBtn = criarBotao("SalvarArquivo", "💾 SALVAR", Color3.fromRGB(0, 180, 0), 
        UDim2.new(0.5, 0, 0, 40), UDim2.new(0.48, 0, 0, 30))

    local limparBtn = criarBotao("Limpar", "🔄 LIMPAR", Color3.fromRGB(255, 170, 0), 
        UDim2.new(0, 0, 0, 80), UDim2.new(0.48, -5, 0, 30))

    local criarPastaBtn = criarBotao("CriarPasta", "📁 CRIAR PASTA", Color3.fromRGB(255, 100, 0), 
        UDim2.new(0.5, 0, 0, 80), UDim2.new(0.48, 0, 0, 30))

    -- Botão fechar
    local fecharButton = Instance.new("TextButton")
    fecharButton.Size = UDim2.new(0, 30, 0, 30)
    fecharButton.Position = UDim2.new(1, -35, 0, 15)
    fecharButton.Text = "X"
    fecharButton.Font = Enum.Font.SourceSansBold
    fecharButton.TextSize = 14
    fecharButton.BackgroundColor3 = Color3.fromRGB(220, 60, 60)
    fecharButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    fecharButton.BorderSizePixel = 0
    fecharButton.AutoButtonColor = true
    fecharButton.Parent = header

    local fecharCorner = Instance.new("UICorner")
    fecharCorner.CornerRadius = UDim.new(0, 5)
    fecharCorner.Parent = fecharButton

    -- Conexões dos botões
    exportTudoBtn.MouseButton1Click:Connect(function()
        local codigo = exportarTudoEtbonito()
        textbox.Text = codigo
        if setclipboard then 
            setclipboard(codigo)
        end
    end)

    exportPartesBtn.MouseButton1Click:Connect(function()
        local codigo = exportarPartesEtbonito()
        textbox.Text = codigo
        if setclipboard then 
            setclipboard(codigo)
        end
    end)

    exportScriptsBtn.MouseButton1Click:Connect(function()
        local codigo = exportarScriptsEtbonito()
        textbox.Text = codigo
        if setclipboard then 
            setclipboard(codigo)
        end
    end)

    salvarArquivoBtn.MouseButton1Click:Connect(function()
        if not writefile then
            textbox.Text = "Erro: writefile não disponível"
            return
        end

        local nomeArquivo = "etbonito_export_" .. os.time() .. ".lua"
        writefile(nomeArquivo, textbox.Text)
        textbox.Text = "✅ Arquivo salvo: " .. nomeArquivo .. "\n\n" .. textbox.Text
    end)

    criarPastaBtn.MouseButton1Click:Connect(function()
        local pasta = workspace:FindFirstChild("etbonito")
        if not pasta then
            pasta = Instance.new("Folder")
            pasta.Name = "etbonito"
            pasta.Parent = workspace
            textbox.Text = "✅ Pasta 'etbonito' criada!\nColoque seus objetos nela."
        else
            textbox.Text = "✅ Pasta 'etbonito' já existe!"
        end
    end)

    limparBtn.MouseButton1Click:Connect(function()
        textbox.Text = "Coloque seus objetos na pasta 'etbonito' do workspace"
    end)

    fecharButton.MouseButton1Click:Connect(function()
        frame:Destroy()
    end)
end

-- Conectar botão de abrir
abrirButton.MouseButton1Click:Connect(function()
    criarInterface()
end)

print("✅ Exportador Etbonito carregado! Clique no ET para abrir.")
