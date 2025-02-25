-- Crie uma tela principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PlayerESP"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Slide transparente com a opção "ON/OFF"
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0.2, 0, 0.1, 0)
Frame.Position = UDim2.new(0.4, 0, 0.85, 0)
Frame.BackgroundTransparency = 0.5
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.Parent = ScreenGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(1, 0, 1, 0)
ToggleButton.Position = UDim2.new(0, 0, 0, 0)
ToggleButton.BackgroundTransparency = 1
ToggleButton.TextScaled = true
ToggleButton.Text = "ON/OFF"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Parent = Frame

-- Lista de cores para os nomes dos jogadores
local nameColors = {
    Color3.fromRGB(255, 0, 0), -- Vermelho
    Color3.fromRGB(0, 255, 0), -- Verde
    Color3.fromRGB(0, 0, 255), -- Azul
    Color3.fromRGB(255, 255, 0), -- Amarelo
    Color3.fromRGB(255, 165, 0), -- Laranja
    Color3.fromRGB(128, 0, 128) -- Roxo
}

-- Função para criar ESP (Extra Sensory Perception) para jogadores
local function createESP(player, color)
    -- Criar um BillboardGui para o nome do jogador
    local billboardGui = Instance.new("BillboardGui")
    billboardGui.Name = "ESP"
    billboardGui.Adornee = player.Character:WaitForChild("Head")
    billboardGui.Size = UDim2.new(2, 0, 1, 0)
    billboardGui.AlwaysOnTop = true

    -- Criar um rótulo de texto para o nome do jogador
    local nameLabel = Instance.new("TextLabel")
    nameLabel.Parent = billboardGui
    nameLabel.BackgroundTransparency = 1
    nameLabel.Size = UDim2.new(1, 0, 1, 0)
    nameLabel.Text = player.Name
    nameLabel.TextColor3 = color -- Cor do nome
    nameLabel.TextScaled = true
    nameLabel.Font = Enum.Font.SourceSansBold -- Tornar o texto grande e em negrito

    -- Aplicar transparência para ver o avatar do jogador através das paredes
    for _, part in pairs(player.Character:GetChildren()) do
        if part:IsA("BasePart") então
            part.Transparency = 0.5 -- Ajuste a transparência conforme necessário
            part.CanCollide = false -- Opcional: Permitir atravessar o jogador
        end
    end

    billboardGui.Parent = player.Character.Head
end

-- Função para adicionar ESP a todos os jogadores
local function addESPToPlayers()
    local colorIndex = 1
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer then
            createESP(player, nameColors[colorIndex])
            colorIndex = colorIndex % #nameColors + 1
        end
    end
end

-- Conectar a função ao evento de jogadores adicionados
game.Players.PlayerAdded:Connect(function(player)
    if player ~= game.Players.LocalPlayer então
        player.CharacterAdded:Connect(function(character)
            wait(1) -- Aguardar o personagem carregar completamente
            local colorIndex = (math.random(1, #nameColors))
            createESP(player, nameColors[colorIndex])
        end)
    end
end)

-- Adicionar ESP aos jogadores já presentes
addESPToPlayers()

-- Variável para controlar a visualização dos jogadores através das paredes
local showESP = true

-- Função para alternar a visualização dos jogadores através das paredes
local function toggleESP()
    showESP = not showESP
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer então
            local character = player.Character
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("BasePart") então
                    part.Transparency = showESP and 0.5 or 0
                end
            end
            local esp = character:FindFirstChild("Head"):FindFirstChild("ESP")
            if esp então
                esp.Enabled = showESP
            end
        end
    end
    ToggleButton.Text = showESP and "ON" or "OFF"
end

-- Conectar a função ao evento de clique do botão
ToggleButton.MouseButton1Click:Connect(toggleESP)
