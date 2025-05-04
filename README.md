-- LockInForAll - Atualizado para R6 e travamento fixo
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local camera = workspace.CurrentCamera
local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Interface
local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
screenGui.Name = "LockInForAll"
screenGui.ResetOnSpawn = false

local dragFrame = Instance.new("Frame", screenGui)
dragFrame.Size = UDim2.new(0, 40, 0, 40)
dragFrame.Position = UDim2.new(0, 20, 0, 100)
dragFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
dragFrame.BackgroundTransparency = 0.2
dragFrame.BorderSizePixel = 0
dragFrame.Active = true
dragFrame.Draggable = true
dragFrame.AnchorPoint = Vector2.new(0.5, 0.5)
dragFrame.ClipsDescendants = true
dragFrame.ZIndex = 2

local toggleButton = Instance.new("TextButton", dragFrame)
toggleButton.Size = UDim2.new(1, 0, 1, 0)
toggleButton.Text = "●"
toggleButton.TextScaled = true
toggleButton.BackgroundTransparency = 1
toggleButton.TextColor3 = Color3.fromRGB(0, 255, 0)
toggleButton.ZIndex = 3

local menu = Instance.new("Frame", screenGui)
menu.Size = UDim2.new(0, 150, 0, 60)
menu.Position = dragFrame.Position + UDim2.new(0, 50, 0, 0)
menu.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
menu.Visible = false
menu.Active = true
menu.Draggable = true
menu.BorderSizePixel = 0
menu.ZIndex = 2

local uiCorner = Instance.new("UICorner", menu)
uiCorner.CornerRadius = UDim.new(0, 8)

local toggleLabel = Instance.new("TextLabel", menu)
toggleLabel.Size = UDim2.new(0.6, 0, 1, 0)
toggleLabel.Position = UDim2.new(0, 5, 0, 0)
toggleLabel.BackgroundTransparency = 1
toggleLabel.Text = "Lock-In:"
toggleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleLabel.TextScaled = true
toggleLabel.ZIndex = 3

local toggleSwitch = Instance.new("TextButton", menu)
toggleSwitch.Size = UDim2.new(0.3, 0, 0.6, 0)
toggleSwitch.Position = UDim2.new(0.65, 0, 0.2, 0)
toggleSwitch.Text = "OFF"
toggleSwitch.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
toggleSwitch.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleSwitch.TextScaled = true
toggleSwitch.ZIndex = 3

local switchCorner = Instance.new("UICorner", toggleSwitch)
switchCorner.CornerRadius = UDim.new(0, 6)

-- Controle
local lockInEnabled = false
local lockedTarget = nil

toggleSwitch.MouseButton1Click:Connect(function()
	lockInEnabled = not lockInEnabled
	toggleSwitch.Text = lockInEnabled and "ON" or "OFF"
	toggleSwitch.BackgroundColor3 = lockInEnabled and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(60, 60, 60)

	if lockInEnabled then
		-- Seleciona alvo ao ativar
		lockedTarget = nil

		local closest, shortestDist = nil, math.huge

		for _, otherPlayer in pairs(Players:GetPlayers()) do
			if otherPlayer ~= player and otherPlayer.Character and otherPlayer.Character:FindFirstChild("Torso") then
				local pos, onScreen = camera:WorldToViewportPoint(otherPlayer.Character.Torso.Position)
				if onScreen then
					local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)).Magnitude
					if dist < shortestDist then
						shortestDist = dist
						closest = otherPlayer.Character.Torso
					end
				end
			end
		end

		lockedTarget = closest
	else
		lockedTarget = nil
	end
end)

toggleButton.MouseButton1Click:Connect(function()
	menu.Visible = not menu.Visible
end)

-- Mira fixa no alvo selecionado
RunService.RenderStepped:Connect(function()
	if lockInEnabled and lockedTarget then
		camera.CFrame = CFrame.new(camera.CFrame.Position, lockedTarget.Position + Vector3.new(0, 1, 0))
	end
end)
