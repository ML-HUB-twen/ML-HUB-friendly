local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Remove GUI antiga
if playerGui:FindFirstChild("TeleportGui") then
	playerGui:FindFirstChild("TeleportGui"):Destroy()
end

-- Criar ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "TeleportGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

-- Frame da interface
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 90)
frame.Position = UDim2.new(0.5, -110, 0.4, 0)
frame.BackgroundColor3 = Color3.fromRGB(45, 45, 70)
frame.BackgroundTransparency = 0.15
frame.BorderSizePixel = 0
frame.AnchorPoint = Vector2.new(0.5, 0.5)
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui

local corner = Instance.new("UICorner", frame)
corner.CornerRadius = UDim.new(0, 10)

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 25)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.Text = "📍 ML-HUB TELEPORTE"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 17
title.Parent = frame

-- Botão
local button = Instance.new("TextButton")
button.Size = UDim2.new(0.85, 0, 0, 35)
button.Position = UDim2.new(0.075, 0, 0.45, 0)
button.Text = "Teleportar"
button.TextColor3 = Color3.fromRGB(255, 255, 255)
button.BackgroundColor3 = Color3.fromRGB(85, 55, 255)
button.Font = Enum.Font.GothamSemibold
button.TextSize = 16
button.AutoButtonColor = true
button.Parent = frame

local btnCorner = Instance.new("UICorner", button)
btnCorner.CornerRadius = UDim.new(0, 8)

-- Função para pegar posição média dos objetos grandes do mapa (centro)
local function getMapCenter()
	local totalPos = Vector3.new()
	local count = 0

	for _, part in pairs(workspace:GetDescendants()) do
		if part:IsA("BasePart") and part.Anchored and part.Size.Magnitude > 20 then
			totalPos = totalPos + part.Position
			count = count + 1
		end
	end

	if count == 0 then
		return Vector3.new(0, 10, 0)
	end

	return totalPos / count
end

-- Teleporte para frente com proteção extra
local function teleportToFront()
	local character = player.Character or player.CharacterAdded:Wait()
	local humanoid = character:FindFirstChildOfClass("Humanoid")
	local root = character:FindFirstChild("HumanoidRootPart")
	if not root or not humanoid then return end

	-- Bloquear morte e física temporariamente
	humanoid:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
	humanoid.PlatformStand = true

	local center = getMapCenter()
	local safeHeight = 25

	-- Deslocamento para frente no eixo Z (+150 studs)
	local frontPos = Vector3.new(center.X, center.Y + safeHeight, center.Z + 150)

	root.CFrame = CFrame.new(frontPos)

	-- Espera 10 segundos de proteção
	task.wait(10)

	-- Restaurar estado normal
	humanoid.PlatformStand = false
	humanoid:SetStateEnabled(Enum.HumanoidStateType.Dead, true)
end

button.MouseButton1Click:Connect(teleportToFront)
