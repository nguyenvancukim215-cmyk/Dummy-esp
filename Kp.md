-- AdminKickMenu (MOBILE)
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer
local kickEvent = ReplicatedStorage:WaitForChild("KickEvent")

-- 🔴 chỉ hiện menu cho owner
local OWNER_USER_ID = 123456789
if player.UserId ~= OWNER_USER_ID then return end

-- UI
local gui = Instance.new("ScreenGui", player.PlayerGui)

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 350)
frame.Position = UDim2.new(0.05, 0, 0.2, 0)
frame.BackgroundColor3 = Color3.fromRGB(25,25,25)
frame.BorderSizePixel = 0

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,40)
title.Text = "ADMIN MENU"
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundTransparency = 1
title.TextScaled = true

-- player list
local listFrame = Instance.new("ScrollingFrame", frame)
listFrame.Position = UDim2.new(0,10,0,50)
listFrame.Size = UDim2.new(1,-20,1,-120)
listFrame.CanvasSize = UDim2.new(0,0,0,0)
listFrame.ScrollBarImageTransparency = 0.2

local uiList = Instance.new("UIListLayout", listFrame)
uiList.Padding = UDim.new(0,6)

local selectedUserId = nil

local function refreshList()
	listFrame:ClearAllChildren()
	uiList.Parent = listFrame
	selectedUserId = nil

	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= player then
			local btn = Instance.new("TextButton")
			btn.Size = UDim2.new(1,0,0,40)
			btn.Text = plr.Name
			btn.TextScaled = true
			btn.BackgroundColor3 = Color3.fromRGB(50,50,50)
			btn.TextColor3 = Color3.new(1,1,1)
			btn.Parent = listFrame

			btn.MouseButton1Click:Connect(function()
				selectedUserId = plr.UserId
				btn.BackgroundColor3 = Color3.fromRGB(120,60,60)
			end)
		end
	end

	task.wait()
	listFrame.CanvasSize = UDim2.new(0,0,0,uiList.AbsoluteContentSize.Y + 10)
end

refreshList()
Players.PlayerAdded:Connect(refreshList)
Players.PlayerRemoving:Connect(refreshList)

-- KICK BUTTON (NGUY HIỂM)
local kickBtn = Instance.new("TextButton", frame)
kickBtn.Size = UDim2.new(1,-20,0,50)
kickBtn.Position = UDim2.new(0,10,1,-60)
kickBtn.Text = "KICK PLAYER"
kickBtn.TextScaled = true
kickBtn.BackgroundColor3 = Color3.fromRGB(150,40,40)
kickBtn.TextColor3 = Color3.new(1,1,1)

kickBtn.MouseButton1Click:Connect(function()
	if selectedUserId then
		kickEvent:FireServer(selectedUserId)
	end
end)
