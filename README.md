local player = game.Players.LocalPlayer
local uis = game:GetService("UserInputService")
local rs = game:GetService("RunService")

-- Plataforma
local tamanho = Vector3.new(6,1,6)
local cor = Color3.fromRGB(0,200,255)
local distanciaAbaixo = 3
local fatorOlhar = 2

local ativo = false
local plataforma = nil
local screenGui, popUp, botao, botaoDiscord, avisoLabel
local bolinha, contorno

-- Criar GUI inicial
screenGui = Instance.new("ScreenGui")
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Contorno RGB circular
contorno = Instance.new("Frame")
contorno.Size = UDim2.new(0,50,0,50) -- menor
contorno.Position = UDim2.new(0.5,-25,0.5,-25)
contorno.BackgroundColor3 = Color3.fromRGB(255,0,0)
contorno.BorderSizePixel = 0
contorno.ZIndex = 1
contorno.Parent = screenGui
local contornoCorner = Instance.new("UICorner")
contornoCorner.CornerRadius = UDim.new(0.5,0)
contornoCorner.Parent = contorno

-- Bolinha com a foto (ImageButton)
bolinha = Instance.new("ImageButton")
bolinha.Size = UDim2.new(0,40,0,40)
bolinha.Position = UDim2.new(0.5,-20,0.5,-20)
bolinha.BackgroundTransparency = 1
bolinha.Image = rbxassetid://118999922087028
bolinha.ZIndex = 2
bolinha.Parent = screenGui

local bolinhaCorner = Instance.new("UICorner")
bolinhaCorner.CornerRadius = UDim.new(0.5,0)
bolinhaCorner.Parent = bolinha

-- Pop-up RGB invisível
popUp = Instance.new("Frame")
popUp.Size = UDim2.new(0,220,0,200)
popUp.Position = UDim2.new(0.5,-110,0.5,-100)
popUp.Visible = false
popUp.BackgroundColor3 = Color3.fromRGB(255,0,0)
popUp.BorderSizePixel = 0
popUp.Parent = screenGui

-- Função RGB pop-up e contorno
rs.RenderStepped:Connect(function()
    if popUp.Visible then
        local t = tick()
        popUp.BackgroundColor3 = Color3.new(
            (math.sin(t*2)*0.5+0.5),
            (math.sin(t*2 + 2)*0.5+0.5),
            (math.sin(t*2 + 4)*0.5+0.5)
        )
    end
    -- Contorno RGB
    local t2 = tick()
    contorno.BackgroundColor3 = Color3.new(
        (math.sin(t2*2)*0.5+0.5),
        (math.sin(t2*2 + 2)*0.5+0.5),
        (math.sin(t2*2 + 4)*0.5+0.5)
    )
    contorno.Position = bolinha.Position - UDim2.new(0,5,0,5)
end)

-- Função para deixar objetos arrastáveis
local function makeDraggable(frame)
    local dragging, dragInput, dragStart, startPos
    local function update(input)
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging=false end
            end)
        end
    end)
    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    uis.InputChanged:Connect(function(input)
        if input == dragInput and dragging then update(input) end
    end)
end

makeDraggable(bolinha)
makeDraggable(contorno)

-- Mostrar/ocultar GUI ao clicar
bolinha.MouseButton1Click:Connect(function()
    popUp.Visible = not popUp.Visible
end)

-- Adicionando elementos do pop-up
local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1,0,0,30)
titulo.Position = UDim2.new(0,0,0,0)
titulo.BackgroundTransparency = 1
titulo.Text = "Script by Gustavo"
titulo.TextColor3 = Color3.new(1,1,1)
titulo.Font = Enum.Font.SourceSansBold
titulo.TextScaled = true
titulo.Parent = popUp

botao = Instance.new("TextButton")
botao.Size = UDim2.new(0,180,0,50)
botao.Position = UDim2.new(0.5,-90,0,40)
botao.BackgroundColor3 = Color3.fromRGB(0,170,255)
botao.TextColor3 = Color3.fromRGB(255,255,255)
botao.Text = "Ativar Plataforma"
botao.Parent = popUp

local labelDiscord = Instance.new("TextLabel")
labelDiscord.Size = UDim2.new(1,0,0,20)
labelDiscord.Position = UDim2.new(0,0,0,100)
labelDiscord.BackgroundTransparency = 1
labelDiscord.Text = "Entre no nosso Discord:"
labelDiscord.TextColor3 = Color3.new(1,1,1)
labelDiscord.Font = Enum.Font.SourceSans
labelDiscord.TextScaled = true
labelDiscord.Parent = popUp

botaoDiscord = Instance.new("TextButton")
botaoDiscord.Size = UDim2.new(0,150,0,40)
botaoDiscord.Position = UDim2.new(0.5,-75,0,125)
botaoDiscord.BackgroundColor3 = Color3.fromRGB(0,200,100)
botaoDiscord.TextColor3 = Color3.fromRGB(255,255,255)
botaoDiscord.Text = "Copiar link"
botaoDiscord.Parent = popUp

avisoLabel = Instance.new("TextLabel")
avisoLabel.Size = UDim2.new(1,0,0,25)
avisoLabel.Position = UDim2.new(0,0,0,170)
avisoLabel.BackgroundTransparency = 1
avisoLabel.TextColor3 = Color3.new(1,1,1)
avisoLabel.Font = Enum.Font.SourceSansBold
avisoLabel.TextScaled = true
avisoLabel.Text = ""
avisoLabel.Parent = popUp

-- Funções dos botões
botao.MouseButton1Click:Connect(function()
    ativo = not ativo
    if ativo then
        botao.Text = "Desativar Plataforma"
        botao.BackgroundColor3 = Color3.fromRGB(0,200,100)
        if plataforma then plataforma:Destroy() end
        plataforma = Instance.new("Part")
        plataforma.Size = tamanho
        plataforma.Anchored = true
        plataforma.Color = cor
        plataforma.Name = "PlataformaSeguidora"
        plataforma.Parent = workspace
    else
        botao.Text = "Ativar Plataforma"
        botao.BackgroundColor3 = Color3.fromRGB(0,170,255)
        if plataforma then
            plataforma:Destroy()
            plataforma = nil
        end
    end
end)

botaoDiscord.MouseButton1Click:Connect(function()
    if setclipboard then
        setclipboard("https://discord.gg/APHQGSNTWF")
    end
    avisoLabel.Text = "Link Copiado! Abra no navegador ou cole no Discord!"
    task.delay(3,function()
        avisoLabel.Text = ""
    end)
end)

-- Atualizar plataforma
rs.RenderStepped:Connect(function()
    if ativo and plataforma and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local root = player.Character.HumanoidRootPart
        local lookVector = root.CFrame.LookVector
        local offsetY = distanciaAbaixo + plataforma.Size.Y/2 - lookVector.Y*fatorOlhar
        plataforma.Position = root.Position - Vector3.new(0, offsetY,0)
    end
end)
