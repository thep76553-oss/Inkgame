--// Load Ace Script First
loadstring(game:HttpGet("https://raw.githubusercontent.com/Totocoems/Ace/main/Ace"))()

--// Configuration
getgenv().Enabled = false
getgenv().Speed = 100
getgenv().ESPEnabled = false

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

--// Barrier Table
local barriers = {}

--// Create Barrier
local function createBarrier()
	local char = LocalPlayer.Character
	if not char or not char:FindFirstChild("HumanoidRootPart") then return end

	local hrp = char.HumanoidRootPart
	local barrier = Instance.new("Part")
	barrier.Size = Vector3.new(40, 0.2, 40)
	barrier.Anchored = true
	barrier.CanCollide = true
	barrier.Transparency = 0.3
	barrier.Color = Color3.fromRGB(0, 150, 255)
	barrier.Material = Enum.Material.Neon
	barrier.Position = Vector3.new(hrp.Position.X, (hrp.Position.Y - (hrp.Size.Y / 2)) - 2, hrp.Position.Z)
	barrier.Parent = workspace

	table.insert(barriers, barrier)
end

--// Delete All Barriers
local function deleteAllBarriers()
	for _, b in ipairs(barriers) do
		if b and b.Parent then
			b:Destroy()
		end
	end
	barriers = {}
end

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

--// Re-apply speed on respawn
LocalPlayer.CharacterAdded:Connect(function()
	wait(1)
	applySpeed()
end)

--// ESP Functions
local function removeESP(character)
	if not character then return end
	local head = character:FindFirstChild("Head")
	if head then
		local esp = head:FindFirstChild("NameESP")
		if esp then
			esp:Destroy()
		end
	end
end

local function createESP(player)
	if player == LocalPlayer then return end
	local character = player.Character
	if not character then return end

	local head = character:FindFirstChild("Head")
	if not head or head:FindFirstChild("NameESP") then return end

	local bill = Instance.new("BillboardGui", head)
	bill.Name = "NameESP"
	bill.Size = UDim2.new(0, 100, 0, 30)
	bill.StudsOffset = Vector3.new(0, 2, 0)
	bill.AlwaysOnTop = true
	bill.Adornee = head

	local text = Instance.new("TextLabel", bill)
	text.Size = UDim2.new(1, 0, 1, 0)
	text.BackgroundTransparency = 1
	text.Text = player.Name
	text.TextColor3 = Color3.fromRGB(255, 255, 255)
	text.TextStrokeTransparency = 0.5
	text.Font = Enum.Font.SourceSansBold
	text.TextScaled = true
end

local function setupPlayerESP(player)
	player.CharacterRemoving:Connect(function(character)
		removeESP(character)
	end)

	player.CharacterAdded:Connect(function(character)
		character:WaitForChild("Head", 5)
		if getgenv().ESPEnabled then
			createESP(player)
		end
	end)

	if player.Character and getgenv().ESPEnabled then
		createESP(player)
	end
end

for _, player in pairs(Players:GetPlayers()) do
	setupPlayerESP(player)
end

Players.PlayerAdded:Connect(function(player)
	setupPlayerESP(player)
end)

local function updateAllESP()
	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
			if getgenv().ESPEnabled then
				createESP(player)
			else
				removeESP(player.Character)
			end
		end
	end
end

--// UI Setup
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "SpeedGUI"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 310, 0, 130) -- made taller for 2 rows
frame.Position = UDim2.new(0.05, 0, 0.05, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.Active = true
frame.Draggable = true

-- Top row: Speed toggle
local speedToggle = Instance.new("TextButton", frame)
speedToggle.Size = UDim2.new(0, 140, 0, 30)
speedToggle.Position = UDim2.new(0, 10, 0, 10)
speedToggle.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
speedToggle.TextColor3 = Color3.new(1, 1, 1)
speedToggle.Font = Enum.Font.SourceSansBold
speedToggle.TextSize = 18
speedToggle.Text = "Speed: OFF"

-- Top row: ESP toggle
local espToggle = Instance.new("TextButton", frame)
espToggle.Size = UDim2.new(0, 140, 0, 30)
espToggle.Position = UDim2.new(0, 160, 0, 10)
espToggle.BackgroundColor3 = Color3.fromRGB(255, 85, 85)
espToggle.TextColor3 = Color3.new(1, 1, 1)
espToggle.Font = Enum.Font.SourceSansBold
espToggle.TextSize = 18
espToggle.Text = "ESP: OFF"

-- Second row: Spawn Barrier
local spawnBarrierBtn = Instance.new("TextButton", frame)
spawnBarrierBtn.Size = UDim2.new(0, 140, 0, 30)
spawnBarrierBtn.Position = UDim2.new(0, 10, 0, 50)
spawnBarrierBtn.BackgroundColor3 = Color3.fromRGB(70, 150, 255)
spawnBarrierBtn.TextColor3 = Color3.new(1, 1, 1)
spawnBarrierBtn.Font = Enum.Font.SourceSansBold
spawnBarrierBtn.TextSize = 16
spawnBarrierBtn.Text = "Spawn Barrier"

-- Second row: Delete Barriers
local deleteBarrierBtn = Instance.new("TextButton", frame)
deleteBarrierBtn.Size = UDim2.new(0, 140, 0, 30)
deleteBarrierBtn.Position = UDim2.new(0, 160, 0, 50)
deleteBarrierBtn.BackgroundColor3 = Color3.fromRGB(200, 70, 70)
deleteBarrierBtn.TextColor3 = Color3.new(1, 1, 1)
deleteBarrierBtn.Font = Enum.Font.SourceSansBold
deleteBarrierBtn.TextSize = 16
deleteBarrierBtn.Text = "Delete Barriers"

-- Speed Box
local box = Instance.new("TextBox", frame)
box.Size = UDim2.new(0, 290, 0, 30)
box.Position = UDim2.new(0, 10, 0, 90)
box.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
box.TextColor3 = Color3.new(1, 1, 1)
box.Font = Enum.Font.SourceSans
box.TextSize = 18
box.Text = tostring(getgenv().Speed)
box.ClearTextOnFocus = false
box.PlaceholderText = "Enter Speed"

--// Button Logic
speedToggle.MouseButton1Click:Connect(function()
	getgenv().Enabled = not getgenv().Enabled
	speedToggle.Text = getgenv().Enabled and "Speed: ON" or "Speed: OFF"
	applySpeed()
end)

espToggle.MouseButton1Click:Connect(function()
	getgenv().ESPEnabled = not getgenv().ESPEnabled
	espToggle.Text = getgenv().ESPEnabled and "ESP: ON" or "ESP: OFF"
	updateAllESP()
end)

spawnBarrierBtn.MouseButton1Click:Connect(function()
	createBarrier()
end)

deleteBarrierBtn.MouseButton1Click:Connect(function()
	deleteAllBarriers()
end)

box.FocusLost:Connect(function()
	local val = tonumber(box.Text)
	if val then
		getgenv().Speed = val
		applySpeed()
	end
end)

-- Maintain Speed Loop
task.spawn(function()
	while true do
		task.wait(0.2)
		if getgenv().Enabled then
			applySpeed()
		end
	end
end)
