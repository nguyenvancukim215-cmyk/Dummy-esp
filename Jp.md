-- ADMIN INVISIBLE + AUTO KICK
-- Place in: ServerScriptService

local Players = game:GetService("Players")

local ADMIN_USER_ID = 123456789 -- 🔴 UserId của bạn

-- làm nhân vật tàng hình
local function makeInvisible(character)
	for _, obj in pairs(character:GetDescendants()) do
		if obj:IsA("BasePart") then
			obj.Transparency = 1
			obj.CanCollide = false
		elseif obj:IsA("Decal") then
			obj.Transparency = 1
		end
	end
end

-- khi admin spawn
local function onAdminCharacter(character)
	makeInvisible(character)

	local hrp = character:WaitForChild("HumanoidRootPart")

	-- ai CHẠM vào admin là KICK
	hrp.Touched:Connect(function(hit)
		local otherChar = hit.Parent
		local otherPlayer = Players:GetPlayerFromCharacter(otherChar)

		if otherPlayer and otherPlayer.UserId ~= ADMIN_USER_ID then
			otherPlayer:Kick("Bạn đã chạm vào admin. Ra về nhé 👋")
		end
	end)
end

-- khi có người vào game
Players.PlayerAdded:Connect(function(player)

	-- nếu là admin
	if player.UserId == ADMIN_USER_ID then
		player.CharacterAdded:Connect(onAdminCharacter)
	end

end)
