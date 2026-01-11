-- CAMLOCK PLAYER (MOBILE FRIENDLY)
-- FOR PRIVATE MAP / TEST ONLY

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- SETTINGS
local MAX_DISTANCE = 100
local SMOOTHNESS = 0.2

local lockedTarget = nil
local isLocked = false

-- Character
local function getCharacter()
	return player.Character or player.CharacterAdded:Wait()
end

-- Find nearest player
local function getNearestPlayer()
	local character = getCharacter()
	local hrp = character:WaitForChild("HumanoidRootPart")

	local nearest = nil
	local shortest = MAX_DISTANCE

	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= player and plr.Character then
			local root = plr.Character:FindFirstChild("HumanoidRootPart")
			local hum = plr.Character:FindFirstChild("Humanoid")

			if root and hum and hum.Health > 0 then
				local dist = (root.Position - hrp.Position).Magnitude
				if dist < shortest then
					shortest = dist
					nearest = root
				end
			end
		end
	end

	return nearest
end

-- UI BUTTON
local gui = Instance.new("ScreenGui")
gui.Parent = player:WaitForChild("PlayerGui")

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 140, 0, 50)
button.Position = UDim2.new(0.7, 0, 0.75, 0)
button.Text = "CAMLOCK : OFF"
button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
button.TextColor3 = Color3.fromRGB(255, 255, 255)
button.TextScaled = true
button.Parent = gui

-- Toggle camlock
button.MouseButton1Click:Connect(function()
	if isLocked then
		isLocked = false
		lockedTarget = nil
		button.Text = "CAMLOCK : OFF"
	else
		lockedTarget = getNearestPlayer()
		if lockedTarget then
			isLocked = true
			button.Text = "CAMLOCK : ON"
		end
	end
end)

-- Camera follow
RunService.RenderStepped:Connect(function()
	if isLocked and lockedTarget then
		if not lockedTarget.Parent then
			isLocked = false
			return
		end

		local camPos = camera.CFrame.Position
		local lookCFrame = CFrame.new(camPos, lockedTarget.Position)

		camera.CFrame = camera.CFrame:Lerp(lookCFrame, SMOOTHNESS)
	end
end)
