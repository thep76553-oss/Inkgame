local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

getgenv().Enabled = false
getgenv().Speed = 100
getgenv().executed = false

local function bypassWalkSpeed()
    if getgenv().executed then
        if not getgenv().Enabled then return end
    else
        getgenv().executed = true

        local mt = getrawmetatable(game)
        setreadonly(mt, false)

        local oldindex = mt.__index
        mt.__index = newcclosure(function(self, b)
            if b == "WalkSpeed" then
                return 16
            end
            return oldindex(self, b)
        end)
    end
end

bypassWalkSpeed()

local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local ScreenGui = Instance.new("ScreenGui", PlayerGui)
ScreenGui.Name = "SpeedGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 100)
Frame.Position = UDim2.new(0.1, 0, 0.1, 0)
Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0, 180, 0, 30)
ToggleButton.Position = UDim2.new(0, 10, 0, 10)
ToggleButton.BackgroundColor3 = Color3.fromRGB(80, 130, 180)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Text = "Speed: OFF"
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 18
ToggleButton.Parent = Frame

local SpeedBox = Instance.new("TextBox")
SpeedBox.Size = UDim2.new(0, 180, 0, 30)
SpeedBox.Position = UDim2.new(0, 10, 0, 50)
SpeedBox.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
SpeedBox.TextColor3 = Color3.new(1, 1, 1)
SpeedBox.Text = tostring(getgenv().Speed)
SpeedBox.PlaceholderText = "Enter Speed"
SpeedBox.Font = Enum.Font.SourceSans
SpeedBox.TextSize = 18
SpeedBox.ClearTextOnFocus = false
SpeedBox.Parent = Frame

SpeedBox.FocusLost:Connect(function()
	local speed = tonumber(SpeedBox.Text)
	if speed then
		getgenv().Speed = speed
	end
end)

ToggleButton.MouseButton1Click:Connect(function()
	getgenv().Enabled = not getgenv().Enabled
	ToggleButton.Text = getgenv().Enabled and "Speed: ON" or "Speed: OFF"
	local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
	if hum and getgenv().Enabled then
		hum.WalkSpeed = getgenv().Speed
	elseif hum then
		hum.WalkSpeed = 16
	end
end)

LocalPlayer.CharacterAdded:Connect(function(char)
	bypassWalkSpeed()
	char:WaitForChild("Humanoid").WalkSpeed = getgenv().Enabled and getgenv().Speed or 16
end)

task.spawn(function()
	while true do
		task.wait(0.2)
		if getgenv().Enabled then
			local char = LocalPlayer.Character
			if char then
				local hum = char:FindFirstChild("Humanoid")
				if hum then
					hum.WalkSpeed = getgenv().Speed
				end
			end
		end
	end
end)
