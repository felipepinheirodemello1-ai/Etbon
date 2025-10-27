local Players = game:GetService("Players")
local player = Players.LocalPlayer
local gui = Instance.new("ScreenGui")
gui.Name = "ExportadorEtbonito"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- Botão principal
local abrirBtn = Instance.new("ImageButton")
abrirBtn.Name = "AbrirEtbonito"
abrirBtn.Size = UDim2.new(0, 50, 0, 50)
abrirBtn.Position = UDim2.new(0, 20, 0, 20)
abrirBtn.Image = "rbxassetid://81739893945719"
abrirBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
abrirBtn.Parent = gui

-- Estilização do botão
local abrirCorner = Instance.new("UICorner")
abrirCorner.CornerRadius = UDim.new(0, 10)
abrirCorner.Parent = abrirBtn

local abrirStroke = Instance.new("UIStroke")
abrirStroke.Color = Color3.fromRGB(255, 255, 255)
abrirStroke.Thickness = 1.5
abrirStroke.Parent = abrirBtn

-- Efeito hover
abrirBtn.MouseEnter:Connect(function()
    abrirBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 255)
end)
abrirBtn.MouseLeave:Connect(function()
    abrirBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
end)

-- Funções principais
local function criarPastaEtbonito()
    local pasta = workspace:FindFirstChild("etbonito")
    if not pasta then
        pasta = Instance.new("Folder")
        pasta.Name = "etbonito"
        pasta.Parent = workspace
        return true, "✅ PASTA CRIADA"
    end
    return false, "📁 PASTA JA EXISTE"
end

local function escaparString(texto)
    if not texto then return "" end
    return texto:gsub("\\", "\\\\"):gsub('"', '\\"'):gsub("\n", "\\n"):gsub("\t", "\\t")
end

local function exportarObjeto(obj, nivel, parentName)
    local indentacao = string.rep("    ", nivel)
    local codigo = ""
    local nomeVar = obj.Name:gsub("[^%w]", "_")
    
    if obj:IsA("Folder") then
        codigo = codigo .. indentacao .. string.format('local %s = Instance.new("Folder")', nomeVar)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Name = "%s"', nomeVar, escaparString(obj.Name))
        for _, filho in ipairs(obj:GetChildren()) do
            codigo = codigo .. "\n\n" .. exportarObjeto(filho, nivel, nomeVar)
        end
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Parent = %s', nomeVar, parentName or "workspace")
    elseif obj:IsA("Part") then
        codigo = codigo .. indentacao .. string.format('local %s = Instance.new("Part")', nomeVar)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Name = "%s"', nomeVar, escaparString(obj.Name))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Size = Vector3.new(%s, %s, %s)', nomeVar, obj.Size.X, obj.Size.Y, obj.Size.Z)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Position = Vector3.new(%s, %s, %s)', nomeVar, obj.Position.X, obj.Position.Y, obj.Position.Z)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Color = Color3.fromRGB(%d, %d, %d)', nomeVar, math.floor(obj.Color.R * 255), math.floor(obj.Color.G * 255), math.floor(obj.Color.B * 255))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Material = Enum.Material.%s', nomeVar, obj.Material.Name)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Anchored = %s', nomeVar, obj.Anchored)
        for _, filho in ipairs(obj:GetChildren()) do
            codigo = codigo .. "\n\n" .. exportarObjeto(filho, nivel + 1, nomeVar)
        end
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Parent = %s', nomeVar, parentName or "workspace")
    elseif obj:IsA("Model") then
        codigo = codigo .. indentacao .. string.format('local %s = Instance.new("Model")', nomeVar)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Name = "%s"', nomeVar, escaparString(obj.Name))
        for _, filho in ipairs(obj:GetChildren()) do
            codigo = codigo .. "\n\n" .. exportarObjeto(filho, nivel + 1, nomeVar)
        end
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Parent = %s', nomeVar, parentName or "workspace")
    else
        codigo = codigo .. indentacao .. string.format('local %s = Instance.new("%s")', nomeVar, obj.ClassName)
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Name = "%s"', nomeVar, escaparString(obj.Name))
        codigo = codigo .. "\n" .. indentacao .. string.format('%s.Parent = %s', nomeVar, parentName or "workspace")
    end
    return codigo
end

local function exportarTudo()
    local pasta = workspace:FindFirstChild("etbonito")
    if not pasta then
        return "❌ CRIE PASTA 'ETBONITO'"
    end
    if #pasta:GetChildren() == 0 then
        return "📁 PASTA VAZIA"
    end
    
    local codigo = "🔥 ETBONITO EXPORT\n"
    codigo = codigo .. "📅 " .. os.date("%d/%m/%Y %H:%M") .. "\n"
    codigo = codigo .. "local etbonito = Instance.new(\"Folder\")\n"
    codigo = codigo .. "etbonito.Name = \"etbonito\"\n"
    codigo = codigo .. "etbonito.Parent = workspace\n\n"
    
    for _, obj in ipairs(pasta:GetChildren()) do
        if not obj:IsA("Script") and not obj:IsA("LocalScript") then
            codigo = codigo .. exportarObjeto(obj, 0, "etbonito") .. "\n\n"
        end
    end
    codigo = codigo .. "print('✅ EXPORT CONCLUIDO')\n"
    return codigo
end

-- Interface principal
local function criarInterface()
    if gui:FindFirstChild("PainelPrincipal") then
        gui.PainelPrincipal:Destroy()
    end
    
    local frame = Instance.new("Frame")
    frame.Name = "PainelPrincipal"
    frame.Size = UDim2.new(0, 350, 0, 300)
    frame.Position = UDim2.new(0.5, -175, 0.3, -100)
    frame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    frame.BorderSizePixel = 0
    frame.Parent = gui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = frame

    local stroke = Instance.new("UIStroke")
    stroke.Color = Color3.fromRGB(80, 80, 120)
    stroke.Thickness = 2
    stroke.Parent = frame

    -- Header
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 50)
    header.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    header.BorderSizePixel = 0
    header.Parent = frame

    local headerCorner = Instance.new("UICorner")
    headerCorner.CornerRadius = UDim.new(0, 10)
    headerCorner.Parent = header

    -- Logo e títulos
    local logo = Instance.new("ImageLabel")
    logo.Size = UDim2.new(0, 35, 0, 35)
    logo.Position = UDim2.new(0, 8, 0, 7)
    logo.Image = "rbxassetid://81739893945719"
    logo.BackgroundTransparency = 1
    logo.Parent = header

    local titulo = Instance.new("TextLabel")
    titulo.Size = UDim2.new(1, -50, 1, 0)
    titulo.Position = UDim2.new(0, 50, 0, 0)
    titulo.Text = "ETBONITO EXPORT"
    titulo.Font = Enum.Font.GothamBold
    titulo.TextSize = 14
    titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
    titulo.BackgroundTransparency = 1
    titulo.TextXAlignment = Enum.TextXAlignment.Left
    titulo.Parent = header

    -- Área de conteúdo
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -10, 1, -60)
    contentFrame.Position = UDim2.new(0, 5, 0, 55)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = frame

    -- Caixa de texto
    local textboxContainer = Instance.new("Frame")
    textboxContainer.Size = UDim2.new(1, 0, 0, 150)
    textboxContainer.Position = UDim2.new(0, 0, 0, 0)
    textboxContainer.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    textboxContainer.BorderSizePixel = 0
    textboxContainer.Parent = contentFrame

    local textboxCorner = Instance.new("UICorner")
    textboxCorner.CornerRadius = UDim.new(0, 8)
    textboxCorner.Parent = textboxContainer

    local textbox = Instance.new("TextBox")
    textbox.Name = "ExportBox"
    textbox.Size = UDim2.new(1, -10, 1, -10)
    textbox.Position = UDim2.new(0, 5, 0, 5)
    textbox.TextWrapped = true
    textbox.TextXAlignment = Enum.TextXAlignment.Left
    textbox.TextYAlignment = Enum.TextYAlignment.Top
    textbox.MultiLine = true
    textbox.Font = Enum.Font.Gotham
    textbox.TextSize = 10
    textbox.Text = "📁 SPLASH\n• Voltar para Crescendo\n• Rotar\n• Collarem\n\n📜 SCRIPTS SERVIDOR\n• Tagé\n• ck\n\n⚡ INFINITE YIELD\n• ETIBONISO EXPORT\n• Interface levantado\n• Scripts em manutencao"
    textbox.BackgroundTransparency = 1
    textbox.TextColor3 = Color3.fromRGB(240, 240, 255)
    textbox.Parent = textboxContainer

    -- Botões
    local buttonGrid = Instance.new("Frame")
    buttonGrid.Size = UDim2.new(1, 0, 0, 80)
    buttonGrid.Position = UDim2.new(0, 0, 0, 160)
    buttonGrid.BackgroundTransparency = 1
    buttonGrid.Parent = contentFrame

    local function criarBotao(nome, texto, cor, posicao, tamanho)
        local botao = Instance.new("TextButton")
        botao.Name = nome
        botao.Size = tamanho
        botao.Position = posicao
        botao.Text = texto
        botao.Font = Enum.Font.GothamBold
        botao.TextSize = 11
        botao.BackgroundColor3 = cor
        botao.TextColor3 = Color3.fromRGB(255, 255, 255)
        botao.BorderSizePixel = 0
        botao.AutoButtonColor = true
        botao.Parent = buttonGrid

        local botaoCorner = Instance.new("UICorner")
        botaoCorner.CornerRadius = UDim.new(0, 6)
        botaoCorner.Parent = botao

        botao.MouseEnter:Connect(function()
            botao.BackgroundColor3 = Color3.new(cor.R + 0.1, cor.G + 0.1, cor.B + 0.1)
        end)
        botao.MouseLeave:Connect(function()
            botao.BackgroundColor3 = cor
        end)

        return botao
    end

    -- Botões compactos - 4 botões em 2 linhas
    local criarPastaBtn = criarBotao("CriarPasta", "📁 CRIAR", Color3.fromRGB(255, 120, 0), 
        UDim2.new(0, 0, 0, 0), UDim2.new(0.48, -2, 0, 35))

    local exportTudoBtn = criarBotao("ExportTudo", "🚀 EXPORTAR", Color3.fromRGB(0, 150, 255), 
        UDim2.new(0.52, 0, 0, 0), UDim2.new(0.48, 0, 0, 35))

    -- NOVO BOTÃO: COMO USAR
    local comoUsarBtn = criarBotao("ComoUsar", "❓ COMO USAR", Color3.fromRGB(100, 180, 100), 
        UDim2.new(0, 0, 0, 40), UDim2.new(0.48, -2, 0, 35))

    -- Botão scripts (desativado)
    local scriptsBtn = criarBotao("ScriptsBtn", "📜 MANUTENCAO", Color3.fromRGB(80, 80, 80), 
        UDim2.new(0.52, 0, 0, 40), UDim2.new(0.48, 0, 0, 35))
    scriptsBtn.AutoButtonColor = false
    scriptsBtn.TextColor3 = Color3.fromRGB(180, 180, 180)

    -- Botão fechar
    local fecharBtn = Instance.new("TextButton")
    fecharBtn.Size = UDim2.new(0, 25, 0, 25)
    fecharBtn.Position = UDim2.new(1, -30, 0, 12)
    fecharBtn.Text = "✕"
    fecharBtn.Font = Enum.Font.GothamBold
    fecharBtn.TextSize = 12
    fecharBtn.BackgroundColor3 = Color3.fromRGB(220, 60, 60)
    fecharBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    fecharBtn.BorderSizePixel = 0
    fecharBtn.AutoButtonColor = true
    fecharBtn.Parent = header

    local fecharCorner = Instance.new("UICorner")
    fecharCorner.CornerRadius = UDim.new(0, 6)
    fecharCorner.Parent = fecharBtn

    -- Conexões dos botões
    criarPastaBtn.MouseButton1Click:Connect(function()
        local sucesso, mensagem = criarPastaEtbonito()
        textbox.Text = mensagem
    end)

    exportTudoBtn.MouseButton1Click:Connect(function()
        local codigo = exportarTudo()
        textbox.Text = codigo
        if setclipboard then setclipboard(codigo) end
    end)

    -- NOVA FUNÇÃO: COMO USAR
    comoUsarBtn.MouseButton1Click:Connect(function()
        textbox.Text = "🎯 COMO USAR O ETBONITO EXPORT:\n\n" ..
                       "1️⃣ CLIQUE EM 'CRIAR'\n" ..
                       "   • Cria pasta 'etbonito' no Workspace\n\n" ..
                       "2️⃣ COLOQUE OBJETOS\n" ..
                       "   • Parts, Models, Folders\n" ..
                       "   • Na pasta 'etbonito'\n\n" ..
                       "3️⃣ CLIQUE EM 'EXPORTAR'\n" ..
                       "   • Gera código automaticamente\n" ..
                       "   • Copia para área de transferência\n\n" ..
                       "⚠️  SCRIPTS EM MANUTENCAO\n" ..
                       "• Apenas objetos visuais por enquanto"
    end)

    scriptsBtn.MouseButton1Click:Connect(function()
        textbox.Text = "🚧 SCRIPTS EM MANUTENCAO\n\n📁 SPLASH\n• Voltar para Crescendo\n• Rotar\n• Collarem\n• Contar/Appar Grupo\n• Selecionar\n\n📜 SCRIPTS SERVIDOR\n• Tagé\n• ck\n\n⚡ INFINITE YIELD\n• ETIBONISO EXPORT\n• Interface levantado\n• Scripts em manutencao"
    end)

    fecharBtn.MouseButton1Click:Connect(function()
        frame:Destroy()
    end)

    -- Sistema de arrastar
    local dragging, dragInput, dragStart, startPos = false
    header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    header.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- Conectar botão principal
abrirBtn.MouseButton1Click:Connect(criarInterface)

print("📁 ETBONITO EXPORT CARREGADO")
print("⚡ Interface compacta")
print("❓ Botão 'Como Usar' adicionado")
print("🚧 Scripts em manutencao")    titulo.BackgroundTransparency = 1
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
