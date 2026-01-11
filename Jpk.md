		-- ServerScriptService/AdminAllInOne

local Players = game:GetService("Players")

-- 🔐 USER ID ADMIN (ĐỔI THÀNH CỦA BẠN)
local ADMINS = {
	[123456789] = true
}

local function isAdmin(player)
	return ADMINS[player.UserId] == true
end

-- 🎨 TẠO GUI ADMIN
local function createGui(player)
	local gui = Instance.new("ScreenGui")
	gui.Name = "AdminGui"
	gui.ResetOnSpawn = false
	gui.Parent = player:WaitForChild("PlayerGui")

	local frame = Instance.new("Frame", gui)
	frame.Size = UDim2.fromScale(0.3,0.4)
	frame.Position = UDim2.fromScale(0.35,0.3)
	frame.BackgroundColor3 = Color3.fromRGB(25,25,25)
	frame.Active = true
	frame.Draggable = true

	local title = Instance.new("TextLabel", frame)
	title.Size = UDim2.fromScale(1,0.15)
	title.Text = "ADMIN PANEL"
	title.TextColor3 = Color3.new(1,1,1)
	title.BackgroundTransparency = 1

	local box = Instance.new("TextBox", frame)
	box.PlaceholderText = "Tên người chơi"
	box.Size = UDim2.fromScale(0.9,0.15)
	box.Position = UDim2.fromScale(0.05,0.2)

	local function makeButton(text, y, callback)
		local btn = Instance.new("TextButton", frame)
		btn.Size = UDim2.fromScale(0.4,0.12)
		btn.Position = UDim2.fromScale(0.05,y)
		btn.Text = text
		btn.MouseButton1Click:Connect(callback)
	end

	makeButton("Kill", 0.4, function()
		local target = Players:FindFirstChild(box.Text)
		if target and target.Character then
			target.Character.Humanoid.Health = 0
		end
	end)

	makeButton("Heal", 0.55, function()
		local target = Players:FindFirstChild(box.Text)
		if target and target.Character then
			target.Character.Humanoid.Health =
				target.Character.Humanoid.MaxHealth
		end
	end)

	makeButton("Kick", 0.7, function()
		local target = Players:FindFirstChild(box.Text)
		if target then
			target:Kick("Bị admin kick")
		end
	end)

	makeButton("Bring", 0.4, function()
		local target = Players:FindFirstChild(box.Text)
		if target and target.Character and player.Character then
			target.Character.HumanoidRootPart.CFrame =
				player.Character.HumanoidRootPart.CFrame * CFrame.new(0,0,-3)
		end
	end)

	makeButton("Goto", 0.55, function()
		local target = Players:FindFirstChild(box.Text)
		if target and target.Character and player.Character then
			player.Character.HumanoidRootPart.CFrame =
				target.Character.HumanoidRootPart.CFrame * CFrame.new(0,0,-3)
		end
	end)
end

-- 🚪 KHI NGƯỜI CHƠI VÀO GAME
Players.PlayerAdded:Connect(function(player)
	if isAdmin(player) then
		player.CharacterAdded:Wait()
		createGui(player)
	end
end)
