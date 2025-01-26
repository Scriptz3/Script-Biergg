-- Script do personagem "Bierg"

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

-- Função para configurar a aparência do personagem
local function setupAppearance()
    -- Remover acessórios existentes
    for _, accessory in pairs(character:GetChildren()) do
        if accessory:IsA("Accessory") or accessory:IsA("Clothing") then
            accessory:Destroy()
        end
    end

    -- Tornar o personagem todo preto
    for _, part in pairs(character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.BrickColor = BrickColor.new("Black")
            part.Material = Enum.Material.SmoothPlastic
        end
    end

    -- Adicionar o capuz
    local hood = Instance.new("Accessory")
    hood.Name = "BlackHood"
    local handle = Instance.new("Part")
    handle.Name = "Handle"
    handle.Size = Vector3.new(2, 2, 2)
    handle.BrickColor = BrickColor.new("Black")
    handle.Material = Enum.Material.SmoothPlastic
    handle.CanCollide = false
    handle.Parent = hood
    hood.Parent = character
    character.Humanoid:AddAccessory(hood)
end

-- Função para criar a bola preta que expande
local function createBlackBall()
    local ball = Instance.new("Part")
    ball.Size = Vector3.new(1, 1, 1)
    ball.Shape = Enum.PartType.Ball
    ball.BrickColor = BrickColor.new("Black")
    ball.Material = Enum.Material.Neon
    ball.Anchored = true
    ball.CanCollide = false
    ball.CFrame = character.HumanoidRootPart.CFrame
    ball.Parent = workspace

    -- Expansão da bola
    local size = 1
    local expansionRate = 5
    local expansionDuration = 5 -- Segundos

    local startTime = tick()
    local connection
    connection = game:GetService("RunService").Heartbeat:Connect(function()
        local elapsed = tick() - startTime
        if elapsed > expansionDuration then
            connection:Disconnect()
            ball:Destroy()
            return
        end

        size = size + expansionRate * game:GetService("RunService").Heartbeat:Wait()
        ball.Size = Vector3.new(size, size, size)
        ball.CFrame = character.HumanoidRootPart.CFrame

        -- Matar jogadores dentro do raio
        for _, otherPlayer in pairs(game.Players:GetPlayers()) do
            if otherPlayer ~= player and otherPlayer.Character and otherPlayer.Character:FindFirstChild("Humanoid") then
                local distance = (otherPlayer.Character.HumanoidRootPart.Position - ball.Position).Magnitude
                if distance <= size / 2 then
                    otherPlayer.Character.Humanoid.Health = 0
                end
            end
        end
    end)
end

-- Configurar aparência ao carregar o script
setupAppearance()

-- Detectar a tecla "E" para ativar a habilidade
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, isProcessed)
    if isProcessed then return end
    if input.KeyCode == Enum.KeyCode.E then
        createBlackBall()
    end
end)
