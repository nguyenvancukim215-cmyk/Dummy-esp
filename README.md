--// Dummy ESP - Learning Purpose Only
--// LocalScript

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- CONFIG
local DUMMY_NAME = "Dummy" -- đổi nếu dummy tên khác
local ESP_COLOR = Color3.fromRGB(255, 80, 80)

-- Lưu ESP đã tạo
local espCache = {}

-- Tạo Highlight ESP
local function createESP(model)
	if espCache[model] then return end

	local highlight = Instance.new("Highlight")
	highlight.Name = "DummyESP"
	highlight.FillColor = ESP_COLOR
	highlight.OutlineColor = Color3.new(1,1,1)
	highlight.FillTransparency = 0.6
	highlight.OutlineTransparency = 0
	highlight.Adornee = model
	highlight.Parent = model

	-- Billboard hiển thị tên + khoảng cách
	local billboard = Instance.new("BillboardGui")
	billboard.Name = "Info"
	billboard.Size = UDim2.new(0, 200, 0, 50)
	billboard.StudsOffset = Vector3.new(0, 3, 0)
	billboard.AlwaysOnTop = true
	billboard.Parent = model:WaitForChild("HumanoidRootPart")

	local text = Instance.new("TextLabel")
	text.Size = UDim2.new(1, 0, 1, 0)
	text.BackgroundTransparency = 1
	text.TextColor3 = ESP_COLOR
	text.TextStrokeTransparency = 0
	text.TextScaled = true
	text.Font = Enum.Font.GothamBold
	text.Parent = billboard

	espCache[model] = {
		highlight = highlight,
		text = text
	}
end

-- Cập nhật ESP mỗi frame
RunService.RenderStepped:Connect(function()
	local character = player.Character
	if not character or not character:FindFirstChild("HumanoidRootPart") then return end

	local root = character.HumanoidRootPart.Position

	for _, obj in pairs(workspace:GetChildren()) do
		if obj:IsA("Model")
			and obj.Name == DUMMY_NAME
			and obj:FindFirstChild("Humanoid")
			and obj:FindFirstChild("HumanoidRootPart") then

			createESP(obj)

			local distance = (root - obj.HumanoidRootPart.Position).Magnitude
			local esp = espCache[obj]
			if esp then
				esp.text.Text = obj.Name .. " | " .. math.floor(distance) .. "m"
			end
		end
	end
end)
