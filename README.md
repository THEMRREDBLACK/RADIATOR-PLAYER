- radiador de manos
  
local sound = Instance.new("Sound")
sound.SoundId = "rbxassetid://2865227271"
sound.Volume = 3
sound.Parent = game:GetService("SoundService")
sound:Play()

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- Criar GUI
local gui = Instance.new("ScreenGui")
gui.Parent = game.CoreGui
gui.Name = "RADIATOR PLAYER"

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 120)
frame.Position = UDim2.new(0.5, -150, 0.5, -60)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.Active = true
frame.Draggable = true
frame.Parent = gui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 25)
title.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
title.Text = "🔥 RADIATOR PLAYER 🔥"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Parent = frame

local box = Instance.new("TextBox")
box.Size = UDim2.new(1, -20, 0, 30)
box.Position = UDim2.new(0, 10, 0, 40)
box.PlaceholderText = "Digite o nick do alvo..."
box.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
box.TextColor3 = Color3.fromRGB(255, 255, 255)
box.Parent = frame

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(1, -20, 0, 30)
btn.Position = UDim2.new(0, 10, 0, 80)
btn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
btn.Text = "RADIATOR (OFF)"
btn.TextColor3 = Color3.fromRGB(255, 255, 255)
btn.Parent = frame

-- Função para achar player pelo nome parcial/display
local function getPlayer(name)
    name = name:lower()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Name:lower():find(name) or plr.DisplayName:lower():find(name) then
            return plr
        end
    end
    return nil
end

-- Toggle system
local flingConnection
local flingActive = false
local target

local function stopFling()
    if flingConnection then flingConnection:Disconnect() flingConnection = nil end
    flingActive = false
    btn.Text = "RADIATOR (OFF)"
    btn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
end

local function startFling()
    if flingConnection then flingConnection:Disconnect() end
    target = getPlayer(box.Text)
    if not target then
        btn.Text = "Player não encontrado!"
        task.wait(1)
        stopFling()
        return
    end

    flingActive = true
    btn.Text = "RADIATOR (ON)"
    btn.BackgroundColor3 = Color3.fromRGB(0, 200, 0)

    local angle = 0
    flingConnection = RunService.Heartbeat:Connect(function()
        local char = LocalPlayer.Character
        local targetChar = target.Character
        if not flingActive then return end

        if char and targetChar and char:FindFirstChild("HumanoidRootPart") and targetChar:FindFirstChild("HumanoidRootPart") then
            local hrp = char.HumanoidRootPart
            local thrp = targetChar.HumanoidRootPart

            -- checar se alvo está sentado
            local humanoid = targetChar:FindFirstChildOfClass("Humanoid")
            if humanoid and humanoid.Sit then
                hrp.CFrame = CFrame.new(Vector3.new(798, -11, 476)) -- 🔴 teleportar para coordenadas especificadas
                stopFling()
                return
            end

            -- calcular posição ao redor
            angle += 0.5
            local offset = Vector3.new(math.cos(angle) * 5, 0, math.sin(angle) * 5)
            hrp.CFrame = CFrame.new(thrp.Position + offset) * CFrame.Angles(0, math.rad(angle*50), 0)
        end
    end)
end

-- Clique do botão → toggle
btn.MouseButton1Click:Connect(function()
    if flingActive then
        stopFling()
    else
        startFling()
    end
end)
