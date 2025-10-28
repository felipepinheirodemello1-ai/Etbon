local Players = game:GetService("Players")
local player = Players.LocalPlayer
local gui = Instance.new("ScreenGui")
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- Sistema de Chave
local CHAVE_CORRETA = "ETBONITO2025"
local URL_SITE = "https://direct-link.net/1403806/XasEMxJac6Yp"
local acessoPermitido = false

-- Botão principal
local abrirBtn = Instance.new("ImageButton")
abrirBtn.Size = UDim2.new(0, 50, 0, 50)
abrirBtn.Position = UDim2.new(0, 20, 0, 20)
abrirBtn.Image = "rbxassetid://81739893945719"
abrirBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
abrirBtn.Visible = false
abrirBtn.Parent = gui

local abrirCorner = Instance.new("UICorner")
abrirCorner.CornerRadius = UDim.new(0, 10)
abrirCorner.Parent = abrirBtn

-- Tela de chave
local function criarTelaChave()
    if gui:FindFirstChild("TelaChave") then
        gui.TelaChave:Destroy()
    end
    
    local telaChave = Instance.new("Frame")
    telaChave.Size = UDim2.new(0, 320, 0, 280)
    telaChave.Position = UDim2.new(0.5, -160, 0.5, -140)
    telaChave.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    telaChave.Parent = gui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = telaChave

    -- Header
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 40)
    header.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    header.Parent = telaChave

    local titulo = Instance.new("TextLabel")
    titulo.Size = UDim2.new(1, -40, 1, 0)
    titulo.Text = "ACESSO RESTRITO"
    titulo.Font = Enum.Font.GothamBold
    titulo.TextSize = 14
    titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
    titulo.BackgroundTransparency = 1
    titulo.Parent = header

    -- Botão fechar
    local fecharBtn = Instance.new("TextButton")
    fecharBtn.Size = UDim2.new(0, 30, 0, 30)
    fecharBtn.Position = UDim2.new(1, -35, 0, 5)
    fecharBtn.Text = "X"
    fecharBtn.Font = Enum.Font.GothamBold
    fecharBtn.TextSize = 14
    fecharBtn.BackgroundColor3 = Color3.fromRGB(220, 60, 60)
    fecharBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    fecharBtn.Parent = header

    local fecharCorner = Instance.new("UICorner")
    fecharCorner.CornerRadius = UDim.new(0, 6)
    fecharCorner.Parent = fecharBtn

    -- Conteúdo
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -20, 1, -60)
    contentFrame.Position = UDim2.new(0, 10, 0, 50)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = telaChave

    -- Instruções
    local instrucoes = Instance.new("TextLabel")
    instrucoes.Size = UDim2.new(1, 0, 0, 80)
    instrucoes.Position = UDim2.new(0, 0, 0, 0)
    instrucoes.Text = "COMO OBTER ACESSO:\n\n1. Clique em ACESSAR SITE\n2. Siga as instruções do site\n3. Cole a chave abaixo"
    instrucoes.Font = Enum.Font.Gotham
    instrucoes.TextSize = 12
    instrucoes.TextColor3 = Color3.fromRGB(200, 200, 255)
    instrucoes.BackgroundTransparency = 1
    instrucoes.TextXAlignment = Enum.TextXAlignment.Left
    instrucoes.TextYAlignment = Enum.TextYAlignment.Top
    instrucoes.Parent = contentFrame

    -- Botão do site
    local btnSite = Instance.new("TextButton")
    btnSite.Size = UDim2.new(1, 0, 0, 30)
    btnSite.Position = UDim2.new(0, 0, 0, 85)
    btnSite.Text = "ACESSAR SITE PARA PEGAR CHAVE"
    btnSite.Font = Enum.Font.GothamBold
    btnSite.TextSize = 11
    btnSite.BackgroundColor3 = Color3.fromRGB(100, 180, 100)
    btnSite.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnSite.Parent = contentFrame

    local btnSiteCorner = Instance.new("UICorner")
    btnSiteCorner.CornerRadius = UDim.new(0, 6)
    btnSiteCorner.Parent = btnSite

    -- Input da chave
    local inputChave = Instance.new("TextBox")
    inputChave.Size = UDim2.new(1, 0, 0, 35)
    inputChave.Position = UDim2.new(0, 0, 0, 125)
    inputChave.PlaceholderText = "Cole a chave aqui..."
    inputChave.Font = Enum.Font.Gotham
    inputChave.TextSize = 12
    inputChave.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
    inputChave.TextColor3 = Color3.fromRGB(255, 255, 255)
    inputChave.Parent = contentFrame

    local inputCorner = Instance.new("UICorner")
    inputCorner.CornerRadius = UDim.new(0, 6)
    inputCorner.Parent = inputChave

    -- Botão verificar
    local btnVerificar = Instance.new("TextButton")
    btnVerificar.Size = UDim2.new(1, 0, 0, 35)
    btnVerificar.Position = UDim2.new(0, 0, 0, 170)
    btnVerificar.Text = "VERIFICAR CHAVE"
    btnVerificar.Font = Enum.Font.GothamBold
    btnVerificar.TextSize = 12
    btnVerificar.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
    btnVerificar.TextColor3 = Color3.fromRGB(255, 255, 255)
    btnVerificar.Parent = contentFrame

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 6)
    btnCorner.Parent = btnVerificar

    -- Abrir site
    btnSite.MouseButton1Click:Connect(function()
        if setclipboard then
            setclipboard(URL_SITE)
        end
        btnSite.Text = "LINK COPIADO! Cole no navegador"
        wait(2)
        btnSite.Text = "ACESSAR SITE PARA PEGAR CHAVE"
    end)

    -- Verificar chave
    btnVerificar.MouseButton1Click:Connect(function()
        local chaveDigitada = inputChave.Text:upper():gsub("%s+", "")
        
        if chaveDigitada == CHAVE_CORRETA then
            acessoPermitido = true
            telaChave:Destroy()
            criarInterface()
        else
            inputChave.Text = ""
            inputChave.PlaceholderText = "Chave incorreta!"
            inputChave.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
            wait(1.5)
            inputChave.PlaceholderText = "Cole a chave aqui..."
            inputChave.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
        end
    end)

    -- Fechar tela
    fecharBtn.MouseButton1Click:Connect(function()
        telaChave:Destroy()
    end)
end

-- Funções principais
local function criarPastaEtbonito()
    local pasta = workspace:FindFirstChild("etbonito")
    if not pasta then
        pasta = Instance.new("Folder")
        pasta.Name = "etbonito"
        pasta.Parent = workspace
        return "PASTA ETBONITO CRIADA! Agora coloque objetos dentro dela."
    end
    return "PASTA ETBONITO JA EXISTE! Coloque objetos dentro dela."
end

local function exportarTudo()
    local pasta = workspace:FindFirstChild("etbonito")
    if not pasta then
        return "CRIE A PASTA ETBONITO PRIMEIRO! Clique em CRIAR PASTA."
    end
    if #pasta:GetChildren() == 0 then
        return "PASTA ETBONITO ESTA VAZIA! Adicione objetos dentro dela."
    end
    
    local codigo = "-- ETBONITO EXPORT\n"
    codigo = codigo .. "-- Gerado em: " .. os.date("%d/%m/%Y %H:%M") .. "\n\n"
    codigo = codigo .. "local etbonito = Instance.new(\"Folder\")\n"
    codigo = codigo .. "etbonito.Name = \"etbonito\"\n"
    codigo = codigo .. "etbonito.Parent = workspace\n\n"
    
    for _, obj in ipairs(pasta:GetChildren()) do
        if obj:IsA("Part") then
            local nomeVar = obj.Name:gsub("[^%w]", "_")
            codigo = codigo .. string.format('local %s = Instance.new("Part")\n', nomeVar)
            codigo = codigo .. string.format('%s.Name = "%s"\n', nomeVar, obj.Name)
            codigo = codigo .. string.format('%s.Size = Vector3.new(%s, %s, %s)\n', nomeVar, obj.Size.X, obj.Size.Y, obj.Size.Z)
            codigo = codigo .. string.format('%s.Position = Vector3.new(%s, %s, %s)\n', nomeVar, obj.Position.X, obj.Position.Y, obj.Position.Z)
            codigo = codigo .. string.format('%s.Parent = etbonito\n\n', nomeVar)
        end
    end
    
    codigo = codigo .. "print('EXPORTACAO CONCLUIDA!')\n"
    return codigo
end

-- Interface principal
local function criarInterface()
    if not acessoPermitido then
        criarTelaChave()
        return
    end
    
    if gui:FindFirstChild("PainelPrincipal") then
        gui.PainelPrincipal:Destroy()
    end
    
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 350, 0, 300)
    frame.Position = UDim2.new(0.5, -175, 0.5, -150)
    frame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    frame.Parent = gui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = frame

    -- Header
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 40)
    header.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    header.Parent = frame

    local titulo = Instance.new("TextLabel")
    titulo.Size = UDim2.new(1, -40, 1, 0)
    titulo.Text = "ETBONITO EXPORT"
    titulo.Font = Enum.Font.GothamBold
    titulo.TextSize = 14
    titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
    titulo.BackgroundTransparency = 1
    titulo.Parent = header

    -- Botão fechar
    local fecharBtn = Instance.new("TextButton")
    fecharBtn.Size = UDim2.new(0, 30, 0, 30)
    fecharBtn.Position = UDim2.new(1, -35, 0, 5)
    fecharBtn.Text = "X"
    fecharBtn.Font = Enum.Font.GothamBold
    fecharBtn.TextSize = 14
    fecharBtn.BackgroundColor3 = Color3.fromRGB(220, 60, 60)
    fecharBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    fecharBtn.Parent = header

    local fecharCorner = Instance.new("UICorner")
    fecharCorner.CornerRadius = UDim.new(0, 6)
    fecharCorner.Parent = fecharBtn

    -- Conteúdo
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -20, 1, -60)
    contentFrame.Position = UDim2.new(0, 10, 0, 50)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = frame

    -- Caixa de texto
    local textbox = Instance.new("TextBox")
    textbox.Size = UDim2.new(1, 0, 0, 150)
    textbox.Position = UDim2.new(0, 0, 0, 0)
    textbox.TextWrapped = true
    textbox.Text = "COMO USAR:\n\n1. Clique em CRIAR PASTA\n2. Coloque objetos na pasta etbonito\n3. Clique em EXPORTAR TUDO\n4. Codigo e copiado automaticamente\n\nSCRIPTS EM MANUTENCAO"
    textbox.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    textbox.TextColor3 = Color3.fromRGB(255, 255, 255)
    textbox.Parent = contentFrame

    local textboxCorner = Instance.new("UICorner")
    textboxCorner.CornerRadius = UDim.new(0, 6)
    textboxCorner.Parent = textbox

    -- Botões
    local criarBtn = Instance.new("TextButton")
    criarBtn.Size = UDim2.new(0.48, -5, 0, 35)
    criarBtn.Position = UDim2.new(0, 0, 0, 160)
    criarBtn.Text = "CRIAR PASTA"
    criarBtn.Font = Enum.Font.GothamBold
    criarBtn.TextSize = 12
    criarBtn.BackgroundColor3 = Color3.fromRGB(255, 120, 0)
    criarBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    criarBtn.Parent = contentFrame

    local exportBtn = Instance.new("TextButton")
    exportBtn.Size = UDim2.new(0.48, 0, 0, 35)
    exportBtn.Position = UDim2.new(0.52, 0, 0, 160)
    exportBtn.Text = "EXPORTAR TUDO"
    exportBtn.Font = Enum.Font.GothamBold
    exportBtn.TextSize = 12
    exportBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
    exportBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    exportBtn.Parent = contentFrame

    -- Botão Como Usar
    local comoUsarBtn = Instance.new("TextButton")
    comoUsarBtn.Size = UDim2.new(1, 0, 0, 35)
    comoUsarBtn.Position = UDim2.new(0, 0, 0, 205)
    comoUsarBtn.Text = "COMO USAR"
    comoUsarBtn.Font = Enum.Font.GothamBold
    comoUsarBtn.TextSize = 12
    comoUsarBtn.BackgroundColor3 = Color3.fromRGB(100, 180, 100)
    comoUsarBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    comoUsarBtn.Parent = contentFrame

    local criarCorner = Instance.new("UICorner")
    criarCorner.CornerRadius = UDim.new(0, 6)
    criarCorner.Parent = criarBtn

    local exportCorner = Instance.new("UICorner")
    exportCorner.CornerRadius = UDim.new(0, 6)
    exportCorner.Parent = exportBtn

    local comoUsarCorner = Instance.new("UICorner")
    comoUsarCorner.CornerRadius = UDim.new(0, 6)
    comoUsarCorner.Parent = comoUsarBtn

    -- Ações
    criarBtn.MouseButton1Click:Connect(function()
        local mensagem = criarPastaEtbonito()
        textbox.Text = mensagem
    end)

    exportBtn.MouseButton1Click:Connect(function()
        local codigo = exportarTudo()
        textbox.Text = codigo
        if setclipboard then
            setclipboard(codigo)
        end
    end)

    comoUsarBtn.MouseButton1Click:Connect(function()
        textbox.Text = "TUTORIAL COMPLETO:\n\n" ..
                       "1. CRIACAO DA PASTA:\n" ..
                       "Clique em CRIAR PASTA\n" ..
                       "Uma pasta etbonito sera criada\n\n" ..
                       "2. ADICIONAR OBJETOS:\n" ..
                       "Coloque Parts, Models na pasta\n" ..
                       "Use o Explorer do Roblox\n\n" ..
                       "3. EXPORTAR:\n" ..
                       "Clique em EXPORTAR TUDO\n" ..
                       "Codigo e gerado automaticamente\n" ..
                       "Copiado para area de transferencia\n\n" ..
                       "LIMITACOES:\n" ..
                       "Scripts em manutencao\n" ..
                       "Apenas objetos visuais"
    end)

    -- Fechar interface
    fecharBtn.MouseButton1Click:Connect(function()
        frame:Destroy()
    end)
end

-- Conectar botão principal
abrirBtn.MouseButton1Click:Connect(function()
    if acessoPermitido then
        criarInterface()
    else
        criarTelaChave()
    end
end)

-- Iniciar
criarTelaChave()

-- Mostrar botão após acesso
spawn(function()
    while true do
        if acessoPermitido and not abrirBtn.Visible then
            abrirBtn.Visible = true
        end
        wait(0.5)
    end
end)

print("SISTEMA CARREGADO")
print("Site: " .. URL_SITE)
print("ETBONITO EXPORT PRONTO")
