local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

-- GUI
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "RennxzHub"
gui.ResetOnSpawn = false

-- Menu vertical
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0.25, 0, 0.4, 0)
frame.Position = UDim2.new(0.05, 0, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.Active = true
frame.Draggable = true

-- Título RGB
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0.2, 0)
title.BackgroundTransparency = 1
title.Text = "rennxz hub"
title.Font = Enum.Font.Fantasy
title.TextScaled = true

task.spawn(function()
    while true do
        for i = 0, 255 do
            title.TextColor3 = Color3.fromHSV(i/255,1,1)
            task.wait(0.05)
        end
    end
end)

-- Função para deixar botão 3D
local function make3D(button, color)
    button.BackgroundColor3 = color
    button.Font = Enum.Font.Fantasy
    button.TextScaled = true
    button.TextColor3 = Color3.new(1,1,1)
    local corner = Instance.new("UICorner", button)
    corner.CornerRadius = UDim.new(0.2,0)
    local stroke = Instance.new("UIStroke", button)
    stroke.Thickness = 3
    stroke.Color = Color3.fromRGB(0,0,0)
end

-- Botão Fly
local flyButton = Instance.new("TextButton", frame)
flyButton.Size = UDim2.new(0.8, 0, 0.2, 0)
flyButton.Position = UDim2.new(0.1, 0, 0.25, 0)
flyButton.Text = "Ativar Fly"
make3D(flyButton, Color3.fromRGB(0,170,255))

-- Botão Hover
local hoverButton = Instance.new("TextButton", frame)
hoverButton.Size = UDim2.new(0.8, 0, 0.2, 0)
hoverButton.Position = UDim2.new(0.1, 0, 0.5, 0)
hoverButton.Text = "Parar no Ar"
make3D(hoverButton, Color3.fromRGB(255,140,0))

-- Caixa de texto para velocidade
local speedBox = Instance.new("TextBox", frame)
speedBox.Size = UDim2.new(0.8, 0, 0.15, 0)
speedBox.Position = UDim2.new(0.1, 0, 0.75, 0)
speedBox.Text = "50"
speedBox.Font = Enum.Font.Fantasy
speedBox.TextScaled = true
speedBox.TextColor3 = Color3.new(1,1,1)
speedBox.BackgroundColor3 = Color3.fromRGB(50,50,50)
local cornerBox = Instance.new("UICorner", speedBox)
cornerBox.CornerRadius = UDim.new(0.2,0)

-- Variáveis
local flying = false
local hovering = false
local speed = tonumber(speedBox.Text) or 50
local bodyGyro, bodyVel

-- Função iniciar objetos de voo
local function setupFly()
    local char = player.Character or player.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")

    bodyGyro = Instance.new("BodyGyro", hrp)
    bodyGyro.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
    bodyGyro.P = 9e4

    bodyVel = Instance.new("BodyVelocity", hrp)
    bodyVel.MaxForce = Vector3.new(1e5, 1e5, 1e5)
end

-- Atualiza voo
game:GetService("RunService").RenderStepped:Connect(function()
    if flying and bodyGyro and bodyVel then
        local cam = workspace.CurrentCamera
        bodyGyro.CFrame = cam.CFrame
        if hovering then
            bodyVel.Velocity = Vector3.new(0,0,0)
        else
            bodyVel.Velocity = cam.CFrame.LookVector * speed
        end
    end
end)

-- Botão Fly
flyButton.MouseButton1Click:Connect(function()
    flying = not flying
    if flying then
        flyButton.Text = "Desativar Fly"
        setupFly()
    else
        flyButton.Text = "Ativar Fly"
        if bodyGyro then bodyGyro:Destroy() bodyGyro = nil end
        if bodyVel then bodyVel:Destroy() bodyVel = nil end
    end
end)

-- Botão Hover
hoverButton.MouseButton1Click:Connect(function()
    hovering = not hovering
    if hovering then
        hoverButton.Text = "Hover ON"
    else
        hoverButton.Text = "Parar no Ar"
    end
end)

-- Atualizar velocidade manualmente
speedBox.FocusLost:Connect(function()
    local newSpeed = tonumber(speedBox.Text)
    if newSpeed and newSpeed > 0 then
        speed = newSpeed
    else
        speedBox.Text = tostring(speed)
    end
end)

-- Garantir que funciona após respawn
player.CharacterAdded:Connect(function()
    if flying then
        setupFly()
    end
end)# Rennxzthegoat.
