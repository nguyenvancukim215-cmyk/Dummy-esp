-- SMOOTH CAMLOCK PLAYER (MOBILE FIX)
-- FOR PRIVATE MAP / TEST ONLY

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- SETTINGS
local MAX_DISTANCE = 120
local CAM_SMOOTH = 0.12     -- camera mượt
local ROTATE_SMOOTH = 0.18  -- xoay nhân vật mượt

local lockedTarget = nil
local isLocked = false

-- Character
local function getCharacter()
	return player.Character or player.CharacterAdded:Wait()
end

-- Find nearest player
local function getNearestPlayer()
	local char = getCharacter()
	local hrp = char:WaitForChild("HumanoidRootPart")

	local nearest, shortest = nil, MAX_DISTANCE

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

-- UI Button
local gui = Instance.new("ScreenGui")
gui.Parent = player:WaitForChild("PlayerGui")

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 150, 0, 50)
button.Position = UDim2.new(0.7, 0, 0.75, 0)
button.Text = "CAMLOCK : OFF"
button.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
button.TextColor3 = Color3.new(1,1,1)
button.TextScaled = true
button.Parent = gui

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

-- MAIN LOOP
RunService.RenderStepped:Connect(function(dt)
	if not isLocked or not lockedTarget then return end

	if not lockedTarget.Parent then
		isLocked = false
		return
	end

	local char = getCharacter()
	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end

	-- distance check
	if (lockedTarget.Position - hrp.Position).Magnitude > MAX_DISTANCE then
		isLocked = false
		lockedTarget = nil
		button.Text = "CAMLOCK : OFF"
		return
	end

	-- 🔹 XOAY NHÂN VẬT THEO TARGET
	local targetPos = Vector3.new(
		lockedTarget.Position.X,
		hrp.Position.Y,
		lockedTarget.Position.Z
	)

	local desiredHRP = CFrame.new(hrp.Position, targetPos)
	hrp.CFrame = hrp.CFrame:Lerp(desiredHRP, ROTATE_SMOOTH)

	-- 🔹 CAMERA THEO SAU NHÂN VẬT + TARGET
	local camPos = camera.CFrame.Position
	local lookPos = lockedTarget.Position + Vector3.new(0, 1.5, 0)
	local desiredCam = CFrame.new(camPos, lookPos)

	camera.CFrame = camera.CFrame:Lerp(desiredCam, CAM_SMOOTH)
end)
