--// Configuration
getgenv().Enabled = false
getgenv().Speed = 100

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

--// Apply speed function
local function applySpeed()
    local character = LocalPlayer.Character
    if character then
        local humanoid = character:FindFirstChildWhichIsA("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = getgenv().Enabled and getgenv().Speed or 16
        end
    end
end

--// Re-apply on respawn
LocalPlayer.CharacterAdded:Connect(function()
    wait(1)
    applySpeed()
end)

--// UI Setup
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "SpeedGUI"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 100)
frame.Position = UDim2.new(0.05, 0, 0.05, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.Active = true
frame.Draggable = true

local toggle = Instance.new("TextButton", frame)
toggle.Size = UDim2.new(0, 180, 0, 30)
toggle.Position = UDim2.new(0, 10, 0, 10)
toggle.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
toggle.TextColor3 = Color3.new(1, 1, 1)
toggle.Font = Enum.Font.SourceSansBold
toggle.TextSize = 18
toggle.Text = "Speed: OFF"

local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0, 180, 0, 30)
box.Position = UDim2.new(0, 10, 0, 50)
box.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
box.TextColor3 = Color3.new(1, 1, 1)
box.Font = Enum.Font.SourceSans
box.TextSize = 18
box.Text = tostring(getgenv().Speed)
box.ClearTextOnFocus = false
box.PlaceholderText = "Enter Speed"

--// Toggle Button Clicked
toggle.MouseButton1Click:Connect(function()
	getgenv().Enabled = not getgenv().Enabled
	toggle.Text = getgenv().Enabled and "Speed: ON" or "Speed: OFF"
	applySpeed()
end)

--// Speed Changed
box.FocusLost:Connect(function()
	local val = tonumber(box.Text)
	if val then
		getgenv().Speed = val
		applySpeed()
	end
end)

--// Loop to maintain speed
task.spawn(function()
	while true do
		task.wait(0.2)
		if getgenv().Enabled then
			applySpeed()
		end
	end
end)
